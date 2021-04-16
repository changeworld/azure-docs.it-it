---
title: Eseguire la migrazione di database a sql Istanza gestita tramite il servizio Log Replay
description: Informazioni su come eseguire la migrazione di database da SQL Server a sql Istanza gestita usando il servizio Disaserzione log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484031"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Eseguire la migrazione di database da SQL Server a sql Istanza gestita tramite il servizio Disaserzione log (anteprima)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra come configurare manualmente la migrazione del database da SQL Server 2008-2019 a Istanza gestita di SQL di Azure usando il Servizio di riproduzione log, attualmente in anteprima pubblica. L'archiviazione con archiviazione con accesso in tempo Istanza gestita è un servizio cloud abilitato per SQL SQL Server log shipping. 

[Servizio Migrazione del database di Azure](../../dms/tutorial-sql-server-to-managed-instance.md) e LRS usano la stessa tecnologia di migrazione sottostante e le stesse API. Il rilascio dell'archiviazione con archiviazione con accesso in locale consente di abilitare migrazioni personalizzate complesse e l'architettura ibrida tra istanze locali SQL Server SQL Istanza gestita.

## <a name="when-to-use-log-replay-service"></a>Quando usare il servizio Riesecuzione log

Quando non è possibile usare Servizio Migrazione del database di Azure per la migrazione, è possibile usare LRS direttamente con PowerShell, i cmdlet dell'interfaccia della riga di comando di Azure o le API per compilare e orchestrare manualmente le migrazioni di database a SQL Istanza gestita. 

È possibile prendere in considerazione l'uso dell'LRS nei casi seguenti:
- È necessario un maggiore controllo per il progetto di migrazione del database.
- La tolleranza per i tempi di inattività per il cutover della migrazione è bassa.
- Il file eseguibile del Servizio Migrazione del database non può essere installato nell'ambiente in uso.
- Il file eseguibile del Servizio Migrazione del database non ha accesso ai file per i backup del database.
- Non è disponibile alcun accesso al sistema operativo host o non sono disponibili privilegi di amministratore.
- Non è possibile aprire le porte di rete dall'ambiente in Azure.
- Limitazione della larghezza di banda della rete o problemi di blocco del proxy nell'ambiente.
- I backup vengono archiviati direttamente Archiviazione BLOB di Azure tramite `TO URL` l'opzione .
- È necessario usare backup differenziali.

> [!NOTE]
> È consigliabile automatizzare la migrazione dei database da SQL Server a sql Istanza gestita tramite Servizio Migrazione del database. Questo servizio usa lo stesso servizio cloud LRS nel back-end, con log shipping `NORECOVERY` modalità. È consigliabile usare manualmente LRS per orchestrare le migrazioni quando il Servizio Migrazione del database non supporta completamente gli scenari.

## <a name="how-it-works"></a>Funzionamento

La compilazione di una soluzione personalizzata usando LRS per eseguire la migrazione dei database nel cloud richiede diversi passaggi di orchestrazione, come illustrato nel diagramma e nella tabella più avanti in questa sezione.

La migrazione consiste nell'eseguire backup completi del database SQL Server con abilitato e copiare i `CHECKSUM` file di backup Archiviazione BLOB di Azure. LRS viene usato per ripristinare i file di backup da Archiviazione BLOB a SQL Istanza gestita. L'archiviazione BLOB è un'archiviazione intermedia tra SQL Server e sql Istanza gestita.

LRS monitora l'archiviazione BLOB per eventuali nuovi backup differenziali o di log aggiunti dopo il ripristino del backup completo. LRS ripristina quindi automaticamente questi nuovi file. È possibile usare il servizio per monitorare lo stato dei file di backup ripristinati in SQL Istanza gestita ed è possibile arrestare il processo, se necessario.

LRS non richiede una convenzione di denominazione specifica per i file di backup. Analizza tutti i file inseriti in Archiviazione BLOB e costruisce la catena di backup leggendo solo le intestazioni dei file. I database sono in uno stato di "ripristino" durante il processo di migrazione. I database vengono ripristinati in modalità [NORECOVERY,](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) quindi non possono essere usati per la lettura o la scrittura fino al completamento del processo di migrazione. 

Se si esegue la migrazione di diversi database, è necessario:
 
- Inserire i backup per ogni database in una cartella separata in Archiviazione BLOB.
- Avviare LRS separatamente per ogni database.
- Specificare percorsi diversi per separare le cartelle di Archiviazione BLOB. 

È possibile avviare LRS in *modalità completamento automatico* *o* continua. Quando viene avviata in modalità di completamento automatico, la migrazione verrà completata automaticamente quando è stato ripristinato l'ultimo dei file di backup specificati. Quando si avvia LRS in modalità continua, il servizio ripristini continuamente tutti i nuovi file di backup aggiunti e la migrazione terminerà solo con il cutover manuale. 

È consigliabile eseguire manualmente il cut over dopo che è stato eseguito il backup finale della parte finale del log e viene visualizzato come ripristinato in SQL Istanza gestita. Il passaggio finale del cutover rende il database online e disponibile per l'uso in lettura e scrittura in SQL Istanza gestita.

Dopo l'arresto dell'istanza di LRS, automaticamente tramite completamento automatico o manualmente tramite cutover, non è possibile riprendere il processo di ripristino per un database portato online in SQL Istanza gestita. Per ripristinare file di backup aggiuntivi al termine della migrazione tramite completamento automatico o cutover, è necessario eliminare il database. È anche necessario ripristinare l'intera catena di backup da zero riavviando LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagramma che illustra i passaggi di orchestrazione del servizio Riesecuzione log per SQL Istanza gestita." border="false":::
    
| Operazione | Dettagli |
| :----------------------------- | :------------------------- |
| **1. Copiare i backup del database SQL Server nell'archivio BLOB.** | Copiare backup completi, differenziali e del log da SQL Server a un contenitore di archiviazione BLOB usando [Azcopy](../../storage/common/storage-use-azcopy-v10.md) [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Usare qualsiasi nome di file. L'LRS non richiede una convenzione di denominazione dei file specifica.<br /><br />Durante la migrazione di diversi database, è necessaria una cartella separata per ogni database. |
| **2. Avviare LRS nel cloud.** | È possibile riavviare il servizio con una scelta di[cmdlet:](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure ( az_sql_midb_log_replay_start cmdlet ). <br /><br /> Avviare LRS separatamente per ogni database che punta a una cartella di backup nell'archiviazione BLOB. <br /><br /> Dopo l'avvio, il servizio esegue i backup dal contenitore di archiviazione BLOB e avvia il ripristino in SQL Istanza gestita.<br /><br /> Se l'archiviazione con archiviazione con registrazione locale è stata avviata in modalità continua, dopo il ripristino di tutti i backup caricati inizialmente, il servizio verifica la disponibilità di eventuali nuovi file caricati nella cartella. Il servizio applicherà continuamente i log in base alla catena di numeri di sequenza del file di log (LSN) fino a quando non viene arrestato. |
| **2.1. Monitorare lo stato dell'operazione**. | È possibile monitorare lo stato dell'operazione di ripristino con una scelta di[cmdlet:](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)PowerShell ([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure ( az_sql_midb_log_replay_show cmdlet ). |
| **2.2. Arrestare l'operazione se necessario.** | Se è necessario arrestare il processo di migrazione, è possibile scegliere tra cmdlet: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) o interfaccia della riga di comando di Azure ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> L'arresto dell'operazione eliminerà il database da ripristinare in SQL Istanza gestita. Dopo l'arresto di un'operazione, non è possibile riprendere LRS per un database. È necessario riavviare il processo di migrazione da zero. |
| **3. Eseguire il cut over nel cloud quando si è pronti.** | Arrestare l'applicazione e il carico di lavoro. Eseguire l'ultimo backup della parte finale del log e caricarlo Archiviazione BLOB di Azure.<br /><br /> Completare il cutover avviando un'operazione LRS con una scelta di `complete` cmdlet: PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) o interfaccia della riga di comando di Azure [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Questa operazione arresta LRS e fa sì che il database venga online per l'uso in lettura e scrittura in SQL Istanza gestita.<br /><br /> Ridefinire la stringa di connessione dell'applicazione SQL Server a SQL Istanza gestita. È necessario orchestrare questo passaggio manualmente, tramite una modifica manuale della stringa di connessione nell'applicazione o automaticamente (ad esempio, se l'applicazione può, ad esempio, leggere la stringa di connessione da una proprietà o da un database). |

## <a name="requirements-for-getting-started"></a>Requisiti per iniziare

### <a name="sql-server-side"></a>SQL Server lato
- SQL Server 2008-2019
- Backup completo dei database (uno o più file)
- Backup differenziale (uno o più file)
- Backup del log (non suddiviso per un file di log delle transazioni)
- `CHECKSUM` abilitato per i backup (obbligatorio)

### <a name="azure-side"></a>Lato Azure
- Modulo Az.SQL di PowerShell versione 2.16.0 o successiva[(](https://www.powershellgallery.com/packages/Az.Sql/) installato o accessibile [tramite Azure Cloud Shell](/azure/cloud-shell/))
- Interfaccia della riga di comando di Azure versione 2.19.0 o successiva[(installato)](/cli/azure/install-azure-cli)
- Archiviazione BLOB di Azure provisioning del contenitore
- Token di sicurezza di firma di accesso condiviso con autorizzazioni di lettura ed elenco generate per il contenitore di archiviazione BLOB

### <a name="migration-of-multiple-databases"></a>Migrazione di più database
È necessario inserire i file di backup per database diversi in cartelle separate nell'archivio BLOB.

Avviare LRS separatamente per ogni database puntando a una cartella appropriata nell'archivio BLOB. L'LRS può supportare fino a 100 processi di ripristino simultanei per ogni singola istanza gestita.

### <a name="azure-rbac-permissions"></a>Autorizzazioni del controllo degli accessi in base al ruolo di Azure
L'esecuzione dell'archiviazione con accesso in remoto tramite i client forniti richiede uno dei ruoli di Azure seguenti:
- Ruolo di Proprietario della sottoscrizione
- [Istanza gestita collaboratore](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Ruolo personalizzato con l'autorizzazione seguente: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Procedure consigliate

Si consiglia di ricorrere alle seguenti procedure:
- Eseguire [Data Migration Assistant](/sql/dma/dma-overview) per verificare che i database siano pronti per la migrazione a SQL Istanza gestita. 
- Suddividere i backup completi e differenziali in più file, anziché usare un singolo file.
- Abilitare la compressione dei backup.
- Usare Cloud Shell per eseguire gli script, perché verrà sempre aggiornato ai cmdlet più recenti rilasciati.
- Pianificare il completamento della migrazione entro 47 ore dall'avvio dell'istanza di LRS. Si tratta di un periodo di tolleranza che impedisce l'installazione di patch software gestite dal sistema.

> [!IMPORTANT]
> - Non è possibile usare il database ripristinato tramite LRS fino al termine del processo di migrazione. 
> - L'archiviazione con accesso in lettura non supporta l'accesso in sola lettura ai database durante la migrazione.
> - Al termine della migrazione, il processo di migrazione viene finalizzato perché LRS non supporta la ripresa del processo di ripristino.

## <a name="steps-to-execute"></a>Passaggi da eseguire

### <a name="make-backups-of-sql-server"></a>Eseguire backup di SQL Server

È possibile eseguire backup di SQL Server usando una delle opzioni seguenti:

- Eseguire il backup nell'archiviazione su disco locale e quindi caricare i file Archiviazione BLOB di Azure, se l'ambiente limita i backup diretti all'archiviazione BLOB.
- Eseguire il backup direttamente nell'archivio BLOB con `TO URL` l'opzione in T-SQL, se l'ambiente e le procedure di sicurezza lo consentono. 

Impostare i database di cui si vuole eseguire la migrazione alla modalità di recupero con registrazione completa per consentire i backup del log.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Per eseguire manualmente backup completi, differenziali e del log del database nell'archiviazione locale, usare gli script T-SQL di esempio seguenti. Assicurarsi che `CHECKSUM` l'opzione sia abilitata, perché è obbligatoria per l'LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Archiviazione BLOB di Azure viene usato come archivio intermedio per i file di backup tra SQL Server e SQL Istanza gestita. Per creare un nuovo account di archiviazione e un contenitore BLOB all'interno dell'account di archiviazione, seguire questa procedura:

1. [Creare un account di archiviazione](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Creare un contenitore BLOB all'interno](../../storage/blobs/storage-quickstart-blobs-portal.md) dell'account di archiviazione.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Copiare i backup da SQL Server nell'archivio BLOB

Durante la migrazione dei database a un'istanza gestita tramite l'archiviazione con accesso in lettura, è possibile usare gli approcci seguenti per caricare i backup nell'archiviazione BLOB:
- Uso SQL Server funzionalità [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) nativa
- Uso [di Azcopy](../../storage/common/storage-use-azcopy-v10.md) [o Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) per caricare i backup in un contenitore BLOB
- Uso Storage Explorer nella portale di Azure

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Eseguire backup da un SQL Server direttamente all'archivio BLOB
Se i criteri aziendali e di rete lo consentono, un'alternativa consiste nell'eseguire backup da SQL Server direttamente all'archiviazione BLOB usando l'opzione [backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) nativa SQL Server. Se è possibile usare questa opzione, non è necessario eseguire backup nell'archiviazione locale e caricarli nell'archivio BLOB.

Come primo passaggio, per questa operazione è necessario generare un token di autenticazione di firma di accesso condiviso per l'archiviazione BLOB e quindi importare il token in SQL Server. Il secondo passaggio consiste nell'eseguire backup con `TO URL` l'opzione in T-SQL. Assicurarsi che tutti i backup siano evasi con `CHEKSUM` l'opzione abilitata.

Per riferimento, il codice di esempio seguente esegue backup nell'archivio BLOB. Questo esempio non include istruzioni su come importare il token di firma di accesso condiviso. È possibile trovare istruzioni dettagliate, tra cui come generare e importare il token di firma di accesso condiviso in SQL Server, nell'esercitazione Usare Archiviazione BLOB di Azure [con SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Generare un token di autenticazione di firma di accesso condiviso dell'archiviazione BLOB per l'archiviazione con registrazione locale

Archiviazione BLOB di Azure viene usato come archivio intermedio per i file di backup tra SQL Server e SQL Istanza gestita. È necessario generare un token di autenticazione di firma di accesso condiviso, con solo autorizzazioni di elenco e lettura, per l'accesso in lettura. Il token consentirà all'archiviazione con registrazione locale di accedere all'archiviazione BLOB e di usare i file di backup per ripristinarli in SQL Istanza gestita. 

Per generare il token, seguire questa procedura:

1. Aprire Storage Explorer dal portale di Azure.
2. Espandere **Contenitori BLOB**.
3. Fare clic con il pulsante destro del mouse sul contenitore BLOB e **scegliere Ottieni firma di accesso condiviso.**

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Screenshot che mostra le selezioni per la generazione di un token di autenticazione S A S.":::

4. Selezionare l'intervallo di tempo per la scadenza del token. Assicurarsi che il token sia valido per la durata della migrazione.
5. Selezionare il fuso orario per il token: UTC o ora locale.
    
   > [!IMPORTANT]
   > Il fuso orario del token e l'istanza gestita potrebbero non essere corrispondenti. Assicurarsi che il token di firma di accesso condiviso abbia la validità temporale appropriata, tenendo in considerazione i fusi orari. Se possibile, impostare il fuso orario su un'ora precedente e successiva dell'intervallo di migrazione pianificato.
6. Selezionare **Sola lettura** ed **Elenca** autorizzazioni.

   > [!IMPORTANT]
   > Non selezionare altre autorizzazioni. In caso contrario, LRS non verrà avviato. Questo requisito di sicurezza è di progettazione.
7. Selezionare **Crea**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Screenshot che mostra le selezioni per la scadenza del token S A S, il fuso orario e le autorizzazioni, insieme al pulsante Crea.":::

L'autenticazione di firma di accesso condiviso viene generata con la validità dell'ora specificata. È necessaria la versione URI del token, come illustrato nello screenshot seguente.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Screenshot che mostra un esempio della versione U R I di un token S A S.":::

### <a name="copy-parameters-from-the-sas-token"></a>Copiare i parametri dal token di firma di accesso condiviso

Prima di usare il token di firma di accesso condiviso per avviare LRS, è necessario comprenderne la struttura. L'URI del token di firma di accesso condiviso generato è costituito da due parti separate da un punto interrogativo ( `?` ), come illustrato in questo esempio:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Esempio di U R I per un token S A S generato per il servizio Di riesecuzione log." border="false":::

La prima parte, a partire da fino al punto interrogativo ( ), viene usata per il parametro immesso `https://` `?` come input per `StorageContainerURI` LRS. Fornisce informazioni LRS sulla cartella in cui sono archiviati i file di backup del database.

La seconda parte, a partire dal punto interrogativo ( ) e fino alla fine della `?` stringa, è il `StorageContainerSasToken` parametro . Si tratta del token di autenticazione firmato effettivo, valido per la durata dell'ora specificata. Questa parte non deve necessariamente iniziare con `sp=` come illustrato nell'esempio. Il caso potrebbe essere diverso.

Copiare i parametri come indicato di seguito:

1. Copiare la prima parte del token, a partire da tutto `https://` fino al punto interrogativo ( `?` ). Usarlo come parametro in `StorageContainerUri` PowerShell o nell'interfaccia della riga di comando di Azure per avviare LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Screenshot che mostra la copia della prima parte del token.":::

2. Copiare la seconda parte del token, a partire dal punto interrogativo ( ) fino `?` alla fine della stringa. Usarlo come parametro in `StorageContainerSasToken` PowerShell o nell'interfaccia della riga di comando di Azure per avviare LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Screenshot che mostra la copia della seconda parte del token.":::
   
> [!NOTE]
> Non includere il punto interrogativo quando si copia una delle due parti del token.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Accedere ad Azure e selezionare una sottoscrizione

Usare il cmdlet di PowerShell seguente per accedere ad Azure:

```powershell
Login-AzAccount
```

Selezionare la sottoscrizione appropriata in cui risiede l'istanza gestita usando il cmdlet di PowerShell seguente:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Avviare la migrazione

Avviare la migrazione avviando l'istanza di LRS. È possibile avviare il servizio in modalità di completamento automatico o continua. 

Quando si usa la modalità di completamento automatico, la migrazione viene completata automaticamente quando viene ripristinato l'ultimo dei file di backup specificati. Questa opzione richiede il comando start per specificare il nome file dell'ultimo file di backup. 

Quando si usa la modalità continua, il servizio ripristini continuamente tutti i nuovi file di backup aggiunti. La migrazione verrà completata solo con il cutover manuale. 

### <a name="start-lrs-in-autocomplete-mode"></a>Avviare l'istanza di LRS in modalità di completamento automatico

Per avviare LRS in modalità di completamento automatico, usare i comandi di PowerShell o dell'interfaccia della riga di comando di Azure seguenti. Specificare il nome dell'ultimo file di backup usando il `-LastBackupName` parametro . Al momento del ripristino dell'ultimo dei file di backup specificati, il servizio avvierà automaticamente un cutover.

Di seguito è riportato un esempio di avvio dell'istanza di LRS in modalità di completamento automatico tramite PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Di seguito è riportato un esempio di avvio dell'archiviazione con archiviazione con accesso in lettura in modalità di completamento automatico tramite l'interfaccia della riga di comando di Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Avviare l'istanza di LRS in modalità continua

Di seguito è riportato un esempio di avvio dell'istanza di LRS in modalità continua tramite PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Di seguito è riportato un esempio di avvio dell'archiviazione con archiviazione con accesso in modalità continua tramite l'interfaccia della riga di comando di Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

I client PowerShell e dell'interfaccia della riga di comando per avviare l'istanza di LRS in modalità continua sono sincroni. Ciò significa che i client attenderanno che la risposta dell'API segnala l'esito positivo o negativo dell'avvio del processo. 

Durante questa attesa, il comando non restituirà il controllo al prompt dei comandi. Se si esegue lo script dell'esperienza di migrazione ed è necessario il comando di avvio dell'istanza di LRS per ridare immediatamente il controllo per continuare con il resto dello script, è possibile eseguire PowerShell come processo in background con l'opzione `-AsJob` . Ad esempio:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quando si avvia un processo in background, un oggetto processo viene restituito immediatamente, anche se il completamento del processo richiede molto tempo. È possibile continuare a lavorare nella sessione senza interruzioni durante l'esecuzione del processo. Per informazioni dettagliate sull'esecuzione di PowerShell come processo in background, vedere la [documentazione di PowerShell Start-Job.](/powershell/module/microsoft.powershell.core/start-job#description)

Analogamente, per avviare un comando dell'interfaccia della riga di comando di Azure in Linux come processo in background, usare la e commerciale ( ) alla fine del comando di avvio `&` dell'archiviazione con archiviazione con registrazione remota:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Dopo l'avvio di LRS, tutte le patch software gestite dal sistema vengono arrestate per 47 ore. Dopo questa finestra, la successiva patch software automatizzata arresterà automaticamente LRS. In questo caso, non è possibile riprendere la migrazione ed è necessario riavviarla da zero. 

## <a name="monitor-the-migration-progress"></a>Monitorare lo stato di avanzamento della migrazione

Per monitorare lo stato di avanzamento della migrazione tramite PowerShell, usare il comando seguente:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per monitorare lo stato di avanzamento della migrazione tramite l'interfaccia della riga di comando di Azure, usare il comando seguente:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Arrestare la migrazione

Se è necessario arrestare la migrazione, usare i cmdlet seguenti. L'arresto della migrazione eliminerà il database in Istanza gestita SQL, quindi la ripresa della migrazione non sarà possibile.

Per arrestare il processo di migrazione tramite PowerShell, usare il comando seguente:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Per arrestare il processo di migrazione tramite l'interfaccia della riga di comando di Azure, usare il comando seguente:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Completare la migrazione (modalità continua)

Se è stata avviata l'archiviazione con registrazione differenziale in modalità continua, dopo aver eseguito il ripristino di tutti i backup, l'avvio del cutover completerà la migrazione. Dopo il cutover, il database verrà migrato e pronto per l'accesso in lettura e scrittura.

Per completare il processo di migrazione in modalità continua LRS tramite PowerShell, usare il comando seguente:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Per completare il processo di migrazione in modalità continua LRS tramite l'interfaccia della riga di comando di Azure, usare il comando seguente:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitazioni funzionali

Le limitazioni funzionali dell'istanza di LRS sono:
- Il database da ripristinare non può essere usato per l'accesso in sola lettura durante il processo di migrazione.
- Le patch software gestite dal sistema vengono bloccate per 47 ore dopo l'avvio di LRS. Dopo la scadenza di questo intervallo di tempo, l'aggiornamento software successivo arresterà LRS. È quindi necessario riavviare l'istanza di LRS da zero.
- L'archiviazione con archiviazione con SQL Server database deve essere sottoposto a backup con `CHECKSUM` l'opzione abilitata.
- Il token di firma di accesso condiviso che verrà utilizzato dall'archiviazione con accesso condiviso deve essere generato per l'intero contenitore Archiviazione BLOB di Azure e deve avere solo autorizzazioni di lettura ed elenco.
- I file di backup per database diversi devono essere inseriti in cartelle separate nell'archivio BLOB.
- L'archiviazione con archiviazione con archiviazione locale deve essere avviata separatamente per ogni database che punta a cartelle separate con file di backup nell'archiviazione BLOB.
- L'LRS può supportare fino a 100 processi di ripristino simultanei per ogni singola istanza gestita.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Dopo aver avviato l'istanza di LRS, usare il cmdlet di monitoraggio ( `get-azsqlinstancedatabaselogreplay` o ) per visualizzare lo stato `az_sql_midb_log_replay_show` dell'operazione. Se LRS non viene avviato dopo un certo periodo di tempo e viene visualizzato un errore, verificare la presenza dei problemi più comuni:

- Un database esistente in SQL Istanza gestita ha lo stesso nome di quello di cui si sta tentando di eseguire la migrazione da SQL Server? Risolvere il conflitto rinominando uno dei database.
- Il backup del database in SQL Server eseguito tramite `CHECKSUM` l'opzione ?
- Le autorizzazioni per il token di firma di accesso condiviso sono di sola lettura ed elenco per LRS?
- Il token di firma di accesso condiviso per LRS è stato copiato dopo il punto interrogativo ( `?` ), con contenuto che inizia come il seguente: `sv=2020-02-10...` ? Â 
- Il tempo di validità del token di firma di accesso condiviso è applicabile per l'intervallo di tempo di avvio e completamento della migrazione? È possibile che non siano presenti corrispondenze a causa dei diversi fusi orari usati per sql Istanza gestita e il token di firma di accesso condiviso. Provare a rigenerare il token di firma di accesso condiviso ed estendere la validità del token dell'intervallo di tempo prima e dopo la data corrente.
- Il nome del database, il nome del gruppo di risorse e il nome dell'istanza gestita sono stati digitati correttamente?
- Se è stato avviato LRS in modalità di completamento automatico, è stato specificato un nome file valido per l'ultimo file di backup?

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [migrazione di SQL Server'istanza gestita di SQL.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- Altre informazioni sulle [differenze tra SQL Server e SQL Istanza gestita](transact-sql-tsql-differences-sql-server.md).
- Altre informazioni sulle procedure consigliate per i carichi di lavoro di costi e dimensioni [di cui è stata eseguita la migrazione ad Azure.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
