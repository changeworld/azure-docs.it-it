---
title: Abilitare SQL Insights
description: Abilitare informazioni dettagliate su SQL in Monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 012aa364fe9e379455b6b63f7c9e541d2d5b97ed
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726899"
---
# <a name="enable-sql-insights-preview"></a>Abilitare informazioni dettagliate su SQL (anteprima)
Questo articolo descrive come abilitare le [informazioni dettagliate di SQL](sql-insights-overview.md) per monitorare le distribuzioni SQL. Il monitoraggio viene eseguito da una macchina virtuale di Azure che esegue una connessione alle distribuzioni SQL e usa viste a gestione dinamica (DMV) per raccogliere i dati di monitoraggio. È possibile controllare quali set di dati vengono raccolti e la frequenza di raccolta usando un profilo di monitoraggio.

> [!NOTE]
> Per abilitare le informazioni dettagliate su SQL creando il profilo di monitoraggio e la macchina virtuale usando un modello di Resource Manager, vedere esempi di modelli Resource Manager [per informazioni dettagliate su SQL.](resource-manager-sql-insights.md)

## <a name="create-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics
Sql Insights archivia i dati in una o più aree [di lavoro Log Analytics.](../logs/data-platform-logs.md#log-analytics-workspaces)  Prima di poter abilitare SQL Insights, è necessario creare [un'area](../logs/quick-create-workspace.md) di lavoro o selezionarne una esistente. È possibile usare una singola area di lavoro con più profili di monitoraggio, ma l'area di lavoro e i profili devono trovarsi nella stessa area di Azure. Per abilitare e accedere alle funzionalità di SQL Insights, è necessario avere il ruolo [collaboratore di Log Analytics](../logs/manage-access.md) nell'area di lavoro. 

## <a name="create-monitoring-user"></a>Creare un utente di monitoraggio 
È necessario un utente nelle distribuzioni SQL da monitorare. Seguire le procedure seguenti per i diversi tipi di distribuzioni SQL.

Le istruzioni seguenti illustrano il processo per ogni tipo di SQL che è possibile monitorare.  Per eseguire questa operazione con uno script in più risorse SQL contemporaneamente, fare riferimento al [file README](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) e allo [script di esempio seguente.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1)


### <a name="azure-sql-database"></a>Database SQL di Azure
Aprire database SQL di Azure con [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o [Editor di query (anteprima)](../../azure-sql/database/connect-query-portal.md) nel portale di Azure.

Eseguire lo script seguente per creare un utente con le autorizzazioni necessarie. Sostituire *user* con un nome utente e *mystrongpassword* con una password.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Creare uno script utente telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Verificare che l'utente sia stato creato.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verificare lo script utente telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Istanza gestita di SQL di Azure
Accedere al Istanza gestita di SQL di Azure e usare [](../../azure-sql/database/connect-query-ssms.md) SQL Server Management Studio o uno strumento simile per eseguire lo script seguente per creare l'utente di monitoraggio con le autorizzazioni necessarie. Sostituire *user* con un nome utente e *mystrongpassword* con una password.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Accedere alla macchina virtuale di Azure che esegue SQL Server e usare [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o uno strumento simile per eseguire lo script seguente per creare l'utente di monitoraggio con le autorizzazioni necessarie. Sostituire *user* con un nome utente e *mystrongpassword* con una password.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Verificare che l'utente sia stato creato.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Creare una macchina virtuale di Azure 
Sarà necessario creare una o più macchine virtuali di Azure che verranno usate per raccogliere i dati per monitorare SQL.  

> [!NOTE]
>  I [profili di](#create-sql-monitoring-profile) monitoraggio specificano i dati che verranno raccolti dai diversi tipi di SQL da monitorare. A ogni macchina virtuale di monitoraggio può essere associato un solo profilo di monitoraggio. Se sono necessari più profili di monitoraggio, è necessario creare una macchina virtuale per ognuno di essi.

### <a name="azure-virtual-machine-requirements"></a>Requisiti delle macchine virtuali di Azure
Le macchine virtuali di Azure hanno i requisiti seguenti.

- Sistema operativo: Ubuntu 18.04 
- Dimensioni consigliate delle macchine virtuali di Azure: Standard_B2s (2 CPU, 4 GiB di memoria) 
- Aree supportate: qualsiasi [area supportata dall'agente Monitoraggio di Azure](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Le Standard_B2s (2 CPU, 4 GiB di memoria) della macchina virtuale supporteranno fino a 100 stringhe di connessione. Non è consigliabile allocare più di 100 connessioni a una singola macchina virtuale.

A seconda delle impostazioni di rete delle risorse SQL, potrebbe essere necessario inserire le macchine virtuali nella stessa rete virtuale delle risorse SQL in modo che possano effettuare connessioni di rete per raccogliere i dati di monitoraggio.  

## <a name="configure-network-settings"></a>Configurare le impostazioni di rete
Ogni tipo di SQL offre metodi per la macchina virtuale di monitoraggio per accedere in modo sicuro a SQL.  Le sezioni seguenti illustrano le opzioni basate sul tipo di SQL.

### <a name="azure-sql-databases"></a>Database SQL di Azure  

Sql Insights supporta l'accesso al database SQL di Azure tramite l'endpoint pubblico e dalla rete virtuale.

Per l'accesso tramite l'endpoint pubblico, è necessario aggiungere una regola nella pagina **Impostazioni firewall** e nella sezione Impostazioni del [firewall IP.](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules)  Per specificare l'accesso da una rete virtuale, è possibile impostare le regole del [firewall](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) di rete virtuale e impostare i tag di servizio richiesti dall'Monitoraggio di Azure [virtuale.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking)  [Questo articolo](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) descrive le differenze tra questi due tipi di regole del firewall.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Impostare il firewall del server" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Impostazioni del firewall." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Istanze gestite di SQL di Azure 

Se la macchina virtuale di monitoraggio si trova nella stessa rete virtuale delle risorse mi SQL, vedere [Connettersi all'interno della stessa rete virtuale.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet) Se la macchina virtuale di monitoraggio si trova nella rete virtuale diversa rispetto alle risorse DELL'istanza di SQL, vedere [Connettersi all'interno di una rete virtuale diversa.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Macchina virtuale di Azure e Azure SQL virtuale  
Se la macchina virtuale di monitoraggio si trova nella stessa rete virtuale delle risorse della macchina virtuale SQL, vedere Connettersi a SQL Server [all'interno di una rete virtuale.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network) Se la macchina virtuale di monitoraggio sarà in una rete virtuale diversa rispetto alle risorse della macchina virtuale SQL, vedere Connettersi a SQL Server [su Internet.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)

## <a name="store-monitoring-password-in-key-vault"></a>Archiviare la password di monitoraggio Key Vault
È consigliabile archiviare le password di connessione utente SQL in un Key Vault anziché immetterle direttamente nelle stringhe di connessione del profilo di monitoraggio.

Quando si imposta il profilo per il monitoraggio SQL, è necessaria una delle autorizzazioni seguenti per la risorsa Key Vault che si intende usare:

- Microsoft.Authorization/roleAssignments/write 
- Autorizzazioni Microsoft.Authorization/roleAssignments/delete, ad esempio Amministratore accesso utenti o Proprietario 

Un nuovo criterio di accesso verrà creato automaticamente come parte della creazione del profilo SQL Monitoring che usa il Key Vault specificato. Usare *Consenti l'accesso da Tutte le reti* per Key Vault di rete.


## <a name="create-sql-monitoring-profile"></a>Creare un profilo di monitoraggio SQL
Aprire Informazioni dettagliate SQL selezionando **SQL (anteprima)** nella **sezione Informazioni dettagliate** del menu Monitoraggio di Azure nel portale di Azure.  Fare clic su **Crea nuovo profilo**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Creare un nuovo profilo." lightbox="media/sql-insights-enable/create-new-profile.png":::

Il profilo archivierà le informazioni che si desidera raccogliere dai sistemi SQL.  Include impostazioni specifiche per: 

- Database SQL di Azure 
- Istanze gestite di SQL di Azure 
- SQL Server in esecuzione nelle macchine virtuali  

Ad esempio, è possibile creare un profilo denominato Produzione *SQL* e un altro denominato Gestione temporanea *SQL* con impostazioni diverse per la frequenza di raccolta dei dati, i dati da raccogliere e l'area di lavoro a cui inviare i dati. 

Il profilo viene archiviato come risorsa [regola di raccolta](../agents/data-collection-rule-overview.md) dati nella sottoscrizione e nel gruppo di risorse selezionati. Ogni profilo richiede quanto segue:

- Name. Non può essere modificato dopo la creazione.
- Posizione. Si tratta di un'area di Azure.
- Area di lavoro Log Analytics per archiviare i dati di monitoraggio.
- Impostazioni di raccolta per la frequenza e il tipo di dati di monitoraggio SQL da raccogliere.

> [!NOTE]
> La posizione del profilo deve essere nella stessa posizione dell'area di lavoro Log Analytics a cui si intende inviare i dati di monitoraggio.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Dettagli del profilo." lightbox="media/sql-insights-enable/profile-details.png":::

Dopo **aver immesso** i dettagli per il profilo di monitoraggio, fare clic su Crea profilo di monitoraggio. La distribuzione del profilo può richiedere fino a un minuto.  Se il nuovo profilo non è  elencato nella casella combinata Profilo di monitoraggio, fare clic sul pulsante di aggiornamento per visualizzare il profilo al termine della distribuzione.  Dopo aver selezionato il nuovo profilo, selezionare la scheda Gestisci profilo per aggiungere un computer di monitoraggio che verrà associato al profilo. 

### <a name="add-monitoring-machine"></a>Aggiungere il computer di monitoraggio
Selezionare **Aggiungi computer di monitoraggio** per aprire un pannello di contesto in cui scegliere la macchina virtuale da configurare per monitorare le istanze di SQL e specificare le stringhe di connessione.

Selezionare la sottoscrizione e il nome della macchina virtuale di monitoraggio. Se si usa Key Vault per archiviare la password per l'utente di monitoraggio, selezionare le risorse Key Vault con questi segreti e immettere l'URL e il nome del segreto da usare nelle stringhe di connessione. Per informazioni dettagliate sull'identificazione della stringa di connessione per distribuzioni SQL diverse, vedere la sezione successiva.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Aggiungere il computer di monitoraggio." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Aggiungere stringhe di connessione 
La stringa di connessione specifica il nome utente che SQL Insights deve usare per l'accesso a SQL per eseguire le viste a gestione dinamica. Se si usa un Key Vault per archiviare la password per l'utente di monitoraggio, specificare l'URL e il nome del segreto da usare. 

La stringa di connessione varia per ogni tipo di risorsa SQL:

#### <a name="azure-sql-databases"></a>Database SQL di Azure 
Immettere la stringa di connessione nel formato:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Ottenere i dettagli dalla voce di menu **Stringhe di** connessione per il database.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Stringa di connessione del database SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Per monitorare un database secondario leggibile, includere key-value `ApplicationIntent=ReadOnly` nella stringa di connessione. SQL Insights supporta il monitoraggio di un singolo database secondario. I dati raccolti verranno contrassegnati in modo da riflettere il database primario o secondario. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Macchine virtuali di Azure che eseguono SQL Server 
Immettere la stringa di connessione nel formato:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Se la macchina virtuale di monitoraggio si trova nella stessa rete virtuale, usare l'indirizzo IP privato del server.  In caso contrario, usare l'indirizzo IP pubblico. Se si usa una Azure SQL virtuale, è possibile vedere quale porta usare qui nella **pagina** Sicurezza per la risorsa.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Sicurezza delle macchine virtuali SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Istanze gestite di SQL di Azure 
Immettere la stringa di connessione nel formato:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Ottenere i dettagli dalla voce di menu **Stringhe di** connessione per l'istanza gestita.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Stringa di Istanza gestita SQL" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Per monitorare un database secondario leggibile, includere key-value `ApplicationIntent=ReadOnly` nella stringa di connessione. SQL Insights supporta il monitoraggio di un singolo database secondario e i dati raccolti verranno contrassegnati in modo da riflettere primario o secondario. 


## <a name="monitoring-profile-created"></a>Profilo di monitoraggio creato 

Selezionare **Aggiungi macchina virtuale di monitoraggio** per configurare la macchina virtuale per raccogliere dati dalle risorse SQL. Non tornare alla **scheda** Panoramica.  In pochi minuti, la colonna Stato dovrebbe cambiare in "Raccolta". Dovrebbero essere visualizzati i dati per le risorse SQL che si è scelto di monitorare.

Se i dati non vengono visualizzati, vedere Risoluzione [dei problemi relativi alle informazioni dettagliate di SQL](sql-insights-troubleshoot.md) per identificare il problema. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profilo creato" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Se è necessario aggiornare il profilo di monitoraggio o le stringhe di connessione nelle macchine virtuali di monitoraggio, è possibile farlo tramite la scheda Gestisci profilo di SQL **Insights.**  Dopo aver salvato gli aggiornamenti, le modifiche verranno applicate in circa 5 minuti.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Risoluzione dei problemi relativi alle informazioni](sql-insights-troubleshoot.md) dettagliate DI SQL se le informazioni dettagliate SQL non funzionano correttamente dopo l'a attivazione.
