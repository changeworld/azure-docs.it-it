---
title: Eseguire il backup del server di Backup di Microsoft Azure
description: Informazioni su come eseguire il backup di Backup di Microsoft Azure Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519120"
---
# <a name="back-up-the-mabs-server"></a>Eseguire il backup del server di Backup di Microsoft Azure

Per assicurarsi che i dati possano essere ripristinati in caso di errore di Backup di Microsoft Azure Server (MABS), è necessaria una strategia per il backup del server maBS. Se non viene eseguito il backup, sarà necessario ricompilarlo manualmente dopo un errore e i punti di ripristino basati su disco non saranno recuperabili. È possibile eseguire il backup dei server maBS tramite il backup del database maBS.

## <a name="back-up-the-mabs-database"></a>Eseguire il backup del database MABS

Nell'ambito della strategia di backup maBS, è necessario eseguire il backup del database maBS. Il database mabs è denominato DPMDB. Questo database contiene la configurazione maBS insieme ai dati sui backup di MABS. In caso di emergenza, è possibile ricompilare la maggior parte delle funzionalità di un server mabs usando un backup recente del database. Supponendo che sia possibile ripristinare il database, i backup basati su nastro sono accessibili e mantengono tutte le impostazioni del gruppo protezione dati e le pianificazioni di backup. Se i dischi del pool di archiviazione del server di Backup di Microsoft Azure non sono stati interessati dall'interruzione, anche i backup basati su disco possono essere utilizzati dopo una ricompilazione. È possibile eseguire il backup del database con diversi metodi.

|Metodo di backup del database|Vantaggi|Svantaggi|
|--------------------------|--------------|-----------------|
|[Backup in Azure](#back-up-to-azure)|<li>Facilità di configurazione e monitoraggio in MABS.<li>Più percorsi dei file del database di backup.<li>L'archiviazione cloud offre una soluzione efficace per il ripristino di emergenza.<li>Archiviazione molto sicura per il database.<li>Supporta 120 punti di ripristino online.|<li>Richiede l'account Azure e la configurazione aggiuntiva del server di Database di Microsoft Azure. Comporta un costo per l'archiviazione Azure.<li> Richiede una versione supportata del sistema basato su Windows Server con l'agente di Azure per ottenere l'accesso ai backup del server di Backup di Microsoft Azure archiviati nell'insieme di credenziali di backup di Azure. Non può trattarsi di un altro server maBS.<li>Opzione non disponibile se il database è ospitato in locale e vuoi abilitare la protezione secondaria. <li>Occorre più tempo per la preparazione e il ripristino.|
|[Eseguire il backup del database tramite il backup del pool di archiviazione del server di Backup di Microsoft Azure](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Facilità di configurazione e monitoraggio.<li>Il backup viene mantenuto nei dischi del pool di archiviazione del server di Backup di Microsoft Azure ed è facile da accedere in locale.<li>I backup pianificati mabs supportano 512 backup completi express. Se si backup su base oraria, si hanno 21 giorni di protezione completa.|<li>Non è un'opzione valida per il ripristino di emergenza. È online e il ripristino potrebbe non funzionare come previsto in caso di errore del disco del server di Backup di Microsoft Azure o del pool di archiviazione.<li>Opzione non disponibile se il database è ospitato in locale e vuoi abilitare la protezione secondaria. <li>Sono necessari alcuni passaggi di preparazione e speciali per ottenere l'accesso ai punti di ripristino se il servizio o la console di MaBS non è in esecuzione o non funziona.|
|[Backup con il backup nativo di SQL Server in un disco locale](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Incorporato in SQL Server.<li>Il backup viene mantenuto su un disco locale facilmente accessibile.<li>L'esecuzione può essere pianificata con la frequenza desiderata.<li>Completamente indipendente da MABS.<li>È possibile pianificare una pulizia del file di backup.|<li>Non è un'opzione valida per il ripristino di emergenza, a meno che i backup non vengano copiati in una posizione remota.<li>Richiede l'archiviazione locale per i backup, che possono limitare la conservazione e la frequenza.|
|[Eseguire il backup con il backup SQL nativo e la protezione MABS in una condivisione protetta da MABS](#back-up-to-a-share-protected-by-mabs)|<li>Facilmente monitorato in MABS.<li>Più percorsi dei file del database di backup.<li>Facilità di accesso da qualsiasi computer Windows in rete.<li>È potenzialmente il metodo di ripristino più veloce.|<li>Supporta solo 64 punti di ripristino.<li>Non è un'opzione valida per il ripristino di emergenza dei siti. L'errore del disco del server MABS o del pool di archiviazione MABS può ostacolare le operazioni di ripristino.<li>Non è un'opzione se il database MABS è ospitato in locale e si vuole abilitare la protezione secondaria. <li>Sono necessarie alcune attività di preparazione aggiuntive per la configurazione e il testing.<li>Sono necessari alcuni tempi di preparazione e ripristino aggiuntivi se il server MABS stesso non è disponibile, ma i dischi del pool di archiviazione MABS sono a posto.|

- Se si usa un gruppo protezione dati MABS, è consigliabile usare un gruppo protezione dati univoco per il database.

    > [!NOTE]
    > Ai fini del ripristino, l'installazione di MABS da ripristinare con il database MABS deve corrispondere alla versione del database MABS stesso.  Ad esempio, se il database da ripristinare è da un'installazione di MABS V3 con Aggiornamento cumulativo 1, il server MABS deve eseguire la stessa versione dell'aggiornamento cumulativo 1. Ciò significa che potrebbe essere necessario disinstallare e reinstallare MABS con una versione compatibile prima di ripristinare il database.  Per visualizzare la versione del database potrebbe essere necessario montarlo manualmente in un nome di database temporaneo, quindi eseguire una query SQL sul database per verificare l'ultimo aggiornamento cumulativo installato in base alla versione principale e secondaria.

- Per controllare la versione del database MABS, seguire questa procedura:

    1. Per eseguire la query, aprire SQL Management Studio e quindi connettersi all'istanza di SQL che esegue il database MABS.

    2. Selezionare il database MABS e quindi avviare una nuova query.

    3. Incollare il codice SQL seguente nel riquadro della query ed eseguirlo:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Se non viene restituito alcun risultato nei risultati della query o se il server MABS è stato aggiornato dalle versioni precedenti ma non è stato installato alcun nuovo aggiornamento cumulativo da allora, non sarà presente una voce per il server principale e secondario per un'installazione di base di MABS. Per controllare le versioni maBS associate agli aggiornamenti cumulativi, vedere [Elenco dei numeri di build per MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Backup in Azure

1. Prima di iniziare, è necessario eseguire uno script per recuperare il percorso del punto di montaggio del volume di replica MABS in modo da sapere quale punto di ripristino contiene il backup MABS. Eseguire questa operazione dopo la replica iniziale con Backup di Azure. Nello script sostituire con `dplsqlservername%` il nome dell'istanza SQL Server che ospita il database MABS.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Assicurarsi di avere il passcode specificato quando è stato installato l'agente di Servizi di ripristino di Azure e il server MABS è stato registrato nell'insieme Backup di Azure vault. Questo passcode sarà necessario per il ripristino del backup.

2. Creare un insieme di credenziali di Backup di Azure, scaricare il file di installazione dell’agente Backup di Azure e l’insieme di credenziali. Eseguire il file di installazione per installare l'agente nel server MABS e usare le credenziali dell'insieme di credenziali per registrare il server MABS nell'insieme di credenziali. [Altre informazioni](backup-azure-microsoft-azure-backup.md)

3. Dopo aver configurato l'insieme di credenziali, configurare un gruppo protezione dati MABS contenente il database MABS. Selezionare questa opzione per eseguire il backup su disco e in Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Ripristinare il database MABS da Azure

È possibile ripristinare il database da Azure usando qualsiasi server MABS registrato nell'insieme di Backup di Azure, come indicato di seguito:

1. Nella console di MABS selezionare **Recovery**  >  Add External MABS (Aggiungi server di backup **esterno).**

2. Specificare le credenziali dell'insieme di credenziali (scaricarlo dall'Backup di Azure vault). Si noti che le credenziali sono valide solo per due giorni.

3. In **Select External MABS for Recovery** selezionare il server MABS per cui si vuole ripristinare il database, digitare la passphrase di crittografia e selezionare **OK.**

4. Selezionare il punto di ripristino che si vuole usare nell'elenco dei punti disponibili. Selezionare **Cancella MABS esterno** per tornare alla visualizzazione MABS locale.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Eseguire il backup del database MABS nel pool di archiviazione MABS

> [!NOTE]  
> Questa opzione è applicabile per MABS con Modern Backup Storage.

1. Nella console di MABS selezionare **Protezione**  >  **dati Crea gruppo protezione dati**.
2. Nella pagina **Selezione tipo di gruppo protezione dati** seleziona **Server**.
3. Nella pagina **Selezione membri gruppo** selezionare database DPM .  Espandere il server MABS e selezionare DPMDB.
4. Nella pagina **Seleziona metodo protezione dati** selezionare **Protezione dati a breve termine tramite: disco**. Specificare le opzioni per i criteri di protezione a breve termine.
5. Dopo la replica iniziale del database MABS, eseguire lo script SQL seguente:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Ripristinare il database MABS

Per ricostruire il database MABS con lo stesso database, è necessario innanzitutto ripristinare il database MABS e sincronizzarlo con il database MABS appena installato.

#### <a name="use-the-following-steps"></a>Seguire questa procedura

1. Aprire un prompt dei comandi amministrativo ed eseguire `psexec.exe -s powershell.exe` per avviare una finestra di PowerShell nel contesto di sistema.
2. Decidere da quale posizione si vuole ripristinare il database.

#### <a name="to-copy-the-database-from-the-last-backup"></a>Per copiare il database dall'ultimo backup

1. Passare al percorso del disco rigido virtuale di replica `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Montare **il file disk0.vhdx** presente in esso usando il `mount-vhd disk0.vhdx` comando .
3. Dopo aver montato il disco rigido virtuale di replica, usare per assegnare una lettera di unità al volume di replica usando l'ID replica fisica `mountvol.exe` dall'output dello script SQL. ad esempio `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Per copiare il database da un punto di ripristino precedente

1. Passare alla directory del contenitore DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Al di sotto verranno visualizzati più directory con alcuni identificatori GUID univoci corrispondenti ai punti di ripristino presi per il database MABS. Altre directory rappresentano un punto di ripristino/PIT.
2. Passare a qualsiasi percorso vhd PIT, ad esempio e montare `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` **il file disk0.vhdx** presente in esso usando il `mount-vhd disk0.vhdx` comando .
3. Dopo aver montato il disco rigido virtuale di replica, usare per assegnare una lettera di unità al volume di replica usando l'ID replica fisica `mountvol.exe` dall'output dello script SQL. ad esempio `mountvol X: \?\Volume{}\`

   Tutti i termini visualizzati con parentesi graffe angolari nei passaggi precedenti sono segnaposto. Sostituirli con i valori appropriati come indicato di seguito:
   - **ReFSVolume** : percorso di accesso dall'output dello script SQL
   - **NOME DI DOMINIO COMPLETO MABSServer** : nome completo del server MABS
   - **PhysicalReplicaId** - ID replica fisica dallo script SQL
   - **PITId:** identificatore GUID diverso dall'ID replica fisica nella directory del contenitore.
4. Aprire un altro prompt dei comandi amministrativo ed `psexec.exe -s cmd.exe` eseguire per avviare un prompt dei comandi nel contesto di sistema.
5. Modificare la directory nell'unità X: e passare al percorso dei file di database MABS.
6. Copiarli in un percorso di facile ripristino. Al termine della copia chiudere la finestra di comando di psexec.
7. Passare alla finestra di PowerShell psexec aperta nel passaggio 1, passare al percorso VHDX e smontare il disco rigido virtuale usando il comando `dismount-vhd disk0.vhdx` .
8. Dopo aver reinstallato il server MABS, è possibile usare DPMDB ripristinato per connettersi al server MABS eseguendo il `DPMSYNC-RESTOREDB` comando .
9. Eseguire `DPMSYNC-SYNC` una volta completata `DPMSYNC-RESTOREDB` l'operazione.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Eseguire il backup del database tramite il backup del pool di archiviazione MABS

> [!NOTE]
> Questa opzione è applicabile per MABS con archiviazione legacy.

Prima di iniziare, è necessario eseguire uno script per recuperare il percorso del punto di montaggio del volume di replica MABS in modo da sapere quale punto di ripristino contiene il backup MABS. Eseguire questa operazione dopo la replica iniziale con Backup di Azure. Nello script sostituire con `dplsqlservername%` il nome dell'istanza SQL Server che ospita il database MABS.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Nella console di MABS selezionare **Protezione**  >  **dati Crea gruppo protezione dati**.

2. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**.

3. Nella pagina **Selezione membri gruppo** selezionare il database MABS. Espandere l'elemento server MABS e selezionare **DPMDB**.

4. Nella pagina **Selezione metodo di protezione dati** selezionare I want short-term protection using disk (I want **short-term protection using disk).** Specificare le opzioni per i criteri di protezione a breve termine. È consigliabile un intervallo di conservazione di due settimane per i database MABS.

#### <a name="recover-the-database"></a>Ripristinare il database

Se il server MABS è ancora operativo e il pool di archiviazione è intatto(ad esempio, problemi con il servizio o la console MABS), copiare il database dal volume di replica o da una copia shadow come indicato di seguito:

1. Decidere da dove si vuole ripristinare il database.

    - Se si vuole copiare il database dall'ultimo backup eseguito direttamente dal volume di replica **MABS,** usaremountvol.exeper assegnare una lettera di unità al volume di replica usando il GUID dell'output dello script SQL. ad esempio `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Se si desidera copiare il database da un punto di ripristino precedente (copia shadow), è necessario elencare tutte le copie shadow per la replica usando il GUID del volume dall'output dello script SQL. Questo comando elenca le copie shadow per il volume: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Prendere nota dell'ora di creazione e dell'ID della copia shadow da cui si vuole eseguire il ripristino.

2. Usare quindi **diskshadow.exe** per montare la copia shadow in una lettera di unità inutilizzata X: usando l'ID della copia shadow, in modo da poter copiare i file di database.

3. Aprire un prompt dei comandi amministrativo ed eseguire per avviare un prompt dei comandi nel contesto di sistema, in modo da avere l'autorizzazione per passare `psexec.exe -s cmd.exe` al volume di replica (X:) e copiare i file.

4. Passare all'unità X: e passare al percorso dei file di database MABS. Copiarli in un percorso di facile ripristino. Al termine della copia, è presente la finestra di comando psexec **ed** eseguirediskshadow.exee rimuovere l'esposizione del volume X:.

5. È ora possibile ripristinare i file di database usando SQL Management Studio o eseguendo **DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Backup con il backup nativo di SQL Server in un disco locale

È possibile eseguire il backup del database MABS in un disco locale con backup SQL Server nativo, indipendentemente da MABS.

- [Panoramica ](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) di backup e ripristino di database SQL Server.

- [Altre informazioni](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sul backup di SQL Server nel cloud.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Eseguire il backup in una condivisione protetta da MABS

Questa opzione di backup usa SQL nativo per eseguire il backup del database MABS in una condivisione, protegge la condivisione con MABS e usa le versioni precedenti di Windows VSS per facilitare il ripristino.

### <a name="before-you-start"></a>Prima di iniziare

1. Nel SQL Server creare una cartella in un'unità con spazio disponibile sufficiente per contenere una singola copia di un backup. Ad esempio: `C:\MABSBACKUP`.

1. Condividi la cartella. Ad esempio, condividere `C:\MABSBACKUP` la cartella come *DPMBACKUP*.

1. Copiare e incollare il comando OSQL seguente nel Blocco note e salvarlo in un file denominato `C:\MABSACKUP\bkupdb.cmd` . Assicurarsi che non sia presente alcuna estensione txt. Modificare i SQL_Instance_name e DPMDB_NAME in modo che corrispondano all'istanza e al nome DPMDB usati dal server MABS.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Con Blocco note aprire il **ScriptingConfig.xml** file che si trova nella `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` cartella nel server MABS.

1. Modificare **ScriptingConfig.xml** e modificare **DataSourceName=** in modo che sia la lettera di unità che contiene la cartella o la condivisione DPMDBBACKUP. Modificare la voce PreBackupScript nel percorso completo e nel nome **di bkupdb.cmd** salvato nel passaggio 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Salvare le modifiche in **ScriptingConfig.xml**.

1. Proteggere la cartella C:\MABSBACKUP o la condivisione usando MABS e attendere la creazione `\sqlservername\MABSBACKUP` della replica iniziale. Dovrebbe essere presente **un file dpmdb.bak** nella cartella C:\MABSBACKUP come risultato dell'esecuzione dello script di pre-backup, che è stato a sua volta copiato nella replica MABS.

1. Se non si abilita il ripristino self-service, sono necessari alcuni passaggi aggiuntivi per condividere la cartella MABSBACKUP nella replica:

    1. Nella console di MABS > **protezione** individuare l'origine dati MABSBACKUP e selezionarla. Nella sezione dei dettagli selezionare Fare clic per **visualizzare i** dettagli sul collegamento al percorso della replica e copiare il percorso nel Blocco note. Rimuovi il percorso di origine e mantieni il percorso di destinazione. Il percorso dovrebbe essere simile al seguente: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Creare una condivisione in tale percorso usando il nome di condivisione **MABSSERVERNAME-DPMDB**. Puoi usare il comando Net Share seguente da un prompt dei comandi amministrativo.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Configurare il backup

È possibile eseguire il backup del database MABS come qualsiasi altro database SQL Server usando SQL Server backup nativo.

- [Panoramica ](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) di backup e ripristino di database SQL Server.

- [Altre informazioni](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sul backup di SQL Server nel cloud.

### <a name="recover-the-mabs-database"></a>Ripristinare il database mabs

1. Connettersi alla `\\MABSServer\MABSSERVERNAME-dpmdb` condivisione usando Esplora risorse da qualsiasi computer Windows.

2. Fare clic con il pulsante destro del mouse sul file **dpmdb.bak** per visualizzare le proprietà. Nella scheda **Versioni precedenti** sono elencati tutti i backup che è possibile selezionare e copiare. Esiste anche l'ultimo backup ancora disponibile nella cartella C:\MABSBACKUP, facilmente accessibile.

3. Se è necessario spostare un disco del pool di archiviazione MABS collegato a SAN in un altro server per essere in grado di leggere dal volume di replica o per reinstallare Windows per leggere i dischi collegati in locale, è necessario conoscere in anticipo il percorso del punto di montaggio del volume di Replica MABS o il GUID del volume per sapere quale volume contiene il backup del database. Puoi usare lo script SQL seguente per estrarre tali informazioni in qualsiasi momento dopo la protezione iniziale, ma prima di dover eseguire il ripristino. Sostituire con `%dpmsqlservername%` il nome del SQL Server che ospita il database.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Se è necessario eseguire il ripristino dopo lo spostamento dei dischi del pool di archiviazione mabs o della ricompilazione di un server mabs:

    1. Si dispone del GUID del volume, pertanto se il volume deve essere montato in un altro server Windows o dopo la ricompilazione di un server maBS, usare **mountvol.exe** per assegnare una lettera di unità usando il GUID del volume dall'output dello script SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Ricondividere la cartella MABSBACKUP nel volume di replica usando la lettera di unità e la parte del percorso di replica che rappresenta la struttura di cartelle.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Connettersi alla `\\SERVERNAME\MABSSERVERNAME-dpmdb` condivisione usando Esplora risorse da qualsiasi computer Windows.

    4. Fare clic con il pulsante destro del mouse sul file **dpmdb.bak** per visualizzare le proprietà. Nella scheda **Versioni precedenti** sono elencati tutti i backup che è possibile selezionare e copiare.

## <a name="using-dpmsync"></a>Uso di DPMSync

**DpmSync** è uno strumento da riga di comando che consente di sincronizzare il database mabs con lo stato dei dischi nel pool di archiviazione e con gli agenti protezione installati. DpmSync ripristina il database del server di Backup di Microsoft Azure, sincronizza il database del server di Backup di Microsoft Azure con le repliche nel pool di archiviazione, ripristina il database di report e rialloca le repliche mancanti.

### <a name="parameters"></a>Parametri

| Parametro      | Descrizione    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Ripristina un database MABS da un percorso specificato.|
| **-Sync**                            | Sincronizza i database ripristinati. È necessario eseguire **DpmSync –Sync** dopo aver ripristinato i database. Dopo aver eseguito **DpmSync –Sync**, alcune repliche potrebbero essere ancora contrassegnate come mancanti. |
| **-DbLoc** *location*                | Identifica il percorso del backup del database MABS.|
| **-InstanceName** <br/>*server\instance*     | Istanza in cui il database DPM deve essere ripristinato.|
| **-ReallocateReplica**         | Rialloca tutti i volumi di replica mancanti senza sincronizzazione. |
| **-DataCopied**                      | Indica che il caricamento dei dati nei volumi di replica appena allocati è stato completato. Si applica solo ai computer client. |

**Esempio 1:** Per ripristinare il database mabs dal supporto di backup locale nel server MABS, eseguire il comando seguente:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Dopo aver ripristinato il database mabs, per sincronizzare i database, eseguire il comando seguente:

```cmd
DpmSync -Sync
```

Dopo aver ripristinato e sincronizzato il database MABS e prima di ripristinare la replica, eseguire il comando seguente per riallocare lo spazio su disco per la replica:

```cmd
DpmSync -ReallocateReplica
```

**Esempio 2:** Per ripristinare il database mabs da un database remoto, eseguire il comando seguente nel computer remoto:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Dopo aver ripristinato il database maBS, per sincronizzare i database, eseguire il comando seguente nel server maBS:

```cmd
DpmSync -Sync
```

Dopo aver ripristinato e sincronizzato il database maBS e prima di ripristinare la replica, eseguire il comando seguente nel server mabs per riallocare lo spazio su disco per la replica:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto di MABS](backup-support-matrix-mabs-dpm.md)
- [Domande frequenti su MABS](backup-azure-dpm-azure-server-faq.yml)