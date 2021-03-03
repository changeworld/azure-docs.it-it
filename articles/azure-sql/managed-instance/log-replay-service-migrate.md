---
title: Eseguire la migrazione di database a SQL Istanza gestita tramite il servizio di riproduzione log
description: Informazioni su come eseguire la migrazione dei database da SQL Server a SQL Istanza gestita tramite il servizio di riproduzione log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661359"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Eseguire la migrazione dei database da SQL Server a SQL Istanza gestita utilizzando il servizio di riproduzione log
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In questo articolo viene illustrato come configurare manualmente la migrazione del database da SQL Server 2008-2019 a SQL Istanza gestita utilizzando il servizio di riproduzione log (con ridondanza locale). Si tratta di un servizio cloud abilitato per Istanza gestita in base alla tecnologia di log shipping SQL Server. CON ridondanza locale deve essere usato nei casi in cui non è possibile usare il servizio migrazione dati di Azure (DMS), quando è necessario un maggiore controllo o quando esiste una tolleranza minima per i tempi di inattività.

## <a name="when-to-use-log-replay-service"></a>Quando utilizzare il servizio di riproduzione log

Nei casi in cui non è possibile usare [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) per la migrazione, il servizio cloud con ridondanza locale può essere usato direttamente con PowerShell, i cmdlet dell'interfaccia della riga di comando o l'API per compilare e orchestrare manualmente le migrazioni di database a SQL istanza gestita. 

È possibile prendere in considerazione l'uso del servizio cloud con ridondanza locale in alcuni dei casi seguenti:
- È necessario un maggiore controllo per il progetto di migrazione del database
- Esiste una piccola tolleranza per i tempi di inattività sulla migrazione cutover
- Non è possibile installare il file eseguibile DMS nell'ambiente in uso
- L'eseguibile DMS non ha accesso ai file dei backup del database
- Nessun accesso al sistema operativo host disponibile oppure nessun privilegio di amministratore
- Non è possibile aprire le porte di rete dall'ambiente in Azure

> [!NOTE]
> Il modo automatizzato consigliato per eseguire la migrazione dei database da SQL Server a SQL Istanza gestita è l'uso di Azure DMS. Questo servizio usa lo stesso servizio cloud con ridondanza locale nel back-end con log shipping in modalità NORECOVERY. È necessario considerare manualmente l'uso di con ridondanza locale per orchestrare le migrazioni nei casi in cui Azure DMS non supporta completamente gli scenari.

## <a name="how-does-it-work"></a>Come funziona

La creazione di una soluzione personalizzata usando con ridondanza locale per eseguire la migrazione dei database nel cloud richiede diversi passaggi di orchestrazione mostrati nel diagramma e descritti nella tabella seguente.

La migrazione prevede l'esecuzione di backup completi del database in SQL Server con CHECKSUM abilitato e la copia dei file di backup nell'archiviazione BLOB di Azure. CON ridondanza locale viene usato per ripristinare i file di backup dall'archiviazione BLOB di Azure a SQL Istanza gestita. L'archivio BLOB di Azure viene usato come risorsa di archiviazione intermedia tra SQL Server e SQL Istanza gestita.

CON ridondanza locale eseguirà il monitoraggio dell'archiviazione BLOB di Azure per qualsiasi nuovo backup differenziale o del log aggiunto dopo il ripristino del backup completo e ripristinerà automaticamente eventuali nuovi file aggiunti. Lo stato di avanzamento dei file di backup da ripristinare in SQL Istanza gestita può essere monitorato tramite il servizio e, se necessario, il processo può anche essere interrotto.

CON ridondanza locale non richiede una convenzione di denominazione dei file di backup specifica durante l'analisi di tutti i file presenti nell'archivio BLOB di Azure e crea la catena di backup leggendo solo le intestazioni di file. I database si trovano nello stato "ripristino" durante il processo di migrazione, perché vengono ripristinati in modalità [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) e non possono essere utilizzati per la lettura o la scrittura finché il processo di migrazione non è stato completato completamente. 

Quando si esegue la migrazione di diversi database, i backup per ogni database devono essere inseriti in una cartella separata nell'archivio BLOB di Azure. CON ridondanza locale deve essere avviato separatamente per ogni database e devono essere specificati percorsi diversi per separare le cartelle di archiviazione BLOB di Azure. 

CON ridondanza locale può essere avviato in modalità di completamento automatico o in modalità continua. Quando viene avviato in modalità di completamento automatico, la migrazione viene completata automaticamente quando è stato ripristinato l'ultimo nome del file di backup specificato. Quando viene avviato in modalità continua, il servizio ripristinerà continuamente tutti i nuovi file di backup aggiunti e la migrazione viene completata solo su cutover manuale. Si consiglia di eseguire il cutover manuale solo dopo che è stato eseguito il backup della parte finale del log e viene visualizzato come ripristinato in SQL Istanza gestita. Il passaggio finale cutover renderà il database online e disponibile per l'uso in lettura e scrittura in SQL Istanza gestita.

Quando con ridondanza locale viene arrestato, automaticamente in caso di completamento automatico o manualmente in cutover, non è possibile riprendere il processo di ripristino per un database portato online in SQL Istanza gestita. Per ripristinare i file di backup aggiuntivi dopo che la migrazione è stata completata tramite il completamento automatico o manualmente in cutover, è necessario eliminare il database e l'intera catena di backup deve essere ripristinata da zero riavviando il con ridondanza locale.

  ![Passaggi dell'orchestrazione del servizio di riproduzione log illustrati per SQL Istanza gestita](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operazione | Dettagli |
| :----------------------------- | :------------------------- |
| **1. copiare i backup del database da SQL Server all'archivio BLOB di Azure**. | -Copiare backup completi, differenziali e del log da SQL Server al contenitore di archiviazione BLOB di Azure usando [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />-Usare i nomi di file, perché con ridondanza locale non richiede una convenzione di denominazione dei file specifica.<br />-Durante la migrazione di diversi database, è necessaria una cartella separata per ogni database. |
| **2. avviare il servizio con ridondanza locale nel cloud**. | -Il servizio può essere avviato con una scelta di cmdlet: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Cmdlet di az_sql_midb_log_replay_start](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)dell'interfaccia della riga di comando. <br /> -Avviare con ridondanza locale separatamente per ogni database diverso che punta a una cartella di backup diversa nell'archivio BLOB di Azure. <br />-Una volta avviata, il servizio eseguirà il backup dal contenitore di archiviazione BLOB di Azure e avvierà il ripristino in SQL Istanza gestita.<br /> -Nel caso in cui con ridondanza locale sia stato avviato in modalità continua, una volta ripristinati tutti i backup caricati inizialmente, il servizio controllerà tutti i nuovi file caricati nella cartella e applicherà continuamente i log in base alla catena LSN, fino a quando il servizio non viene arrestato. |
| **2,1. monitorare lo stato dell'operazione**. | -Lo stato di avanzamento dell'operazione di ripristino può essere monitorato con una scelta di cmdlet o: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Cmdlet di az_sql_midb_log_replay_show](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)dell'interfaccia della riga di comando. |
| **2,2. Stop\abort l'operazione, se necessario**. | -Se il processo di migrazione deve essere interrotto, l'operazione può essere interrotta con una scelta di cmdlet: <br /> [Arresto di PowerShell-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Cmdlet di [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) dell'interfaccia della riga di comando. <br /><br />-Questo comporterà l'eliminazione del database da ripristinare in SQL Istanza gestita. <br />-Una volta arrestata, non è possibile riprendere con ridondanza locale per un database. Il processo di migrazione deve essere riavviato da zero. |
| **3. cutover nel cloud quando è pronto**. | -Dopo il ripristino di tutti i backup in SQL Istanza gestita, completare il cutover avviando l'operazione con ridondanza locale complete con una scelta di cmdlet: <br />PowerShell [complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Cmdlet di [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) dell'interfaccia della riga di comando. <br /><br />-In questo modo il servizio con ridondanza locale verrà arrestato e il database tornerà online per l'uso in lettura e scrittura su SQL Istanza gestita.<br /> : Consente di reindirizzare la stringa di connessione dell'applicazione da SQL Server a SQL Istanza gestita. |

## <a name="requirements-for-getting-started"></a>Requisiti per la Guida introduttiva

### <a name="sql-server-side"></a>Lato SQL Server
- SQL Server 2008-2019
- Backup completo dei database (uno o più file)
- Backup differenziale (uno o più file)
- Backup del log (non suddiviso per il file di log delle transazioni)
- **È necessario abilitare il checksum** per i backup (obbligatorio)

### <a name="azure-side"></a>Lato Azure
- PowerShell AZ. SQL Module versione 2.16.0 o successiva ([installare](https://www.powershellgallery.com/packages/Az.Sql/)o usare Azure [cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
- INTERFACCIA della riga di comando versione 2.19.0 o successiva ([installazione](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Provisioning del contenitore di archiviazione BLOB di Azure
- Token di sicurezza SAS con autorizzazioni di **sola lettura** ed **elenco** generate per il contenitore di archiviazione BLOB

### <a name="migrating-multiple-databases"></a>Migrazione di più database
- I file di backup per database diversi devono essere posizionati in cartelle separate nell'archivio BLOB di Azure.
- CON ridondanza locale deve essere avviato separatamente per ogni database che punta a una cartella appropriata nell'archivio BLOB di Azure.
- CON ridondanza locale può supportare fino a 100 processi di ripristino simultanei per singolo SQL Istanza gestita.

### <a name="azure-rbac-permissions-required"></a>Autorizzazioni RBAC di Azure richieste
Per eseguire con ridondanza locale tramite i client forniti è necessario uno dei seguenti ruoli di Azure:
- Ruolo di proprietario della sottoscrizione o
- [Istanza gestita ruolo Collaboratore](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) o
- Ruolo personalizzato con l'autorizzazione seguente:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Procedure consigliate

Di seguito sono riportate le procedure consigliate:
- Eseguire [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) per convalidare che i database siano pronti per la migrazione a SQL istanza gestita. 
- Suddividere i backup completi e differenziali in più file, anziché in un singolo file.
- Abilitare la compressione dei backup.
- Usare Cloud Shell per eseguire gli script perché verranno sempre aggiornati ai cmdlet più recenti rilasciati.
- Pianificare il completamento della migrazione entro 47 ore dall'avvio del servizio con ridondanza locale. Si tratta di un periodo di tolleranza che impedisce patch software gestite dal sistema dopo l'avvio di con ridondanza locale.

> [!IMPORTANT]
> - Impossibile utilizzare il database da ripristinare utilizzando con ridondanza locale fino al completamento del processo di migrazione. Ciò è dovuto al fatto che la tecnologia sottostante viene ripristinata in modalità NORECOVERY.
> - La modalità di ripristino STANDBY che consente l'accesso in sola lettura ai database durante la migrazione non è supportata da con ridondanza locale a causa delle differenze tra le versioni tra SQL Istanza gestita e SQL Server nel mercato.
> - Una volta completata la migrazione tramite il completamento automatico o cutover manuale, il processo di migrazione viene finalizzato poiché con ridondanza locale non supporta il ripristino della ripresa.

## <a name="steps-to-execute"></a>Passaggi da eseguire

### <a name="make-backups-on-the-sql-server"></a>Eseguire i backup nel SQL Server

I backup nel SQL Server possono essere effettuati con una delle due opzioni seguenti:

- Eseguire il backup nell'archiviazione su disco locale, quindi caricare i file nell'archiviazione BLOB di Azure, nel caso in cui l'ambiente sia limitato al backup diretto nell'archiviazione BLOB di Azure.
- Eseguire il backup direttamente nell'archivio BLOB di Azure con l'opzione "TO URL" in T-SQL, nel caso in cui l'ambiente e le procedure di sicurezza consentano di eseguire questa operazione. 

Impostare i database di cui si desidera eseguire la migrazione alla modalità di recupero con registrazione completa per consentire i backup del log.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Per eseguire manualmente il backup completo, diff e log del database nella risorsa di archiviazione locale, usare gli script T-SQL di esempio indicati di seguito. Verificare che l'opzione CHECKSUM sia abilitata perché questo è un requisito obbligatorio per con ridondanza locale.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

I file sottoposti a backup nello spazio di archiviazione locale dovranno essere caricati nell'archivio BLOB di Azure. Nel caso in cui i criteri aziendali lo consentano, il modo alternativo per eseguire i backup direttamente nell'archiviazione BLOB di Azure è documentato nell'esercitazione seguente: [usare il servizio di archiviazione BLOB di Azure con SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). Se si usa questo approccio alternativo, assicurarsi che tutti i backup vengano eseguiti con l'opzione CHECKSUM abilitata.

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Copiare i backup da SQL Server all'archivio BLOB di Azure

È possibile usare alcuni degli approcci seguenti per caricare i backup nell'archiviazione BLOB nella migrazione dei database all'istanza gestita usando con ridondanza locale:
- Uso di SQL Server la funzionalità [di backup nativo in URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Uso di [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)o [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) per caricare i backup in un contenitore BLOB.
- Utilizzo di Storage Explorer in portale di Azure.

### <a name="create-azure-blob-and-sas-authentication-token"></a>Creare un BLOB di Azure e un token di autenticazione SAS

L'archivio BLOB di Azure viene usato come risorsa di archiviazione intermedia per i file di backup tra SQL Server e SQL Istanza gestita. Per creare un contenitore di archiviazione BLOB di Azure, seguire questa procedura:

1. [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Creta un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) nell'account di archiviazione

Una volta creato un contenitore BLOB, generare un token di autenticazione SAS con autorizzazioni di lettura ed elenco solo seguendo questa procedura:

1. Accedere all'account di archiviazione usando portale di Azure
2. Passa a Storage Explorer
3. Espandere contenitori BLOB
4. Fare clic con il pulsante destro del mouse sul contenitore BLOB
5. Selezionare Ottieni firma di accesso condiviso
6. Selezionare l'intervallo di tempo per la scadenza del token. Verificare che il token sia valido per la durata della migrazione.
    - Si noti che il fuso orario del token e del Istanza gestita SQL potrebbe non corrispondere. Verificare che il token di firma di accesso condiviso disponga della validità del tempo appropriata per tenere in considerazione i fusi orari. Se possibile, impostare il fuso orario su un'ora precedente e successiva della finestra di migrazione pianificata.
8. Verificare che siano selezionate le autorizzazioni di sola lettura ed elenco
9. Fare clic su Crea
10. Copiare il token dopo il punto interrogativo "?" e poi. Il token di firma di accesso condiviso in genere inizia con "SV = 2020-10" nell'URI per l'uso nel codice.

> [!IMPORTANT]
> - Le autorizzazioni per il token SAS per l'archiviazione BLOB di Azure devono essere lette e solo elencate. Nel caso di qualsiasi altra autorizzazione concessa per il token di autenticazione SAS, l'avvio del servizio con ridondanza locale non riuscirà. Questi requisiti di sicurezza sono di progettazione.
> - Il token deve avere la validità dell'ora appropriata. Assicurarsi che vengano presi in considerazione i fusi orari tra il token e l'istanza gestita.
> - Verificare che il token venga copiato a partire da "SV = 2020-10..." fino alla fine della stringa.

### <a name="log-in-to-azure-and-select-subscription"></a>Accedere ad Azure e selezionare sottoscrizione

Usare il cmdlet di PowerShell seguente per accedere ad Azure:

```powershell
Login-AzAccount
```

Selezionare la sottoscrizione appropriata in cui si trova il Istanza gestita SQL usando il cmdlet di PowerShell seguente:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Avviare la migrazione

La migrazione viene avviata avviando il servizio con ridondanza locale. Il servizio può essere avviato in modalità di completamento automatico o in modalità continua. Quando viene avviato in modalità di completamento automatico, la migrazione viene completata automaticamente al ripristino dell'ultimo file di backup specificato. Questa opzione richiede il comando avvia per specificare il nome file dell'ultimo file di backup. Quando con ridondanza locale viene avviato in modalità continua, il servizio ripristinerà continuamente eventuali nuovi file di backup aggiunti e la migrazione verrà completata solo su cutover manuale. 

### <a name="start-lrs-in-autocomplete-mode"></a>Avviare con ridondanza locale in modalità di completamento automatico

Per avviare il servizio con ridondanza locale in modalità di completamento automatico, usare i comandi seguenti di PowerShell o dell'interfaccia della riga di comando. Specificare l'ultimo nome del file di backup con il parametro-LastBackupName. Al momento del ripristino dell'ultimo nome del file di backup specificato, il servizio avvierà automaticamente un cutover.

Avviare con ridondanza locale in modalità di completamento automatico-esempio di PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Avviare con ridondanza locale in modalità di completamento automatico-esempio dell'interfaccia della riga di comando:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Avviare con ridondanza locale in modalità continua

Avviare con ridondanza locale in modalità continua-esempio di PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Avviare con ridondanza locale in modalità continua-esempio dell'interfaccia della riga di comando:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Dopo l'avvio di con ridondanza locale, tutte le patch del software gestito dal sistema verranno interrotte per le prossime 47 ore. Al passaggio di questa finestra, la prossima patch software automatizzata arresterà automaticamente il con ridondanza locale in corso. In tal caso, non è possibile riprendere la migrazione ed è necessario riavviarla da zero. 

## <a name="monitor-the-migration-progress"></a>Monitorare lo stato di avanzamento della migrazione

Per monitorare lo stato dell'operazione di migrazione, usare il comando di PowerShell seguente:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per monitorare lo stato dell'operazione di migrazione, usare il comando dell'interfaccia della riga di comando seguente:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Arrestare la migrazione

Se è necessario arrestare la migrazione, usare i cmdlet seguenti. L'arresto della migrazione eliminerà il database di ripristino in SQL Istanza gestita a causa del quale non sarà possibile riprendere la migrazione.

Per stop\abort il processo di migrazione, usare il comando di PowerShell seguente:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per stop\abort il processo di migrazione, usare il comando dell'interfaccia della riga di comando seguente:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Completamento della migrazione (modalità continua)

Nel caso in cui con ridondanza locale venga avviato in modalità continua, dopo aver verificato che tutti i backup siano stati ripristinati, l'avvio del cutover completerà la migrazione. Al termine del cutover, verrà eseguita la migrazione del database e sarà possibile accedere in lettura e scrittura.

Per completare il processo di migrazione in modalità continua con ridondanza locale, usare il comando di PowerShell seguente:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Per completare il processo di migrazione in modalità continua con ridondanza locale, usare il comando dell'interfaccia della riga di comando seguente:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Una volta avviato il con ridondanza locale, usare i cmdlet di monitoraggio (Get-azsqlinstancedatabaselogreplay o az_sql_midb_log_replay_show) per visualizzare lo stato dell'operazione. Se dopo qualche tempo con ridondanza locale non riesce a iniziare con un errore, verificare la presenza di alcuni dei problemi più comuni:
- Il backup del database nel SQL Server è stato eseguito con l'opzione **checksum** ?
- Le autorizzazioni per il token SAS sono **lette** ed **elencate** solo per il servizio con ridondanza locale?
- Il token di firma di accesso condiviso per con ridondanza locale è stato copiato iniziando dopo il punto interrogativo "?" con contenuto iniziale simile a "SV = 2020-02-10..."? 
- Il tempo di **validità del token** di firma di accesso condiviso è applicabile per l'intervallo di tempo di avvio e completamento della migrazione? Si noti che potrebbero verificarsi mancate corrispondenze a causa dei diversi **fusi orari** usati per SQL istanza gestita e il token SAS. Provare a rigenerare il token SAS estendendo la validità del token della finestra temporale prima e dopo la data corrente.
- Il nome del database, il nome del gruppo di risorse e il nome dell'istanza gestita sono stati digitati correttamente?
- Se con ridondanza locale è stato avviato in modalità di completamento automatico, era un nome file valido per l'ultimo file di backup specificato?

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [migrazione di SQL Server a un'istanza gestita di SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Altre informazioni sulle [differenze tra SQL Server e istanza gestita SQL di Azure](transact-sql-tsql-differences-sql-server.md).
- Altre informazioni sulle [procedure consigliate per i carichi di lavoro relativi a costi e dimensioni migrati in Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
