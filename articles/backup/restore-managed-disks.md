---
title: Ripristinare Azure Managed Disks
description: Informazioni su come ripristinare azure Managed Disks dalla portale di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517199"
---
# <a name="restore-azure-managed-disks"></a>Ripristinare Azure Managed Disks

Questo articolo illustra come ripristinare azure [Managed Disks](../virtual-machines/managed-disks-overview.md) da un punto di ripristino creato da Backup di Azure.

Attualmente, l'opzione Original-Location recovery (OLR) di ripristino sostituendo il disco di origine esistente da cui sono stati evasi i backup non è supportata. È possibile eseguire il ripristino da un punto di ripristino per creare un nuovo disco nello stesso gruppo di risorse del disco di origine da cui sono stati evasi i backup o in qualsiasi altro gruppo di risorse. Questa operazione è nota come Alternate-Location ripristino automatico (ALR) e consente di mantenere sia il disco di origine che il disco ripristinato (nuovo).

In questo articolo si apprenderà come:

- Eseguire il ripristino per creare un nuovo disco

- Tenere traccia dello stato dell'operazione di ripristino

## <a name="restore-to-create-a-new-disk"></a>Eseguire il ripristino per creare un nuovo disco

Backup Vault usa l'identità gestita per accedere ad altre risorse di Azure. Per eseguire il ripristino dal backup, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni per il gruppo di risorse in cui deve essere ripristinato il disco.

L'insieme di credenziali di backup usa un'identità gestita assegnata dal sistema, limitata a una per ogni risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure. L'identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite.](../active-directory/managed-identities-azure-resources/overview.md)

Per eseguire un'operazione di ripristino, sono necessari i prerequisiti seguenti:

1. Assegnare **il ruolo Operatore** ripristino disco all'identità gestita dell'insieme di credenziali di backup nel gruppo di risorse in cui il disco verrà ripristinato dal Backup di Azure locale.

    >[!NOTE]
    > È possibile scegliere lo stesso gruppo di risorse del disco di origine da cui vengono evasi i backup o qualsiasi altro gruppo di risorse all'interno della stessa sottoscrizione o di un'altra sottoscrizione.

    1. Passare al gruppo di risorse in cui deve essere ripristinato il disco. Ad esempio, il gruppo di risorse è *TargetRG.*

    1. Passare a **Controllo di accesso (IAM)** e selezionare **Aggiungi assegnazioni di ruolo**

    1. Nel riquadro di contesto a destra selezionare **Disk Restore Operator (Operatore ripristino disco)** nell'elenco **a discesa Role (Ruolo).** Selezionare l'identità gestita dell'insieme di credenziali di backup e **salvare**.

        >[!TIP]
        >Digitare il nome dell'insieme di credenziali di backup per selezionare l'identità gestita dell'insieme di credenziali.

        ![Selezionare il ruolo dell'operatore di ripristino del disco](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Verificare che l'identità gestita dell'insieme di credenziali di backup abbia il set di assegnazioni di ruolo nel gruppo di risorse in cui verrà ripristinato il disco.

    1. Passare a **Insieme di credenziali di backup - > identità** e selezionare **Assegnazioni di ruolo di Azure**

        ![Selezionare Assegnazioni di ruolo di Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Verificare che il ruolo, il nome della risorsa e il tipo di risorsa siano visualizzati correttamente.

        ![Verificare il ruolo, il nome della risorsa e il tipo di risorsa](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Anche se le assegnazioni di ruolo vengono riflesse correttamente nel portale, l'applicazione dell'autorizzazione all'identità gestita dell'insieme di credenziali di backup può richiedere circa 15 minuti.
    >
    >Durante i backup pianificati o un'operazione di backup su richiesta, Backup di Azure archivia gli snapshot incrementali del disco nel gruppo di risorse snapshot fornito durante la configurazione del backup del disco. Backup di Azure questi snapshot incrementali durante l'operazione di ripristino. Se gli snapshot vengono eliminati o spostati dal gruppo di risorse snapshot o se le assegnazioni di ruolo dell'insieme di credenziali di backup vengono revocate nel gruppo di risorse snapshot, l'operazione di ripristino avrà esito negativo.

1. Se il disco da ripristinare è crittografato con chiavi gestite dal cliente [(CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) o usando la  crittografia doppia usando chiavi gestite dalla piattaforma  e chiavi gestite dal cliente, assegnare l'autorizzazione del ruolo Lettore all'identità gestita dell'insieme di credenziali di backup nella risorsa set di crittografia del [disco.](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)

Dopo aver soddisfatto i prerequisiti, seguire questa procedura per eseguire l'operazione di ripristino.

1. Nel riquadro [portale di Azure](https://portal.azure.com/)passare a **Centro backup**. Selezionare **Istanze di** backup nella **sezione** Gestisci. Nell'elenco delle istanze di backup selezionare l'istanza di backup su disco per cui si vuole eseguire l'operazione di ripristino.

    ![Elenco di istanze di backup](./media/restore-managed-disks/backup-instances.png)

    In alternativa, è possibile eseguire questa operazione dall'insieme di credenziali di backup usato per configurare il backup per il disco.

1. Nella schermata **Istanza di** backup selezionare il punto di ripristino da usare per eseguire l'operazione di ripristino e selezionare **Ripristina**.

    ![Seleziona punto di ripristino](./media/restore-managed-disks/select-restore-point.png)

1. Nel flusso **di lavoro** Ripristino esaminare le **informazioni di** base e **selezionare** le informazioni sulla scheda Informazioni di base e Seleziona punto di ripristino e selezionare **Avanti: Parametri di ripristino**.

    ![Esaminare Informazioni di base e Selezionare le informazioni sul punto di ripristino](./media/restore-managed-disks/review-information.png)

1. Nella scheda **Parametri di ripristino** selezionare la sottoscrizione **di** destinazione e il gruppo di risorse **di destinazione** in cui si vuole ripristinare il backup. Specificare il nome del disco da ripristinare. Selezionare **Avanti: Rivedi e ripristina**.

    ![Parametri di ripristino](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >È anche possibile eseguire il backup dei dischi di cui Backup di Azure usando la soluzione Backup su disco tramite Backup di Azure usando la soluzione di backup di macchine virtuali di Azure con l'insieme di credenziali di Servizi di ripristino. Se è stata configurata la protezione della macchina virtuale di Azure a cui è collegato questo disco, è anche possibile usare l'operazione di ripristino della macchina virtuale di Azure. È possibile scegliere di ripristinare la macchina virtuale o dischi e file o cartelle dal punto di ripristino dell'istanza di backup della macchina virtuale di Azure corrispondente. Per altre informazioni, vedere Backup [di macchine virtuali di Azure.](./about-azure-vm-restore.md)

1. Al termine della convalida, selezionare Ripristina **per** avviare l'operazione di ripristino.

    ![Avviare l'operazione di ripristino](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Il completamento della convalida potrebbe richiedere alcuni minuti prima di poter attivare l'operazione di ripristino. La convalida potrebbe non riuscire se:
    >
    > - Un disco con lo stesso nome specificato in **Nome disco ripristinato** esiste già nel gruppo **di risorse di destinazione**
    > - L'identità gestita dell'insieme di credenziali di backup non ha assegnazioni di ruolo valide nel gruppo **di risorse di destinazione**
    > - Le assegnazioni di ruolo dell'identità gestita dell'insieme di credenziali di backup vengono revocate nel gruppo di risorse **Snapshot** in cui sono archiviati gli snapshot incrementali
    > - Se gli snapshot incrementali vengono eliminati o spostati dal gruppo di risorse snapshot

Il ripristino creerà un nuovo disco dal punto di ripristino selezionato nel gruppo di risorse di destinazione fornito durante l'operazione di ripristino. Per usare il disco ripristinato in una macchina virtuale esistente, è necessario eseguire altri passaggi:

- Se il disco ripristinato è un disco dati, è possibile collegare un disco esistente a una macchina virtuale. Se il disco ripristinato è un disco del sistema operativo, è possibile scambiare  il disco del sistema operativo di una macchina virtuale dal portale di Azure nel **riquadro** Macchina virtuale - menu Dischi > nella sezione **Impostazioni.**

    ![Scambiare i dischi del sistema operativo](./media/restore-managed-disks/swap-os-disks.png)

- Per le macchine virtuali Windows, se il disco ripristinato è un disco dati, seguire le istruzioni per scollegare il [disco](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) dati originale dalla macchina virtuale. Collegare [quindi il disco ripristinato](../virtual-machines/windows/attach-managed-disk-portal.md) alla macchina virtuale. Seguire le istruzioni per [scambiare il disco del sistema operativo](../virtual-machines/windows/os-disk-swap.md) della macchina virtuale con il disco ripristinato.

- Per le macchine virtuali Linux, se il disco ripristinato è un disco dati, seguire le istruzioni per scollegare il [disco](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) dati originale dalla macchina virtuale. Collegare [quindi il disco ripristinato](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) alla macchina virtuale. Seguire le istruzioni per [scambiare il disco del sistema operativo](../virtual-machines/linux/os-disk-swap.md) della macchina virtuale con il disco ripristinato.

È consigliabile revocare l'assegnazione del ruolo **Operatore** ripristino disco  all'identità gestita dell'insieme di credenziali di backup nel gruppo di risorse di destinazione al termine dell'operazione di ripristino.

## <a name="track-a-restore-operation"></a>Tenere traccia di un'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Per visualizzare lo stato del processo di ripristino:

1. Passare alla schermata **Istanza di** backup. Mostra il dashboard dei processi con l'operazione e lo stato degli ultimi sette giorni.

    ![Dashboard processi](./media/restore-managed-disks/jobs-dashboard.png)

1. Per visualizzare lo stato dell'operazione di ripristino, selezionare **Visualizza tutto** per visualizzare i processi in corso e precedenti di questa istanza di backup.

    ![Selezionare Visualizza tutto](./media/restore-managed-disks/view-all.png)

1. Esaminare l'elenco dei processi di backup e ripristino e il relativo stato. Selezionare un processo dall'elenco di processi per visualizzare i dettagli del processo.

    ![Elenco di processi](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Backup su disco di Azure](disk-backup-faq.yml)