---
title: Ripristinare Managed Disks di Azure
description: Informazioni su come ripristinare Managed Disks di Azure dall'portale di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 043a10a7359c95529ff1c4dcc181ea4aba75cb5f
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557835"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Ripristinare Managed Disks di Azure (in anteprima)

>[!IMPORTANT]
>Il backup su disco di Azure è in anteprima senza un contratto di servizio e non è consigliato per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per la disponibilità delle aree, vedere la [matrice di supporto](disk-backup-support-matrix.md).
>
>[Compilare questo modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) per iscriversi all'anteprima.

Questo articolo illustra come ripristinare [Managed Disks di Azure](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) da un punto di ripristino creato da backup di Azure.

Attualmente, l'opzione di ripristino Original-Location (OLR) di ripristino sostituendo il disco di origine esistente da cui sono stati eseguiti i backup non è supportata. È possibile eseguire il ripristino da un punto di ripristino per creare un nuovo disco nello stesso gruppo di risorse del disco di origine da cui sono stati eseguiti i backup o in qualsiasi altro gruppo di risorse. Questa operazione è nota come Alternate-Location Recovery (ALR) e consente di gestire sia il disco di origine sia il disco ripristinato (nuovo).

In questo articolo si apprenderà come:

- Ripristinare per creare un nuovo disco

- Tenere traccia dello stato dell'operazione di ripristino

## <a name="restore-to-create-a-new-disk"></a>Ripristinare per creare un nuovo disco

L'insieme di credenziali di backup usa l'identità gestita per accedere ad altre risorse di Azure. Per eseguire il ripristino da un backup, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni per il gruppo di risorse in cui deve essere ripristinato il disco.

L'insieme di credenziali per il backup usa un'identità gestita assegnata dal sistema, che è limitata a una per risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere le autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Per eseguire un'operazione di ripristino sono necessari i prerequisiti seguenti:

1. Assegnare il ruolo di **operatore ripristino disco** all'identità gestita dell'insieme di credenziali di backup nel gruppo di risorse in cui il disco verrà ripristinato dal servizio backup di Azure.

    >[!NOTE]
    > È possibile scegliere lo stesso gruppo di risorse del disco di origine da cui vengono eseguiti i backup o a qualsiasi altro gruppo di risorse nella stessa sottoscrizione o in una sottoscrizione diversa.

    1. Passare al gruppo di risorse in cui deve essere ripristinato il disco. Ad esempio, il gruppo di risorse è *TargetRG*.

    1. Passare a **controllo di accesso (IAM)** e selezionare **Aggiungi assegnazioni di ruolo**

    1. Nel riquadro del contesto destro selezionare **operatore ripristino disco** nell'elenco a discesa **ruolo** . Selezionare l'identità gestita dell'insieme di credenziali per il backup e **salvarla**.

        >[!TIP]
        >Digitare il nome dell'insieme di credenziali di backup per selezionare l'identità gestita dell'insieme di credenziali.

        ![Selezione ruolo Operatore ripristino disco](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Verificare che l'identità gestita dell'insieme di credenziali di backup includa il set corretto di assegnazioni di ruolo nel gruppo di risorse in cui verrà ripristinato il disco.

    1. Passare a insieme di credenziali di **backup-> identità** e selezionare **assegnazioni di ruolo di Azure**

        ![Selezionare le assegnazioni di ruolo di Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Verificare che il ruolo, il nome della risorsa e il tipo di risorsa siano visualizzati correttamente.

        ![Verificare il ruolo, il nome della risorsa e il tipo di risorsa](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Mentre le assegnazioni di ruolo vengono riflesse correttamente nel portale, potrebbero essere necessari circa 15 minuti per l'applicazione dell'autorizzazione nell'identità gestita dell'insieme di credenziali di backup.
    >
    >Durante i backup pianificati o un'operazione di backup su richiesta, backup di Azure archivia gli snapshot incrementali del disco nel gruppo di risorse snapshot fornito durante la configurazione del backup del disco. Backup di Azure usa questi snapshot incrementali durante l'operazione di ripristino. Se gli snapshot vengono eliminati o spostati dal gruppo di risorse snapshot o se le assegnazioni di ruolo dell'insieme di credenziali di backup vengono revocate nel gruppo di risorse snapshot, l'operazione di ripristino avrà esito negativo.

Una volta soddisfatti i prerequisiti, attenersi alla procedura seguente per eseguire l'operazione di ripristino.

1. Nella [portale di Azure](https://portal.azure.com/)passare a **Backup Center**. Selezionare **istanze di backup** nella sezione **Gestisci** . Nell'elenco delle istanze di backup selezionare l'istanza di backup su disco per cui si desidera eseguire l'operazione di ripristino.

    ![Elenco di istanze di backup](./media/restore-managed-disks/backup-instances.png)

    In alternativa, è possibile eseguire questa operazione dall'insieme di credenziali per il backup usato per configurare il backup per il disco.

1. Nella schermata **istanza di backup** selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino e selezionare **Ripristina**.

    ![Seleziona punto di ripristino](./media/restore-managed-disks/select-restore-point.png)

1. Nel flusso di lavoro di **ripristino** esaminare le **nozioni di base** e selezionare le informazioni sulla scheda punto di **ripristino** e selezionare **Avanti: Ripristina parametri**.

    ![Esaminare le nozioni di base e selezionare le informazioni](./media/restore-managed-disks/review-information.png)

1. Nella scheda **Ripristina parametri** selezionare la sottoscrizione di **destinazione** e il **gruppo di risorse di destinazione** in cui si desidera ripristinare il backup. Consente di specificare il nome del disco da ripristinare. Selezionare **Avanti: Verifica + ripristino**.

    ![Parametri di ripristino](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >I dischi di cui viene eseguito il backup da backup di Azure tramite la soluzione di backup su disco possono anche essere sottoposti a backup da backup di Azure tramite la soluzione di backup delle VM di Azure con l'insieme di credenziali di servizi di ripristino Se è stata configurata la protezione della macchina virtuale di Azure a cui è collegato il disco, è anche possibile usare l'operazione di ripristino della macchina virtuale di Azure. È possibile scegliere di ripristinare la macchina virtuale o i dischi e i file o le cartelle dal punto di ripristino dell'istanza di backup della macchina virtuale di Azure corrispondente. Per altre informazioni, vedere [backup di macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/about-azure-vm-restore).

1. Una volta completata la convalida, selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Avvia operazione di ripristino](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Il completamento della convalida potrebbe richiedere alcuni minuti prima di poter attivare l'operazione di ripristino. La convalida può non riuscire se:
    >
    > - un disco con lo stesso nome specificato nel **nome del disco ripristinato** esiste già nel **gruppo di risorse di destinazione**
    > - l'identità gestita dell'insieme di credenziali di backup non ha assegnazioni di ruolo valide nel **gruppo di risorse di destinazione**
    > - le assegnazioni di ruolo identità gestite dell'insieme di credenziali di backup vengono revocate nel **gruppo di risorse snapshot** in cui sono archiviati gli snapshot incrementali
    > - Se gli snapshot incrementali vengono eliminati o spostati dal gruppo di risorse snapshot

Restore creerà un nuovo disco dal punto di ripristino selezionato nel gruppo di risorse di destinazione specificato durante l'operazione di ripristino. Per usare il disco ripristinato in una macchina virtuale esistente, è necessario eseguire altri passaggi:

- Se il disco ripristinato è un disco dati, è possibile aggiungere un disco esistente a una macchina virtuale. Se il disco ripristinato è un disco del sistema operativo, è possibile scambiare il disco del sistema operativo di una macchina virtuale dalla portale di Azure nel riquadro della **macchina virtuale** -> menu **dischi** nella sezione **Impostazioni** .

    ![Scambia dischi del sistema operativo](./media/restore-managed-disks/swap-os-disks.png)

- Per le macchine virtuali Windows, se il disco ripristinato è un disco dati, seguire le istruzioni per [scollegare il disco dati originale](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) dalla macchina virtuale. Quindi, [alleghi il disco ripristinato](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) alla macchina virtuale. Seguire le istruzioni per [scambiare il disco del sistema operativo](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) della macchina virtuale con il disco ripristinato.

- Per le macchine virtuali Linux, se il disco ripristinato è un disco dati, seguire le istruzioni per [scollegare il disco dati originale](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) dalla macchina virtuale. Quindi, [alleghi il disco ripristinato](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) alla macchina virtuale. Seguire le istruzioni per [scambiare il disco del sistema operativo](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) della macchina virtuale con il disco ripristinato.

È consigliabile revocare l'assegnazione del ruolo dell' **operatore di ripristino del disco** dall'identità gestita dell'insieme di credenziali di backup nel gruppo di risorse di **destinazione** dopo il completamento dell'operazione di ripristino.

## <a name="track-a-restore-operation"></a>Tenere traccia di un'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Per visualizzare lo stato del processo di ripristino:

1. Passare alla schermata dell' **istanza di backup** . Mostra il dashboard dei processi con l'operazione e lo stato degli ultimi sette giorni.

    ![Dashboard dei processi](./media/restore-managed-disks/jobs-dashboard.png)

1. Per visualizzare lo stato dell'operazione di ripristino, selezionare **Visualizza tutto** per visualizzare i processi in corso e passati di questa istanza di backup.

    ![Seleziona Visualizza tutto](./media/restore-managed-disks/view-all.png)

1. Esaminare l'elenco dei processi di backup e ripristino e il relativo stato. Selezionare un processo dall'elenco dei processi per visualizzare i dettagli del processo.

    ![Elenco di processi](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su backup dischi di Azure](disk-backup-faq.md)
