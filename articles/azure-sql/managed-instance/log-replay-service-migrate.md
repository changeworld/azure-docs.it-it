---
title: Eseguire la migrazione di database a SQL Istanza gestita tramite il servizio di riproduzione log
description: Informazioni su come eseguire la migrazione dei database da SQL Server a SQL Istanza gestita tramite il servizio di riproduzione log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/29/2021
ms.openlocfilehash: 186f1e085cecdc92e345231d50d06195bba55504
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732959"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Eseguire la migrazione dei database da SQL Server a SQL Istanza gestita utilizzando il servizio di riproduzione log (anteprima)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra come configurare manualmente la migrazione del database da SQL Server 2008-2019 ad Azure SQL Istanza gestita tramite il servizio di riproduzione log (con ridondanza locale), attualmente disponibile in anteprima pubblica. CON ridondanza locale è un servizio cloud abilitato per SQL Istanza gestita ed è basato sulla tecnologia di SQL Server log shipping. 

Il [servizio migrazione del database di Azure](../../dms/tutorial-sql-server-to-managed-instance.md) e con ridondanza locale usano la stessa tecnologia di migrazione sottostante e le stesse API. Rilasciando con ridondanza locale, è possibile abilitare migrazioni personalizzate complesse e architetture ibride tra SQL Server locali e SQL Istanza gestita.

## <a name="when-to-use-log-replay-service"></a>Quando utilizzare il servizio di riproduzione log

Quando non è possibile usare il servizio migrazione del database di Azure per la migrazione, è possibile usare con ridondanza locale direttamente con PowerShell, i cmdlet dell'interfaccia della riga di comando di Azure o le API per compilare e orchestrare manualmente le migrazioni di database a SQL Istanza gestita. 

È possibile considerare l'uso di con ridondanza locale nei casi seguenti:
- È necessario un maggiore controllo per il progetto di migrazione del database.
- Il tempo di inattività per la migrazione cutover è ridotto.
- Impossibile installare il file eseguibile del servizio migrazione del database nell'ambiente in uso.
- Il file eseguibile del servizio migrazione del database non dispone dell'accesso ai file dei backup del database.
- Nessun accesso al sistema operativo host disponibile oppure nessun privilegio di amministratore.
- Non è possibile aprire le porte di rete dall'ambiente ad Azure.
- I backup vengono archiviati direttamente nell'archiviazione BLOB di Azure tramite l' `TO URL` opzione.
- È necessario usare i backup differenziali.

> [!NOTE]
> È consigliabile automatizzare la migrazione dei database da SQL Server a SQL Istanza gestita utilizzando il servizio migrazione del database. Questo servizio usa lo stesso servizio cloud con ridondanza locale nel back-end, con log shipping in `NORECOVERY` modalità. Prendere in considerazione l'utilizzo manuale di con ridondanza locale per orchestrare le migrazioni quando il servizio migrazione del database non supporta completamente gli scenari.

## <a name="how-it-works"></a>Funzionamento

La creazione di una soluzione personalizzata usando con ridondanza locale per eseguire la migrazione dei database nel cloud richiede diversi passaggi di orchestrazione, come illustrato nel diagramma e nella tabella più avanti in questa sezione.

La migrazione prevede l'esecuzione di backup completi del database in SQL Server con `CHECKSUM` abilitato e la copia dei file di backup nell'archiviazione BLOB di Azure. CON ridondanza locale viene usato per ripristinare i file di backup dall'archiviazione BLOB a SQL Istanza gestita. L'archiviazione BLOB è una risorsa di archiviazione intermedia tra SQL Server e SQL Istanza gestita.

CON ridondanza locale monitora l'archiviazione BLOB per qualsiasi nuovo backup differenziale o del log aggiunto dopo il ripristino del backup completo. CON ridondanza locale Ripristina quindi automaticamente questi nuovi file. È possibile utilizzare il servizio per monitorare lo stato di avanzamento dei file di backup ripristinati in SQL Istanza gestita ed è possibile arrestare il processo, se necessario.

CON ridondanza locale non richiede una convenzione di denominazione specifica per i file di backup. Analizza tutti i file posizionati nell'archivio BLOB e costruisce la catena di backup leggendo solo le intestazioni dei file. I database si trovano nello stato "ripristino" durante il processo di migrazione. I database vengono ripristinati in modalità [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) , quindi non possono essere utilizzati per la lettura o la scrittura fino al completamento del processo di migrazione. 

Se si sta eseguendo la migrazione di diversi database, è necessario:
 
- Inserire i backup per ogni database in una cartella separata nell'archivio BLOB.
- Avviare con ridondanza locale separatamente per ogni database.
- Specificare percorsi diversi per separare le cartelle di archiviazione BLOB. 

È possibile avviare con ridondanza locale in modalità di *completamento automatico* o *continua* . Quando si avvia la modalità di completamento automatico, la migrazione viene completata automaticamente quando è stato ripristinato l'ultimo file di backup specificato. Quando si avvia con ridondanza locale in modalità continua, il servizio ripristinerà continuamente eventuali nuovi file di backup aggiunti e la migrazione viene completata solo con il cutover manuale. 

È consigliabile eseguire il ritaglio manuale dopo che è stato eseguito il backup della parte finale del log e viene visualizzato come ripristinato in SQL Istanza gestita. Il passaggio finale cutover renderà il database online e disponibile per l'uso in lettura e scrittura in SQL Istanza gestita.

Dopo l'arresto di con ridondanza locale, tramite il completamento automatico o manualmente tramite cutover, non è possibile riprendere il processo di ripristino per un database che è stato portato online in SQL Istanza gestita. Per ripristinare i file di backup aggiuntivi al termine della migrazione tramite il completamento automatico o cutover, è necessario eliminare il database. È anche necessario ripristinare l'intera catena di backup da zero riavviando con ridondanza locale.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagramma che illustra i passaggi di orchestrazione del servizio di riesecuzione del log per SQL Istanza gestita." border="false":::
    
| Operazione | Dettagli |
| :----------------------------- | :------------------------- |
| **1. copiare i backup del database da SQL Server nell'archivio BLOB**. | Copiare i backup completi, differenziali e del log da SQL Server a un contenitore di archiviazione BLOB usando [Azcopy](../../storage/common/storage-use-azcopy-v10.md) o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Usare qualsiasi nome file. CON ridondanza locale non richiede una convenzione di denominazione dei file specifica.<br /><br />Per eseguire la migrazione di diversi database, è necessaria una cartella separata per ogni database. |
| **2. avviare con ridondanza locale nel cloud**. | È possibile riavviare il servizio con una scelta di cmdlet: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure ([cmdlet di az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Avviare con ridondanza locale separatamente per ogni database che punta a una cartella di backup nell'archivio BLOB. <br /><br /> Una volta avviato, il servizio eseguirà i backup dal contenitore di archiviazione BLOB e avvierà il ripristino in SQL Istanza gestita.<br /><br /> Se con ridondanza locale è stato avviato in modalità continua, dopo il ripristino di tutti i backup caricati inizialmente, il servizio osserverà eventuali nuovi file caricati nella cartella. Il servizio applicherà continuamente i log in base alla catena del numero di sequenza del file di log (LSN) finché non verrà arrestato. |
| **2,1. monitorare lo stato dell'operazione**. | È possibile monitorare lo stato di avanzamento dell'operazione di ripristino con una scelta di cmdlet: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure ([cmdlet di az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. arrestare l'operazione, se necessario**. | Se è necessario arrestare il processo di migrazione, è possibile scegliere i cmdlet: PowerShell ([Stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> L'arresto dell'operazione eliminerà il database di cui si sta eseguendo il ripristino in SQL Istanza gestita. Dopo l'arresto di un'operazione, non è possibile riprendere con ridondanza locale per un database. È necessario riavviare il processo di migrazione da zero. |
| **3. quando si è pronti, passare al cloud**. | Arrestare l'applicazione e il carico di lavoro. Eseguire l'ultimo backup di log Tail e caricarlo nell'archiviazione BLOB di Azure.<br /><br /> Completare il cutover avviando un'operazione con ridondanza locale `complete` con una scelta di cmdlet: PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) o l'interfaccia della riga di comando di Azure [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Questa operazione arresterà con ridondanza locale e causerà la messa in linea del database per l'utilizzo in lettura e scrittura su SQL Istanza gestita.<br /><br /> Consente di reindirizzare la stringa di connessione dell'applicazione da SQL Server a SQL Istanza gestita. Sarà necessario orchestrare questo passaggio manualmente, tramite una modifica manuale della stringa di connessione nell'applicazione o automaticamente (ad esempio, se l'applicazione può, ad esempio, leggere la stringa di connessione da una proprietà o da un database). |

## <a name="requirements-for-getting-started"></a>Requisiti per la Guida introduttiva

### <a name="sql-server-side"></a>Lato SQL Server
- SQL Server 2008-2019
- Backup completo dei database (uno o più file)
- Backup differenziale (uno o più file)
- Backup del log (non suddiviso per un file di log delle transazioni)
- `CHECKSUM` abilitato per i backup (obbligatorio)

### <a name="azure-side"></a>Lato Azure
- PowerShell AZ. SQL Module versione 2.16.0 o successiva ([installata](https://www.powershellgallery.com/packages/Az.Sql/) o a cui si accede tramite [Azure cloud Shell](/azure/cloud-shell/))
- INTERFACCIA della riga di comando di Azure versione 2.19.0 o successiva ([installata](/cli/azure/install-azure-cli))
- Provisioning del contenitore di archiviazione BLOB di Azure
- Token di sicurezza della firma di accesso condiviso con autorizzazioni di lettura ed elenco generate per il contenitore di archiviazione BLOB

### <a name="migration-of-multiple-databases"></a>Migrazione di più database
È necessario inserire i file di backup per database diversi in cartelle separate nell'archivio BLOB.

Avviare con ridondanza locale separatamente per ogni database puntando a una cartella appropriata nell'archivio BLOB. CON ridondanza locale può supportare fino a 100 processi di ripristino simultanei per singola istanza gestita.

### <a name="azure-rbac-permissions"></a>Autorizzazioni RBAC di Azure
Per eseguire con ridondanza locale tramite i client forniti è necessario uno dei seguenti ruoli di Azure:
- Ruolo proprietario sottoscrizione
- Ruolo [collaboratore istanza gestita](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Ruolo personalizzato con l'autorizzazione seguente: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Procedure consigliate

Si consiglia di ricorrere alle seguenti procedure:
- Eseguire [Data Migration Assistant](/sql/dma/dma-overview) per verificare che i database siano pronti per la migrazione a SQL istanza gestita. 
- Suddividere i backup completi e differenziali in più file, anziché usare un singolo file.
- Abilitare la compressione dei backup.
- Usare Cloud Shell per eseguire gli script, perché verranno sempre aggiornati ai cmdlet più recenti rilasciati.
- Pianificare il completamento della migrazione entro 47 ore dopo l'avvio di con ridondanza locale. Si tratta di un periodo di tolleranza che impedisce l'installazione di patch software gestite dal sistema.

> [!IMPORTANT]
> - Non è possibile usare il database ripristinato tramite con ridondanza locale fino al termine del processo di migrazione. 
> - CON ridondanza locale non supporta l'accesso in sola lettura ai database durante la migrazione.
> - Al termine della migrazione, il processo di migrazione viene finalizzato perché con ridondanza locale non supporta la ripresa del processo di ripristino.

## <a name="steps-to-execute"></a>Passaggi da eseguire

### <a name="make-backups-of-sql-server"></a>Eseguire backup di SQL Server

È possibile eseguire il backup di SQL Server usando una delle opzioni seguenti:

- Eseguire il backup nell'archiviazione su disco locale e quindi caricare i file nell'archiviazione BLOB di Azure, se l'ambiente limita i backup diretti all'archiviazione BLOB.
- Eseguire il backup direttamente nell'archiviazione BLOB con l' `TO URL` opzione in T-SQL, se l'ambiente e le procedure di sicurezza lo consentono. 

Impostare i database di cui si desidera eseguire la migrazione alla modalità di recupero con registrazione completa per consentire i backup del log.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Per eseguire manualmente backup completi, differenziali e del log del database nell'archiviazione locale, usare gli script T-SQL di esempio seguenti. Verificare che l' `CHECKSUM` opzione sia abilitata, perché è obbligatoria per con ridondanza locale.

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

L'archivio BLOB di Azure viene usato come risorsa di archiviazione intermedia per i file di backup tra SQL Server e SQL Istanza gestita. Per creare un nuovo account di archiviazione e un contenitore BLOB nell'account di archiviazione, seguire questa procedura:

1. [Creare un account di archiviazione](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Creta un contenitore BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md) nell'account di archiviazione.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Copiare i backup da SQL Server nell'archiviazione BLOB

Per eseguire la migrazione di database a un'istanza gestita tramite con ridondanza locale, è possibile usare gli approcci seguenti per caricare i backup nell'archiviazione BLOB:
- Uso di SQL Server funzionalità [di backup nativo in URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Uso di [Azcopy](../../storage/common/storage-use-azcopy-v10.md) o [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) per caricare i backup in un contenitore BLOB
- Utilizzo di Storage Explorer nel portale di Azure

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Eseguire backup da SQL Server direttamente nell'archiviazione BLOB
Se i criteri aziendali e di rete lo consentono, un'alternativa consiste nell'eseguire il backup da SQL Server direttamente nell'archiviazione BLOB usando l'opzione backup nativo SQL Server [per l'URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Se è possibile adottare questa opzione, non è necessario eseguire i backup nell'archivio locale e caricarli nell'archivio BLOB.

Come primo passaggio, per questa operazione è necessario generare un token di autenticazione SAS per l'archiviazione BLOB e quindi importare il token in SQL Server. Il secondo passaggio consiste nel creare backup con l' `TO URL` opzione in T-SQL. Verificare che tutti i backup vengano eseguiti con l' `CHEKSUM` opzione abilitata.

Per riferimento, il codice di esempio seguente consente di eseguire i backup nell'archivio BLOB. Questo esempio non include istruzioni su come importare il token SAS. Per istruzioni dettagliate, tra cui come generare e importare il token di firma di accesso condiviso in SQL Server, vedere l'esercitazione [usare l'archivio BLOB di Azure con SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

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

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Generare un token di autenticazione SAS per l'archiviazione BLOB per con ridondanza locale

L'archivio BLOB di Azure viene usato come risorsa di archiviazione intermedia per i file di backup tra SQL Server e SQL Istanza gestita. È necessario generare un token di autenticazione della firma di accesso condiviso, con solo le autorizzazioni List e Read, per con ridondanza locale. Il token consentirà a con ridondanza locale di accedere all'archiviazione BLOB e usare i file di backup per ripristinarli in SQL Istanza gestita. 

Per generare il token, attenersi alla procedura seguente:

1. Aprire Storage Explorer dalla portale di Azure.
2. Espandere **contenitori BLOB**.
3. Fare clic con il pulsante destro del mouse sul contenitore BLOB e selezionare **Ottieni firma di accesso condiviso**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Screenshot che mostra le selezioni per la generazione di un token di autenticazione S A.":::

4. Selezionare l'intervallo di tempo per la scadenza del token. Verificare che il token sia valido per la durata della migrazione.
5. Selezionare il fuso orario per il token: UTC o l'ora locale.
    
   > [!IMPORTANT]
   > Il fuso orario del token e dell'istanza gestita potrebbe non corrispondere. Verificare che il token di firma di accesso condiviso disponga della validità del tempo appropriato, prendendo in considerazione i fusi orari. Se possibile, impostare il fuso orario su un'ora precedente e successiva della finestra di migrazione pianificata.
6. Selezionare **Leggi** ed **Elenca** solo le autorizzazioni.

   > [!IMPORTANT]
   > Non selezionare altre autorizzazioni. In caso contrario, con ridondanza locale non verrà avviato. Questo requisito di sicurezza è da progettazione.
7. Selezionare **Crea**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Screenshot che mostra le selezioni per la scadenza, il fuso orario e le autorizzazioni del token S A, insieme al pulsante Crea.":::

L'autenticazione SAS viene generata con la validità dell'ora specificata. È necessaria la versione URI del token, come illustrato nello screenshot seguente.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Screenshot che mostra un esempio della versione U R I di un token S A.":::

### <a name="copy-parameters-from-the-sas-token"></a>Copiare i parametri dal token SAS

Prima di usare il token SAS per avviare con ridondanza locale, è necessario comprenderne la struttura. L'URI del token di firma di accesso condiviso generato è costituito da due parti separate da un punto interrogativo ( `?` ), come illustrato nell'esempio seguente:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Esempio U R I per un token S A generato per il servizio di riproduzione log." border="false":::

La prima parte, a partire da `https://` fino a quando il punto interrogativo ( `?` ) viene usata per il `StorageContainerURI` parametro alimentato come input per con ridondanza locale. Fornisce informazioni con ridondanza locale sulla cartella in cui sono archiviati i file di backup del database.

La seconda parte, che inizia dopo il punto interrogativo ( `?` ) e continua fino alla fine della stringa, è il `StorageContainerSasToken` parametro. Questo è il token di autenticazione firmato effettivo, che è valido per la durata dell'ora specificata. Questa parte non deve necessariamente iniziare con `sp=` , come illustrato nell'esempio. Il caso potrebbe variare.

Copiare i parametri come segue:

1. Copiare la prima parte del token, iniziando da `https://` sempre fino al punto interrogativo ( `?` ). Usarlo come `StorageContainerUri` parametro in PowerShell o nell'interfaccia della riga di comando di Azure per avviare con ridondanza locale.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Screenshot che mostra la copia della prima parte del token.":::

2. Copiare la seconda parte del token, a partire dal punto interrogativo ( `?` ) fino alla fine della stringa. Usarlo come `StorageContainerSasToken` parametro in PowerShell o nell'interfaccia della riga di comando di Azure per avviare con ridondanza locale.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Screenshot che mostra la copia della seconda parte del token.":::
   
> [!NOTE]
> Non includere il punto interrogativo quando si copia una parte del token.

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

Per avviare la migrazione, avviare con ridondanza locale. È possibile avviare il servizio in modalità di completamento automatico o continua. 

Quando si usa la modalità di completamento automatico, la migrazione viene completata automaticamente quando è stato ripristinato l'ultimo file di backup specificato. Questa opzione richiede il comando avvia per specificare il nome file dell'ultimo file di backup. 

Quando si usa la modalità continua, il servizio ripristinerà continuamente tutti i nuovi file di backup aggiunti. La migrazione viene completata solo in cutover manuale. 

### <a name="start-lrs-in-autocomplete-mode"></a>Avviare con ridondanza locale in modalità di completamento automatico

Per avviare con ridondanza locale in modalità di completamento automatico, usare i comandi seguenti di PowerShell o dell'interfaccia della riga di comando di Azure. Specificare l'ultimo nome del file di backup utilizzando il `-LastBackupName` parametro. Al momento del ripristino dell'ultimo file di backup specificato, il servizio avvierà automaticamente un cutover.

Di seguito è riportato un esempio di avvio di con ridondanza locale in modalità di completamento automatico usando PowerShell:

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

Di seguito è riportato un esempio di avvio di con ridondanza locale in modalità di completamento automatico usando l'interfaccia della riga di comando di Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Avviare con ridondanza locale in modalità continua

Di seguito è riportato un esempio di avvio di con ridondanza locale in modalità continua con PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Di seguito è riportato un esempio di avvio di con ridondanza locale in modalità continua usando l'interfaccia della riga di comando di Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

I client PowerShell e CLI per avviare con ridondanza locale in modalità continua sono sincroni. Ciò significa che i client attendono che la risposta dell'API segnali in caso di esito positivo o negativo per avviare il processo. 

Durante questa attesa, il comando non restituisce il controllo al prompt dei comandi. Se si sta eseguendo lo script dell'esperienza di migrazione ed è necessario il comando di avvio di con ridondanza locale per restituire immediatamente il controllo per continuare con il resto dello script, è possibile eseguire PowerShell come processo in background con l' `-AsJob` opzione. Ad esempio:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quando si avvia un processo in background, viene restituito immediatamente un oggetto processo, anche se il completamento del processo richiede un tempo prolungato. È possibile continuare a lavorare nella sessione senza interruzioni durante l'esecuzione del processo. Per informazioni dettagliate sull'esecuzione di PowerShell come processo in background, vedere la documentazione di [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) .

Analogamente, per avviare un comando dell'interfaccia della riga di comando di Azure in Linux come processo in background, usare la e commerciale ( `&` ) alla fine del comando di avvio di con ridondanza locale:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Dopo l'avvio di con ridondanza locale, tutte le patch software gestite dal sistema vengono interrotte per 47 ore. Dopo questa finestra, la patch software automatizzata successiva arresterà automaticamente con ridondanza locale. In tal caso, non è possibile riprendere la migrazione ed è necessario riavviarla da zero. 

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

Se è necessario arrestare la migrazione, usare i cmdlet seguenti. L'arresto della migrazione eliminerà il database di ripristino in SQL Istanza gestita, quindi non sarà possibile riprendere la migrazione.

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

## <a name="complete-the-migration-continuous-mode"></a>Completamento della migrazione (modalità continua)

Se il con ridondanza locale è stato avviato in modalità continua, dopo aver verificato che tutti i backup siano stati ripristinati, l'avvio del cutover completerà la migrazione. Dopo cutover, il database verrà migrato e pronto per l'accesso in lettura e scrittura.

Per completare il processo di migrazione in modalità continua con ridondanza locale tramite PowerShell, usare il comando seguente:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Per completare il processo di migrazione in modalità continua con ridondanza locale tramite l'interfaccia della riga di comando di Azure, usare il comando seguente:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitazioni funzionali

Le limitazioni funzionali di con ridondanza locale sono:
- Il database di cui si sta eseguendo il ripristino non può essere utilizzato per l'accesso in sola lettura durante il processo di migrazione.
- Le patch software gestite dal sistema sono bloccate per 47 ore dopo l'avvio di con ridondanza locale. Dopo la scadenza di questo intervallo di tempo, l'aggiornamento software successivo arresterà con ridondanza locale. È quindi necessario riavviare con ridondanza locale da zero.
- Per con ridondanza locale è necessario eseguire il backup di database in SQL Server con l' `CHECKSUM` opzione abilitata.
- Il token di firma di accesso condiviso che verrà usato da con ridondanza locale deve essere generato per l'intero contenitore di archiviazione BLOB di Azure e deve avere solo autorizzazioni di lettura ed elenco.
- I file di backup per database diversi devono essere posizionati in cartelle separate nell'archivio BLOB.
- CON ridondanza locale deve essere avviato separatamente per ogni database che punta a cartelle separate con file di backup nell'archivio BLOB.
- CON ridondanza locale può supportare fino a 100 processi di ripristino simultanei per singola istanza gestita.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Dopo l'avvio di con ridondanza locale, usare il cmdlet Monitoring ( `get-azsqlinstancedatabaselogreplay` o `az_sql_midb_log_replay_show` ) per visualizzare lo stato dell'operazione. Se con ridondanza locale non viene avviato dopo un certo periodo di tempo e viene generato un errore, verificare i problemi più comuni:

- Un database esistente in SQL Istanza gestita ha lo stesso nome di quello che si sta tentando di migrare da SQL Server? Per risolvere il conflitto, rinominare uno dei database.
- È stato eseguito il backup del database in SQL Server tramite l' `CHECKSUM` opzione?
- Le autorizzazioni per il token SAS sono lette ed elencate solo per con ridondanza locale?
- Il token di firma di accesso condiviso per con ridondanza locale è stato copiato dopo il punto interrogativo ( `?` ) con contenuto che inizia come segue: `sv=2020-02-10...` ? 
- Il tempo di validità del token di firma di accesso condiviso è applicabile per l'intervallo di tempo di avvio e completamento della migrazione? Potrebbero esserci mancate corrispondenze a causa dei diversi fusi orari usati per SQL Istanza gestita e il token SAS. Provare a rigenerare il token SAS ed estendere la validità del token della finestra temporale prima e dopo la data corrente.
- Il nome del database, il nome del gruppo di risorse e il nome dell'istanza gestita sono stati digitati correttamente?
- Se con ridondanza locale è stato avviato in modalità di completamento automatico, era un nome file valido per l'ultimo file di backup specificato?

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [migrazione di SQL Server a un'istanza gestita di SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Altre informazioni sulle [differenze tra SQL Server e SQL istanza gestita](transact-sql-tsql-differences-sql-server.md).
- Altre informazioni sulle [procedure consigliate per i carichi di lavoro relativi a costi e dimensioni migrati in Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
