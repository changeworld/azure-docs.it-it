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
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654423"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Eseguire la migrazione dei database da SQL Server a SQL Istanza gestita utilizzando il servizio di riproduzione log
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In questo articolo viene illustrato come configurare manualmente la migrazione del database da SQL Server 2008-2019 a SQL Istanza gestita utilizzando il servizio di riproduzione log (con ridondanza locale). Si tratta di un servizio cloud abilitato per l'istanza gestita basata sulla tecnologia SQL Server log shipping in modalità No Recovery. CON ridondanza locale deve essere usato nei casi in cui non è possibile usare il servizio migrazione dati (DMS), quando è necessario un maggiore controllo o quando esiste una tolleranza minima per i tempi di inattività.

## <a name="when-to-use-log-replay-service"></a>Quando utilizzare il servizio di riproduzione log

Nei casi in cui non è possibile usare [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) per la migrazione, il servizio cloud con ridondanza locale può essere usato direttamente con PowerShell, i cmdlet dell'interfaccia della riga di comando o l'API per compilare e orchestrare manualmente le migrazioni di database nell'istanza gestita di SQL. 

È possibile prendere in considerazione l'uso del servizio cloud con ridondanza locale in alcuni dei casi seguenti:
- È necessario un maggiore controllo per il progetto di migrazione del database
- Esiste una piccola tolleranza per i tempi di inattività sulla migrazione cutover
- Non è possibile installare il file eseguibile DMS nell'ambiente in uso
- L'eseguibile DMS non ha accesso ai file dei backup del database
- Nessun accesso al sistema operativo host disponibile oppure nessun privilegio di amministratore

> [!NOTE]
> Il modo automatizzato consigliato per eseguire la migrazione dei database da SQL Server a SQL Istanza gestita è l'uso di Azure DMS. Questo servizio usa lo stesso servizio cloud con ridondanza locale nel back-end con log shipping in modalità senza ripristino. È necessario considerare manualmente l'uso di con ridondanza locale per orchestrare le migrazioni nei casi in cui Azure DMS non supporta completamente gli scenari.

## <a name="how-does-it-work"></a>Come funziona

La creazione di una soluzione personalizzata usando con ridondanza locale per eseguire la migrazione di un database nel cloud richiede diversi passaggi di orchestrazione mostrati nel diagramma e descritti nella tabella seguente.

La migrazione comporta l'esecuzione di backup completi del database in SQL Server e la copia dei file di backup nell'archiviazione BLOB di Azure. CON ridondanza locale viene usato per ripristinare i file di backup dall'archiviazione BLOB di Azure all'istanza gestita di SQL. L'archivio BLOB di Azure viene usato come risorsa di archiviazione intermedia tra SQL Server e SQL Istanza gestita.

CON ridondanza locale eseguirà il monitoraggio dell'archiviazione BLOB di Azure per qualsiasi nuovo backup differenziale o del log aggiunto dopo il ripristino del backup completo e ripristinerà automaticamente eventuali nuovi file aggiunti. Lo stato di avanzamento dei file di backup ripristinati nell'istanza gestita di SQL può essere monitorato tramite il servizio e, se necessario, il processo può anche essere interrotto. I database da ripristinare durante il processo di migrazione saranno in modalità di ripristino e non potranno essere utilizzati per leggere o scrivere fino al completamento del processo.

CON ridondanza locale può essere avviato in modalità di completamento automatico o in modalità continua. Quando viene avviato in modalità di completamento automatico, la migrazione viene completata automaticamente al ripristino dell'ultimo file di backup specificato. Quando viene avviato in modalità continua, il servizio ripristinerà continuamente tutti i nuovi file di backup aggiunti e la migrazione viene completata solo su cutover manuale. Il passaggio finale cutover renderà i database disponibili per l'uso in lettura e scrittura in SQL Istanza gestita. 

  ![Passaggi dell'orchestrazione del servizio di riproduzione log illustrati per SQL Istanza gestita](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operazione | Dettagli |
| :----------------------------- | :------------------------- |
| **1. copiare i backup del database da SQL Server all'archivio BLOB di Azure**. | -Copiare backup completi, differenziali e del log da SQL Server all'archivio BLOB di Azure usando [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />-Durante la migrazione di diversi database, è necessaria una cartella separata per ogni database. |
| **2. avviare il servizio con ridondanza locale nel cloud**. | -Il servizio può essere avviato con una scelta di cmdlet: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Cmdlet di az_sql_midb_log_replay_start](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)dell'interfaccia della riga di comando. <br /><br />-Una volta avviata, il servizio eseguirà il backup dall'archiviazione BLOB di Azure e avvierà il ripristino in SQL Istanza gestita. <br /> -Dopo il ripristino di tutti i backup caricati inizialmente, il servizio controllerà tutti i nuovi file caricati nella cartella e applicherà in modo continuo i log in base alla catena LSN, fino a quando il servizio non viene arrestato. |
| **2,1. monitorare lo stato dell'operazione**. | -Lo stato di avanzamento dell'operazione di ripristino può essere monitorato con una scelta di cmdlet o: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Cmdlet di az_sql_midb_log_replay_show](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)dell'interfaccia della riga di comando. |
| **2,2. Stop\abort l'operazione, se necessario**. | -Se il processo di migrazione deve essere interrotto, l'operazione può essere interrotta con una scelta di cmdlet: <br /> [Arresto di PowerShell-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Cmdlet di [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) dell'interfaccia della riga di comando. <br /><br />-Questo comporterà l'eliminazione del database ripristinato in SQL Istanza gestita. <br />-Una volta arrestata, non è possibile continuare con ridondanza locale per un database. Il processo di migrazione deve essere riavviato da zero. |
| **3. cutover nel cloud quando è pronto**. | -Dopo il ripristino di tutti i backup in SQL Istanza gestita, completare il cutover avviando l'operazione con ridondanza locale complete con una scelta di chiamata API o cmdlet: <br />PowerShell [complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Cmdlet di [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) dell'interfaccia della riga di comando. <br /><br />-Questo provocherà l'arresto del servizio con ridondanza locale e il recupero del database in Istanza gestita. <br />: Consente di reindirizzare la stringa di connessione dell'applicazione da SQL Server a SQL Istanza gestita. <br />-Il database di completamento dell'operazione è disponibile per le operazioni di R/W nel cloud. |

## <a name="requirements-for-getting-started"></a>Requisiti per la Guida introduttiva

### <a name="sql-server-side"></a>Lato SQL Server
- SQL Server 2008-2019
- Backup completo dei database (uno o più file)
- Backup differenziale (uno o più file)
- Backup del log (non suddiviso per il file di log delle transazioni)
- Il **checksum deve essere abilitato** come obbligatorio

### <a name="azure-side"></a>Lato Azure
-   PowerShell AZ. SQL Module versione 2.16.0 o successiva ([installare](https://www.powershellgallery.com/packages/Az.Sql/)o usare Azure [cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
-   INTERFACCIA della riga di comando versione 2.19.0 o successiva ([installazione](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   Archiviazione BLOB di Azure con provisioning
-   Token di sicurezza SAS con autorizzazioni di **sola lettura** ed **elenco** generate per l'archiviazione BLOB

## <a name="best-practices"></a>Procedure consigliate

Di seguito sono riportate le procedure consigliate:
- Eseguire [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) per convalidare i database non verrà eseguita la migrazione a SQL istanza gestita. 
- Suddividere i backup completi e differenziali in più file, anziché in un singolo file.
- Abilitare la compressione dei backup.
- Usare Cloud Shell per eseguire gli script perché verranno sempre aggiornati ai cmdlet più recenti rilasciati.
- Pianificare il completamento della migrazione entro 47 ore dall'avvio del servizio con ridondanza locale.

> [!IMPORTANT]
> - Impossibile utilizzare il database da ripristinare utilizzando con ridondanza locale fino al completamento del processo di migrazione. Ciò è dovuto al fatto che la tecnologia sottostante è log shipping in modalità non di ripristino.
> - La modalità standby per log shipping non è supportata da con ridondanza locale a causa delle differenze di versione tra SQL Istanza gestita e la versione più recente del SQL Server di mercato.

## <a name="steps-to-execute"></a>Passaggi da eseguire

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Copiare i backup da SQL Server all'archivio BLOB di Azure

I due approcci seguenti possono essere usati per copiare i backup nell'archiviazione BLOB nella migrazione dei database a Istanza gestita usando con ridondanza locale:
- Uso di SQL Server la funzionalità [di backup nativo in URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Copia dei backup nel contenitore BLOB usando [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)o [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Creare un BLOB di Azure e un token di autenticazione SAS

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
7. Verificare che siano selezionate le autorizzazioni di sola lettura ed elenco
8. Fare clic su Crea
9. Copiare il token che inizia con "SV =" nell'URI per usarlo nel codice

> [!IMPORTANT]
> Le autorizzazioni per il token SAS per l'archiviazione BLOB di Azure devono essere lette e solo elencate. Nel caso di qualsiasi altra autorizzazione concessa per il token di autenticazione SAS, l'avvio del servizio con ridondanza locale non riuscirà. Questi requisiti di sicurezza sono di progettazione.

## <a name="log-in-to-azure-and-select-subscription"></a>Accedere ad Azure e selezionare sottoscrizione

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Avviare con ridondanza locale in modalità di completamento automatico-esempio dell'interfaccia della riga di comando:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Avviare con ridondanza locale in modalità continua

Avviare con ridondanza locale in modalità continua-esempio di PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Avviare con ridondanza locale in modalità continua-esempio dell'interfaccia della riga di comando:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Dopo l'avvio di con ridondanza locale, tutte le patch del software gestito dal sistema verranno interrotte per le prossime 47 ore. Al passaggio di questa finestra, la prossima patch software automatizzata arresterà automaticamente il con ridondanza locale in corso. In tal caso, non è possibile riprendere la migrazione ed è necessario riavviarla da zero. 

## <a name="monitor-the-migration-progress"></a>Monitorare lo stato di avanzamento della migrazione

Per monitorare lo stato dell'operazione di migrazione, usare il comando di PowerShell seguente:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per monitorare lo stato dell'operazione di migrazione, usare il comando dell'interfaccia della riga di comando seguente:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Arrestare la migrazione

Se è necessario arrestare la migrazione, usare i cmdlet seguenti. L'arresto della migrazione eliminerà il database di ripristino nell'istanza gestita di SQL perché non sarà possibile riprendere la migrazione.

Per stop\abort il processo di migrazione, usare il comando di PowerShell seguente:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per stop\abort il processo di migrazione, usare il comando dell'interfaccia della riga di comando seguente:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Completamento della migrazione (modalità continua)

Nel caso in cui con ridondanza locale venga avviato in modalità continua, dopo aver verificato che tutti i backup siano stati ripristinati, l'avvio del cutover completerà la migrazione. Al termine del cutover, verrà eseguita la migrazione del database e sarà possibile accedere in lettura e scrittura.

Per completare il processo di migrazione in modalità continua con ridondanza locale, usare il comando di PowerShell seguente:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

Per completare il processo di migrazione in modalità continua con ridondanza locale, usare il comando dell'interfaccia della riga di comando seguente:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [migrazione di SQL Server a un'istanza gestita di SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Altre informazioni sulle [differenze tra SQL Server e istanza gestita SQL di Azure](transact-sql-tsql-differences-sql-server.md).
- Altre informazioni sulle [procedure consigliate per i carichi di lavoro relativi a costi e dimensioni migrati in Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
