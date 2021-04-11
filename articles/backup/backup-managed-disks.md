---
title: Eseguire il backup di Azure Managed Disks
description: Informazioni su come eseguire il backup di Azure Managed Disks dalla portale di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: e234495eb483d6d0cc6ca556ca418138c61a99f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110628"
---
# <a name="back-up-azure-managed-disks"></a>Eseguire il backup di Azure Managed Disks

Questo articolo illustra come eseguire il backup di [Azure Managed disk](../virtual-machines/managed-disks-overview.md) dal portale di Azure.

In questo articolo si apprenderà come:

- Creare un insieme di credenziali per il backup

- Creare un criterio di backup

- Configurare un backup di un disco di Azure

- Eseguire un processo di backup su richiesta

Per informazioni sulla disponibilità dell'area di backup su disco di Azure, sulle limitazioni e sugli scenari supportati, vedere la [matrice di supporto](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup

Un insieme di credenziali per il backup è un'entità di archiviazione di Azure che include i dati di backup per i vari carichi di lavoro più recenti supportati da backup di Azure, ad esempio database di Azure per i server PostgreSQL e dischi di Azure. Gli insiemi di credenziali di backup consentono di organizzare facilmente i dati di backup, riducendo al minimo il sovraccarico di gestione. Gli insiemi di credenziali di backup si basano sul modello di Azure Resource Manager di Azure, che offre funzionalità avanzate per proteggere i dati di backup.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).
1. Digitare **Centro backup** nella casella di ricerca.
1. In **Servizi** selezionare **centro di backup**.
1. Nella pagina **Centro backup** **selezionare insieme** di credenziali.

   ![Selezionare l'insieme di credenziali in Backup Center](./media/backup-managed-disks/backup-center.png)

1. Nella schermata **Avvia: crea** insieme di credenziali selezionare insieme di credenziali per il **backup** e **continuare**.

   ![Avvio: crea insieme di credenziali](./media/backup-managed-disks/initiate-create-vault.png)

1. Nella scheda **nozioni di base** , specificare la sottoscrizione, il gruppo di risorse, il nome dell'insieme di credenziali di backup, l'area e la ridondanza dell'archiviazione Continuare selezionando **Verifica + crea**. Altre informazioni sulla creazione di un insieme di credenziali per [il backup](./backup-vault-overview.md#create-a-backup-vault).

   ![Esaminare e creare l'insieme di credenziali](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Crea criterio di backup

1. Nell'insieme di credenziali per il **backup** di *DemoVault* creato nel passaggio precedente, passare a **criteri di backup** e selezionare **Aggiungi**.

   ![Aggiungi criteri di backup](./media/backup-managed-disks/backup-policies.png)

1. Nella scheda **nozioni di base** specificare nome criterio e selezionare **tipo di origine dati** come **disco di Azure**. L'insieme di credenziali è già prepopolato e vengono presentate le proprietà dell'insieme di credenziali selezionate.

   >[!NOTE]
   > Anche se l'insieme di credenziali selezionato può avere l'impostazione di ridondanza globale, attualmente backup dischi di Azure supporta solo archivio dati snapshot. Tutti i backup vengono archiviati in un gruppo di risorse nella sottoscrizione e non vengono copiati nell'archivio dell'insieme di credenziali di backup.

   ![Selezionare il tipo di origine dati](./media/backup-managed-disks/datasource-type.png)

1. Nella scheda **criteri di backup** selezionare la frequenza di pianificazione del backup.

   ![Selezione frequenza pianificazione backup](./media/backup-managed-disks/backup-schedule-frequency.png)

   Il backup su disco di Azure offre più backup al giorno. Se sono necessari backup più frequenti, scegliere la frequenza di backup **oraria** con la possibilità di eseguire i backup con intervalli di ogni 4, 6, 8 o 12 ore. I backup sono pianificati in base all'intervallo di **tempo** selezionato. Se, ad esempio, si seleziona **ogni 4 ore**, i backup vengono eseguiti approssimativamente nell'intervallo di ogni 4 ore, in modo che i backup vengano distribuiti equamente durante il giorno. Se è sufficiente un backup di una volta al giorno, scegliere la frequenza di backup **giornaliera** . Nella frequenza di backup giornaliera è possibile specificare l'ora del giorno in cui vengono eseguiti i backup. È importante notare che l'ora del giorno indica l'ora di inizio del backup e non l'ora di completamento del backup. Il tempo necessario per il completamento dell'operazione di backup dipende da diversi fattori, tra cui le dimensioni del disco e la velocità di varianza tra backup consecutivi. Tuttavia, backup su disco di Azure è un backup senza agente che usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md), che non influisca sulle prestazioni dell'applicazione di produzione.

1. Nella scheda **criteri di backup** selezionare impostazioni di conservazione che soddisfino il requisito dell'obiettivo del punto di ripristino (RPO).

   La regola di conservazione predefinita si applica se non è specificata nessun'altra regola di conservazione. La regola di conservazione predefinita può essere modificata per modificare la durata del periodo di memorizzazione, ma non può essere eliminata. È possibile aggiungere una nuova regola di conservazione selezionando **Aggiungi regola di conservazione**.

   ![Aggiungere una regola di conservazione](./media/backup-managed-disks/add-retention-rule.png)

   È possibile selezionare il **primo backup** eseguito ogni giorno o ogni settimana e fornire la durata di conservazione per cui devono essere conservati i backup specifici prima che vengano eliminati. Questa opzione è utile per mantenere backup specifici del giorno o della settimana per un periodo di tempo più lungo. Tutti gli altri backup frequenti possono essere conservati per una durata più breve.

   ![Impostazioni di conservazione](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Backup di Azure per Managed Disks USA snapshot incrementali limitati a 200 snapshot per disco. Per consentire l'esecuzione di backup su richiesta a parte i backup pianificati, i criteri di backup limitano i backup totali a 180. Altre informazioni sugli [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md#restrictions) per il disco gestito.

1. Per completare la creazione dei criteri di backup, selezionare **Verifica + crea**.

## <a name="configure-backup"></a>Configurare il backup

L'insieme di credenziali di backup usa l'identità gestita per accedere ad altre risorse di Azure. Per configurare il backup dei dischi gestiti, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni per i dischi di origine e i gruppi di risorse in cui vengono creati e gestiti gli snapshot.

Un'identità gestita assegnata dal sistema è limitata a una per risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere le autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite](../active-directory/managed-identities-azure-resources/overview.md).

Per configurare il backup dei dischi gestiti sono necessari i prerequisiti seguenti:

1. Assegnare il ruolo **lettore di backup su disco** all'identità gestita dell'insieme di credenziali di backup nel disco di origine di cui eseguire il backup.

   1. Passare al disco di cui deve essere eseguito il backup.

   1. Passare a **controllo di accesso (IAM)** e selezionare **Aggiungi assegnazioni di ruolo**

   1. Nel riquadro del contesto destro selezionare **lettore di backup su disco** nell'elenco a discesa **ruolo** . Selezionare l'identità gestita dell'insieme di credenziali per il backup e **salvarla**.

   >[!TIP]
   >Digitare il nome dell'insieme di credenziali di backup per selezionare l'identità gestita dell'insieme di credenziali.

   ![Aggiungi ruolo lettore di backup su disco](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Assegnare il ruolo di **collaboratore snapshot del disco** all'identità gestita dell'insieme di credenziali di backup nel gruppo di risorse in cui i backup vengono creati e gestiti dal servizio backup di Azure. Gli snapshot del disco vengono archiviati in un gruppo di risorse all'interno della sottoscrizione. Per consentire al servizio backup di Azure di creare, archiviare e gestire gli snapshot, è necessario fornire le autorizzazioni per l'insieme di credenziali di backup.

   **Scelta del gruppo di risorse per l'archiviazione e la gestione di snapshot:**

   - Non selezionare lo stesso gruppo di risorse del disco di origine.

   - Come linea guida, è consigliabile creare un gruppo di risorse dedicato come archivio dati snapshot da usare con il servizio backup di Azure. Un gruppo di risorse dedicato consente di limitare le autorizzazioni di accesso al gruppo di risorse, garantendo la sicurezza e la facilità di gestione dei dati di backup.

   - È possibile usare questo gruppo di risorse per archiviare gli snapshot tra più dischi che vengono sottoposti a backup (o pianificati).  

   - Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco. Quindi, scegliere il gruppo di risorse nella stessa sottoscrizione del disco di cui eseguire il backup. Altre informazioni sullo [snapshot incrementale](../virtual-machines/disks-incremental-snapshots.md#restrictions) per Managed Disks.

   Per assegnare il ruolo, attenersi alla procedura seguente:

   1. Passare al gruppo di risorse. Ad esempio, il gruppo di risorse è *SnapshotRG*, che si trova nella stessa sottoscrizione di quella del disco di cui eseguire il backup.

   1. Passare a **controllo di accesso (IAM)** e selezionare **Aggiungi assegnazioni di ruolo**.

   1. Nel riquadro del contesto destro selezionare **collaboratore snapshot disco** nell'elenco a discesa **ruolo** . Selezionare l'identità gestita dell'insieme di credenziali per il backup e **salvarla**.

   >[!TIP]
   >Digitare il nome dell'insieme di credenziali di backup per selezionare l'identità gestita dell'insieme di credenziali.

   ![Aggiungi ruolo Collaboratore snapshot disco](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Verificare che l'identità gestita dell'insieme di credenziali di backup includa il set corretto di assegnazioni di ruolo nel disco di origine e nel gruppo di risorse che funge da archivio dati snapshot.

   1. Passare a **backup Vault-> Identity** e selezionare **assegnazioni di ruolo di Azure**.

      ![Selezionare le assegnazioni di ruolo di Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Verificare che il ruolo, il nome della risorsa e il tipo di risorsa siano riflessi correttamente.

      ![Verificare il ruolo, il nome della risorsa e il tipo di risorsa](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Mentre le assegnazioni di ruolo vengono riflesse correttamente nel portale, potrebbero essere necessari circa 15 minuti per l'applicazione dell'autorizzazione nell'identità gestita dell'insieme di credenziali di backup.

1. Una volta soddisfatti i prerequisiti, passare a **backup Vault-> Panoramica** e selezionare **backup** per avviare la configurazione del backup dei dischi.

   ![Seleziona backup](./media/backup-managed-disks/select-backup.png)

1. Nella scheda **nozioni di base** selezionare **disco di Azure** come tipo di origine dati.

   ![Selezionare il disco di Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Backup di Azure usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md#restrictions) dei dischi gestiti, che archiviano solo le modifiche delta apportate al disco dall'ultimo snapshot in HDD standard archiviazione, indipendentemente dal tipo di archiviazione del disco padre. Per una maggiore affidabilità, per impostazione predefinita gli snapshot incrementali vengono archiviati nell'archiviazione con ridondanza della zona (ZRS) nelle aree che supportano ZRS. Il backup su disco di Azure supporta attualmente il backup operativo di dischi gestiti che non copia i backup nell'archivio dell'insieme di credenziali di backup. Quindi, l'impostazione di ridondanza dell'archiviazione di backup dell'insieme di credenziali di backup non si applica ai punti di ripristino.

1. Nella scheda **criteri di backup** scegliere un criterio di backup.

   ![Scegliere i criteri di backup](./media/backup-managed-disks/choose-backup-policy.png)

1. Nella scheda **risorse** selezionare **Seleziona disco di Azure**. Nel riquadro del contesto destro selezionare i dischi di cui eseguire il backup.

   ![Selezionare i dischi di cui eseguire il backup](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Sebbene il portale consenta di selezionare più dischi e di configurare il backup, ogni disco è una singola istanza di backup. Il backup su disco di Azure supporta attualmente solo il backup di singoli dischi. Il backup temporizzato di più dischi collegati a un disco virtuale non è supportato.
   >
   >Quando si usa il portale, è possibile selezionare solo i dischi all'interno della stessa sottoscrizione. Se è necessario eseguire il backup di più dischi o se i dischi sono distribuiti in una sottoscrizione diversa, è possibile usare gli script per automatizzare.
   >
   >Per ulteriori informazioni sulla disponibilità dell'area di backup su disco di Azure, sulle limitazioni e sugli scenari supportati, vedere la [matrice di supporto](disk-backup-support-matrix.md).

1. Selezionare un **gruppo di risorse snapshot** e selezionare **convalida**. Questo è il gruppo di risorse in cui il servizio backup di Azure crea e gestisce gli snapshot incrementali per l'insieme di credenziali di backup. L'identità gestita viene assegnata con le autorizzazioni del ruolo necessarie come parte dei prerequisiti.

   ![Seleziona gruppo di risorse snapshot](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Il completamento della convalida potrebbe richiedere alcuni minuti prima di poter completare la configurazione del flusso di lavoro di backup. La convalida può non riuscire se:
   >
   > - il disco non è supportato. Per gli scenari non supportati, vedere la [matrice di supporto](disk-backup-support-matrix.md) .
   > - l'identità gestita dell'insieme di credenziali per il backup non ha assegnazioni di ruolo valide nel **disco** di cui eseguire il backup o nel **gruppo di risorse snapshot** in cui sono archiviati gli snapshot incrementali.

1. Dopo aver completato la convalida, selezionare **Verifica e configura** per configurare il backup dei dischi selezionati.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

1. Nell'insieme di credenziali per il **backup** di *DemoVault* creato nel passaggio precedente passare a **istanze di backup** e selezionare un'istanza di backup.

   ![Selezionare l'istanza di backup](./media/backup-managed-disks/select-backup-instance.png)

1. Nella schermata **istanze di backup** sono disponibili le seguenti informazioni:

   - informazioni **essenziali** , inclusi il nome del disco di origine, il gruppo di risorse snapshot in cui vengono archiviati gli snapshot incrementali, l'insieme di credenziali di backup e i criteri di backup.
   - **Stato del processo** che mostra il riepilogo delle operazioni di backup e ripristino e il relativo stato negli ultimi sette giorni.
   - Elenco di **punti di ripristino** per il periodo di tempo selezionato.

1. Selezionare **backup** per avviare un backup su richiesta.

   ![Selezionare Esegui backup ora](./media/backup-managed-disks/backup-now.png)

1. Selezionare una delle regole di conservazione associate al criterio di backup. Questa regola di conservazione determinerà la durata di conservazione del backup su richiesta. Selezionare **Esegui backup ora** per avviare il backup.

   ![Avvia backup](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Tenere traccia di un'operazione di backup

Il servizio backup di Azure crea un processo per i backup pianificati o se si attiva l'operazione di backup su richiesta per il rilevamento. Per visualizzare lo stato del processo di backup:

1. Passare alla schermata dell' **istanza di backup** . Mostra il dashboard dei processi con l'operazione e lo stato degli ultimi sette giorni.

   ![Dashboard dei processi](./media/backup-managed-disks/jobs-dashboard.png)

1. Per visualizzare lo stato dell'operazione di backup, selezionare **Visualizza tutto** per visualizzare i processi in corso e passati di questa istanza di backup.

   ![Seleziona Visualizza tutto](./media/backup-managed-disks/view-all.png)

1. Esaminare l'elenco dei processi di backup e ripristino e il relativo stato. Selezionare un processo dall'elenco dei processi per visualizzare i dettagli del processo.

   ![Selezionare il processo per visualizzare i dettagli](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare Managed Disks di Azure](restore-managed-disks.md)