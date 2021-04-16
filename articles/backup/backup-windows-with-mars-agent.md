---
title: Eseguire il backup di computer Windows usando l'agente MARS
description: Usare l Microsoft Azure di Servizi di ripristino di Microsoft Azure per eseguire il backup dei computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519171"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Eseguire il backup di file e cartelle di Windows Server in Azure

Questo articolo illustra come eseguire il backup di computer Windows usando il [servizio Backup di Azure](backup-overview.md) e l'agente Microsoft Azure Servizi di ripristino di emergenza (MARS). MARS è noto anche come agente Backup di Azure servizio.

In questo articolo si apprenderà come:

> [!div class="checklist"]
>
> * Verificare i prerequisiti
> * Creare un criterio di backup e una pianificazione.
> * Eseguire un backup su richiesta.

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni su [come Backup di Azure'agente MARS per eseguire il backup di computer Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Informazioni sull'architettura [di backup che](backup-architecture.md#architecture-back-up-to-dpmmabs) esegue l'agente MARS in un server di Backup di Microsoft Data Protection Manager secondario.
* Esaminare [gli elementi supportati e gli elementi di cui è possibile eseguire il backup tramite](backup-support-matrix-mars-agent.md) l'agente mars.
* [Verificare l'accesso](install-mars-agent.md#verify-internet-access) a Internet nei computer di cui si vuole eseguire il backup.
* Se l'agente MARS non è installato, vedere [qui.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

I criteri di backup specificano quando creare snapshot dei dati per creare punti di ripristino. Specifica anche per quanto tempo mantenere i punti di ripristino. L'agente MARS viene utilizzato per configurare un criterio di backup.

Backup di Azure non prende automaticamente in considerazione l'ora legale. Questa impostazione predefinita potrebbe causare una discrepanza tra l'ora effettiva e l'ora di backup pianificata.

Per creare un criterio di backup:

1. Dopo aver scaricato e registrato l'agente MARS, aprire la console dell'agente. È possibile trovarlo se si cerca **Backup di Microsoft Azure** nel computer.  

1. In **Azioni** selezionare **Pianifica backup.**

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. Nella Pianificazione guidata backup selezionare **Attività preliminari**  >  **Avanti.**
1. In **Selezionare gli elementi di cui eseguire il backup** selezionare Aggiungi **elementi.**

    ![Aggiungere elementi di cui eseguire il backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Nella casella **Seleziona elementi** selezionare gli elementi di cui eseguire il backup e quindi selezionare **OK.**

    ![Seleziona elementi per backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Nella pagina **Selezione elementi di cui eseguire il** backup selezionare **Avanti.**
1. Nella pagina **Specifica pianificazione backup** specificare quando eseguire backup giornalieri o settimanali. Selezionare quindi **Avanti**.

    * Quando viene eseguito un backup, viene creato un punto di ripristino.
    * Il numero di punti di ripristino creati nell'ambiente dipende dalla pianificazione del backup.
    * È possibile pianificare fino a tre backup giornalieri al giorno. Nell'esempio seguente vengono eseguiti due backup giornalieri, uno a mezzanotte e uno alle 18:00.

        ![Configurare una pianificazione di backup giornaliera](./media/backup-configure-vault/day-schedule.png)

    * È anche possibile eseguire backup settimanali. Nell'esempio seguente i backup vengono evasi ogni domenica e mercoledì alternativi alle 9:30 e alle 13:00.

        ![Configurare una pianificazione di backup settimanale](./media/backup-configure-vault/week-schedule.png)

1. Nella pagina **Seleziona criteri di conservazione** specificare come archiviare le copie cronologiche dei dati. Selezionare quindi **Avanti**.

    * Le impostazioni di conservazione specificano i punti di ripristino da archiviare e per quanto tempo archiviarli.
    * Per un'impostazione di conservazione giornaliera, si indica che al momento specificato per la conservazione giornaliera, il punto di ripristino più recente verrà conservato per il numero di giorni specificato. Oppure è possibile specificare un criterio di conservazione mensile per indicare che il punto di ripristino creato il 30 di ogni mese deve essere archiviato per 12 mesi.
    * La conservazione per i punti di ripristino giornalieri e settimanali coincide in genere con la pianificazione del backup. Pertanto, quando la pianificazione attiva un backup, il punto di ripristino creato dal backup viene archiviato per la durata specificata dal criterio di conservazione giornaliero o settimanale.
    * Nell'esempio seguente:

        * I backup giornalieri a mezzanotte e alle 18:00 vengono conservati per sette giorni.
        * I backup evasi di sabato a mezzanotte e alle 18:00 vengono conservati per quattro settimane.
        * I backup evasi nell'ultimo sabato del mese a mezzanotte e alle 18:00 vengono conservati per 12 mesi.
        * I backup evasi nell'ultimo sabato di marzo vengono conservati per 10 anni.

        ![Esempio di criteri di conservazione](./media/backup-configure-vault/retention-example.png)

1. Nella pagina **Scegli tipo di backup** iniziale decidere se eseguire il backup iniziale in rete o usare il backup offline. Per eseguire il backup iniziale in rete, selezionare **Automaticamente in rete**  >  **Avanti.**

    Per altre informazioni sul backup offline, vedere [Usare Azure Data Box per il backup offline.](offline-backup-azure-data-box.md)

    ![Scegliere un tipo di backup iniziale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Nella pagina **Conferma** esaminare le informazioni e quindi selezionare **Fine.**

    ![Verificare il tipo di backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

    ![Visualizzare lo stato di avanzamento della pianificazione del backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Creare un criterio in ogni computer in cui è installato l'agente.

### <a name="do-the-initial-backup-offline"></a>Eseguire il backup iniziale offline

È possibile eseguire automaticamente un backup iniziale in rete oppure eseguire il backup offline. Il seeding offline per un backup iniziale è utile se si dispone di grandi quantità di dati che richiederanno una grande quantità di larghezza di banda di rete per il trasferimento.

Per eseguire un trasferimento offline:

1. Scrivere i dati di backup in un percorso di staging.
1. Usare lo strumento AzureOfflineBackupDiskPrep per copiare i dati dal percorso di staging a uno o più dischi SATA.

    Lo strumento crea un processo di importazione di Azure. Per altre informazioni, vedere [Informazioni sul servizio Importazione/Esportazione di Azure.](../import-export/storage-import-export-service.md)
1. Inviare i dischi SATA a un data center di Azure.

    Nel data center i dati del disco vengono copiati in un account di archiviazione di Azure. Backup di Azure copia i dati dall'account di archiviazione all'insieme di credenziali e vengono pianificati i backup incrementali.

Per altre informazioni sul seeding offline, vedere Usare Azure Data Box [per il backup offline.](offline-backup-azure-data-box.md)

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda

È possibile controllare il modo in cui l'agente MARS usa la larghezza di banda di rete abilitando la limitazione della rete. La limitazione è utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma si vuole controllare la larghezza di banda utilizzata dall'attività di backup e ripristino.

La limitazione della Backup di Azure di rete usa la qualità del [servizio (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) nel sistema operativo locale.

La limitazione della rete per i backup è disponibile in Windows Server 2012 e versioni successive e in Windows 8 e versioni successive. I sistemi operativi devono eseguire i Service Pack più recenti.

Per abilitare la limitazione della rete:

1. Nell'agente MARS selezionare **Modifica proprietà**.
1. Nella scheda **Limitazione selezionare** Abilita limitazione dell'utilizzo della larghezza di banda Internet per le operazioni **di backup.**

    ![Configurare la limitazione della rete per le operazioni di backup](./media/backup-configure-vault/throttling-dialog.png)
1. Specificare la larghezza di banda consentita durante le ore lavorative e non lavorative. I valori della larghezza di banda iniziano da 512 Kbps e passano a 1.023 Mbps. Selezionare **OK**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

1. Nell'agente MARS selezionare **Backup ora**.

    ![Eseguire il backup ora in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se la versione dell'agente MARS è 2.0.9169.0 o successiva, è possibile impostare una data di conservazione personalizzata. Nella sezione **Mantieni backup fino** a scegliere una data dal calendario.

   ![Usare il calendario per personalizzare una data di conservazione](./media/backup-configure-vault/mars-ondemand.png)

1. Nella pagina **Conferma** esaminare le impostazioni e selezionare **Backup.**
1. Fare clic su **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima del completamento del backup, la procedura guidata continuerà a essere eseguita in background.

Al termine del backup iniziale, **nella** console backup viene visualizzato lo stato Processo completato.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Configurare il comportamento di conservazione dei criteri di backup su richiesta

> [!NOTE]
> Queste informazioni si applicano solo alle versioni dell'agente MARS precedenti alla 2.0.9169.0.
>

| Opzione backup-schedule | Durata della conservazione dei dati
| -- | --
| Giorno | **Conservazione predefinita:** equivalente alla "conservazione in giorni per i backup giornalieri". <br/><br/> **Eccezione:** se un backup pianificato giornaliero impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta che viene attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine.<br/><br/> **Scenario di esempio:** il backup pianificato di giovedì alle 8:00 non è riuscito. Questo backup deve essere considerato per la conservazione settimanale, mensile o annuale. Pertanto, il primo backup su richiesta attivato prima del successivo backup pianificato il venerdì alle 8:00 viene automaticamente contrassegnato per la conservazione settimanale, mensile o annuale. Questo backup sostituisce il backup di giovedì alle 8:00.
| Settimana | **Conservazione predefinita:** un giorno. I backup su richiesta evasi per un'origine dati con criteri di backup settimanali vengono eliminati il giorno successivo. Vengono eliminati anche se si tratta dei backup più recenti per l'origine dati. <br/><br/> **Eccezione:** se un backup pianificato settimanale impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta che viene attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine. <br/><br/> **Scenario di esempio:** il backup pianificato di giovedì alle 8:00 non è riuscito. Questo backup deve essere considerato per la conservazione mensile o annuale. Il primo backup su richiesta attivato prima del successivo backup pianificato di giovedì alle 8.00 viene quindi contrassegnato automaticamente per la conservazione mensile o annuale. Questo backup sostituisce il backup di giovedì 8:00.

Per altre informazioni, vedere [Creare un criterio di backup.](#create-a-backup-policy)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare i file in Azure.](backup-azure-restore-windows-server.md)
* Domande [comuni sul backup di file e cartelle](backup-azure-file-folder-backup-faq.yml)
