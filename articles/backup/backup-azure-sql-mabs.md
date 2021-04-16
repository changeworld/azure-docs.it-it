---
title: Eseguire il SQL Server backup usando server di Backup di Azure
description: Questo articolo illustra la configurazione per eseguire il backup SQL Server database usando Backup di Microsoft Azure Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: e79b5263b248312b7170288be24ab5fc196042a7
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518661"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Eseguire il SQL Server in Azure usando server di Backup di Azure

Questo articolo illustra come configurare i backup di SQL Server database usando Backup di Microsoft Azure Server (MABS).

Per eseguire il backup di un database SQL Server e ripristinarlo da Azure:

1. Creare un criterio di backup per proteggere SQL Server database in Azure.
1. Creare copie di backup su richiesta in Azure.
1. Ripristinare il database in Azure.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

* Se un database con file è ubicato in una condivisione di file remota, la protezione fallirà con Errore ID 104. MABS non supporta la protezione per SQL Server dati in una condivisione file remota.
* MABS non è in grado di proteggere i database archiviati in condivisioni SMB remote.
* Assicurarsi che le [repliche del gruppo di disponibilità siano configurate in sola lettura](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* È necessario aggiungere in modo esplicito l'account di sistema **NTAuthority\System** al gruppo Sysadmin SQL Server.
* Quando si esegue il ripristino in un percorso alternativo per un database parzialmente indipendente, assicurarsi che per l'istanza di SQL di destinazione sia stata abilitata la funzionalità [Database indipendenti](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable).
* Quando si esegue il ripristino in un percorso alternativo per un database di flusso dei file, assicurarsi che per l'istanza di SQL di destinazione sia stata abilitata la funzionalità del [database FILESTREAM](/sql/relational-databases/blob/enable-and-configure-filestream).
* Protezione per SQL Server AlwaysOn:
  * MABS rileva i gruppi di disponibilità durante l'esecuzione di una richiesta al momento della creazione del gruppo protezione dati.
  * MABS rileva un failover e continua la protezione del database.
  * MABS supporta le configurazioni di cluster multis sito per un'istanza di SQL Server.
* Quando si proteggono i database che usano la funzionalità AlwaysOn, MABS presenta le limitazioni seguenti:
  * MaBS rispetta i criteri di backup per i gruppi di disponibilità impostati in SQL Server in base alle preferenze di backup, come indicato di seguito:
    * Preferisco secondario: i backup devono essere eseguiti in una replica secondaria tranne quando la replica primaria è l'unica replica online. Se sono disponibili più repliche secondarie, per il backup verrà selezionato il nodo con la priorità di backup più alta. Se è disponibile solo la replica primaria, il backup deve essere eseguito nella replica primaria.
    * Solo secondario: il backup non deve essere eseguito nella replica primaria. Se la replica primaria è l'unica online, il backup non deve essere eseguito.
    * Primario: i backup devono essere sempre eseguiti nella replica primaria.
    * Qualsiasi replica: i backup possono essere eseguiti in qualsiasi replica nel gruppo di disponibilità. Il nodo da cui eseguire il backup sarà basato sulle priorità di backup per ciascuno dei nodi.
  * Tenere presente quanto segue:
    * I backup possono essere evasi da qualsiasi replica leggibile, ad esempio primaria, secondaria sincrona e secondaria asincrona.
    * Se una replica è esclusa  dal backup, ad esempio l'opzione Escludi replica è abilitata o è contrassegnata come non leggibile, tale replica non verrà selezionata per il backup in nessuna delle opzioni.
    * Se sono disponibili e leggibili più repliche, per il backup verrà selezionato il nodo con la priorità di backup più alta.
    * Se il backup non riesce nel nodo selezionato, l'operazione di backup non riesce.
    * Il ripristino nel percorso originale non è supportato.
* Problemi di backup di SQL Server 2014 o versione successiva:
  * In SQL Server 2014 è stata aggiunta una nuova funzionalità per creare un [database di SQL Server locale nell'archivio BLOB di Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). MaBS non può essere usato per proteggere questa configurazione.
  * Esistono alcuni problemi noti relativi alla preferenza di backup "Prefer secondary" per l'opzione SQL AlwaysOn. MABS usa sempre un backup da un database secondario. Se non viene trovato alcun database secondario, il backup ha esito negativo.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, assicurarsi di aver [installato e preparato server di Backup di Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Per proteggere SQL Server database in Azure, creare prima un criterio di backup:

1. Nella server di Backup di Azure selezionare l'area **di lavoro** Protezione.
1. Selezionare **Nuovo** per creare un gruppo protezione dati.

    ![Creare un gruppo protezione dati in server di Backup di Azure](./media/backup-azure-backup-sql/protection-group.png)
1. Nella pagina iniziale esaminare le indicazioni sulla creazione di un gruppo protezione dati. Selezionare quindi **Avanti**.
1. Per il tipo di gruppo protezione dati selezionare **Server**.

    ![Selezionare il tipo di gruppo protezione dati Server](./media/backup-azure-backup-sql/pg-servers.png)
1. Espandere la SQL Server in cui si trovano i database di cui si vuole eseguire il backup. Verranno visualizzati le origini dati di cui è possibile eseguire il backup da tale server. Espandere **Tutte le condivisioni SQL** e quindi selezionare i database di cui si vuole eseguire il backup. In questo esempio vengono selezionati ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB. Selezionare **Avanti**.

    ![Selezionare un database SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Assegnare un nome al gruppo protezione dati e quindi selezionare I want online protection (I **want online protection).**

    ![Scegliere un metodo di protezione dei dati: protezione del disco a breve termine o protezione online di Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Nella pagina **Specifica Short-Term obiettivi** includere gli input necessari per creare punti di backup sul disco.

    In questo esempio **l'intervallo di conservazione** è impostato su *5 giorni.* La **frequenza di sincronizzazione dei** backup è impostata su una volta ogni *15 minuti.* **Il backup completo** rapido è impostato su *20:00.*

    ![Configurare gli obiettivi a breve termine per la protezione dei backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In questo esempio viene creato un punto di backup ogni giorno alle 20:00. Vengono trasferiti i dati modificati dopo il punto di backup delle 20:00 del giorno precedente. Questo processo è detto **Backup completo rapido**. Anche se i log delle transazioni vengono sincronizzati ogni 15 minuti, se è necessario ripristinare il database alle 21.00, il punto viene creato riproducendo i log dall'ultimo punto di backup completo rapido, ovvero le 20:00 di questo esempio.
   >
   >

1. Selezionare **Avanti**. MABS mostra lo spazio di archiviazione complessivo disponibile. Mostra anche il potenziale utilizzo dello spazio su disco.

    ![Configurare l'allocazione dei dischi in MABS](./media/backup-azure-backup-sql/pg-storage.png)

    Per impostazione predefinita, MABS crea un volume per ogni origine dati (SQL Server database). Il volume viene usato per la copia di backup iniziale. In questa configurazione, Gestione dischi logici (LDM) limita la protezione MABS a 300 origini dati (SQL Server database). Per porre rimedio a questa limitazione, selezionare **Condividi percorso dati nel pool di archiviazione DPM**. Se si usa questa opzione, MABS usa un singolo volume per più origini dati. Questa configurazione consente a MABS di proteggere fino a 2.000 SQL Server database.

    Se si seleziona **Aumenta automaticamente i volumi,** MABS può contenere l'aumento del volume di backup man mano che aumentano i dati di produzione. Se non si seleziona Aumenta **automaticamente** i volumi, MABS limita l'archiviazione di backup alle origini dati nel gruppo protezione dati.
1. Gli amministratori possono scegliere di trasferire automaticamente il **backup** iniziale in rete e scegliere l'ora del trasferimento. In altro modo, **scegliere Trasferisci** manualmente il backup. Selezionare quindi **Avanti**.

    ![Scegliere un metodo di creazione della replica in MABS](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (SQL Server database). I dati di backup vengono spostati dal server di produzione (SQL Server computer) al server di Backup di Microsoft Windows. Se il backup è di grandi dimensioni, il trasferimento dei dati in rete potrebbe causare la congestione della larghezza di banda. Per questo motivo, gli amministratori possono scegliere di usare supporti rimovibili per trasferire manualmente il backup **iniziale.** Oppure possono trasferire automaticamente i **dati in rete** in un momento specificato.

    Al termine del backup iniziale, i backup continuano in modo incrementale nella copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.
1. Scegliere quando eseguire una verifica coerenza. Selezionare quindi **Avanti**.

    ![Scegliere quando eseguire una verifica coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    MaBS può eseguire una verifica coerenza sull'integrità del punto di backup. Calcola il checksum del file di backup nel server di produzione (il computer SQL Server in questo esempio) e i dati di cui è stato eseguito il backup per tale file in MABS. Se il controllo rileva un conflitto, si presuppone che il file di cui è stato eseguito il backup in MABS sia danneggiato. MABS corregge i dati sottoposti a backup inviando i blocchi che corrispondono alla mancata corrispondenza del checksum. Poiché la verifica coerenza è un'operazione a elevato utilizzo di prestazioni, gli amministratori possono scegliere di pianificare la verifica coerenza o eseguirla automaticamente.
1. Selezionare le origini dati da proteggere in Azure. Selezionare quindi **Avanti**.

    ![Selezionare le origini dati da proteggere in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Gli amministratori possono scegliere pianificazioni dei backup e criteri di conservazione adatti ai criteri dell'organizzazione.

    ![Scegliere pianificazioni e criteri di conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio i backup vengono evasi ogni giorno alle 12:00 e alle 20:00.

    > [!TIP]
    > Per un ripristino rapido, mantenere alcuni punti di ripristino a breve termine sul disco. Questi punti di recupero vengono usati per il recupero operativo. Azure funge da buona posizione fuori sede, offrendo contratti di servizio più elevati e disponibilità garantita.
    >
    > Usare Data Protection Manager (DPM) per pianificare Backup di Azure al termine dei backup del disco locale. Quando si segue questa procedura, il backup del disco più recente viene copiato in Azure.
    >

1. Scegliere la pianificazione per i criteri di conservazione. Per altre informazioni sul funzionamento dei criteri di conservazione, vedere Use [Backup di Azure to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md).

    ![Scegliere un criterio di conservazione in MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In questo esempio:

    * I backup vengono evasi ogni giorno alle 12:00 e alle 20:00. Vengono conservati per 180 giorni.
    * Il backup di sabato alle 12:00 viene conservato per 104 settimane.
    * Il backup dell'ultimo sabato del mese alle 12:00 viene conservato per 60 mesi.
    * Il backup dell'ultimo sabato di marzo alle 12:00 viene conservato per 10 anni.

    Dopo aver scelto un criterio di conservazione, selezionare **Avanti.**
1. Scegliere come trasferire la copia di backup iniziale in Azure.

    * **L'opzione Automatically over the network** (Automaticamente in rete) segue la pianificazione del backup per trasferire i dati in Azure.
    * Per altre informazioni sul backup **offline,** vedere [Panoramica del backup offline.](offline-backup-overview.md)

    Dopo aver scelto un meccanismo di trasferimento, selezionare **Avanti.**
1. Nella pagina **Riepilogo** esaminare i dettagli dei criteri. Selezionare quindi **Crea gruppo.** È possibile selezionare Chiudi **e** controllare lo stato del processo nell'area **di lavoro** Monitoraggio.

    ![Stato di avanzamento della creazione del gruppo protezione dati](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Creare copie di backup su richiesta di un database SQL Server locale

Quando viene eseguito il primo backup, viene creato un punto di ripristino. Anziché attendere l'esecuzione della pianificazione, è possibile attivare manualmente la creazione di un punto di ripristino:

1. Nel gruppo protezione dati verificare che lo stato del database sia **OK.**

    ![Un gruppo protezione dati, che mostra lo stato del database](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Fare clic con il pulsante destro del mouse sul database e **quindi scegliere Crea punto di ripristino**.

    ![Scegliere di creare un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Nel menu a discesa selezionare **Protezione online**. Selezionare quindi **OK** per avviare la creazione di un punto di ripristino in Azure.

    ![Iniziare a creare un punto di ripristino in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. È possibile visualizzare lo stato del processo nell'area **di lavoro** Monitoraggio.

    ![Visualizzare lo stato del processo nella console di monitoraggio](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Ripristinare un database SQL Server da Azure

Per ripristinare un'entità protetta, ad esempio un database SQL Server, da Azure:

1. Aprire la console di gestione del server DPM. Passare **all'area di** lavoro Ripristino per visualizzare i server di cui DPM è in backup. Selezionare il database (in questo esempio ReportServer$MSDPM2012). Selezionare un **tempo di ripristino** che termina con **Online.**

    ![selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Fare clic con il pulsante destro del mouse sul nome del database e **scegliere Ripristina**.

    ![Ripristinare un database da Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM mostra i dettagli del punto di ripristino. Selezionare **Avanti**. Per sovrascrivere il database, selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Selezionare quindi **Avanti**.

    ![Ripristinare un database nel percorso originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio DPM consente il ripristino del database in un'altra SQL Server o in una cartella di rete autonoma.
1. Nella pagina **Specifica opzioni di** ripristino è possibile selezionare le opzioni di ripristino. Ad esempio, è possibile scegliere Limitazione **dell'utilizzo della larghezza di** banda di rete per limita la larghezza di banda utilizzata dal ripristino. Selezionare quindi **Avanti**.
1. Nella pagina **Riepilogo** viene visualizzata la configurazione di ripristino corrente. Selezionare **Ripristina**.

    Lo stato di ripristino indica il database in fase di recupero. È possibile selezionare **Chiudi per** chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area **di lavoro** Monitoraggio.

    ![Avviare il processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Al termine del ripristino, il database ripristinato è coerente con l'applicazione.

### <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere domande [Backup di Azure seguenti.](backup-azure-backup-faq.yml)
