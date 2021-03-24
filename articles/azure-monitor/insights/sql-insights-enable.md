---
title: Abilita SQL Insights
description: Abilitare SQL Insights in monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: ac37a6de4197d5e7cae20d2bde759b98fe474047
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889621"
---
# <a name="enable-sql-insights-preview"></a>Abilita SQL Insights (anteprima)
Questo articolo descrive come abilitare [SQL Insights](sql-insights-overview.md) per monitorare le distribuzioni SQL. Il monitoraggio viene eseguito da una macchina virtuale di Azure che effettua una connessione alle distribuzioni SQL e usa le viste a gestione dinamica (DMV) per raccogliere i dati di monitoraggio. È possibile controllare quali set di dati vengono raccolti e la frequenza della raccolta usando un profilo di monitoraggio.

## <a name="create-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics
SQL Insights archivia i dati in una o più [aree di lavoro log Analytics](../logs/data-platform-logs.md#log-analytics-workspaces).  Prima di poter abilitare SQL Insights, è necessario [creare un'area di lavoro](../logs/quick-create-workspace.md) o selezionarne una esistente. Una singola area di lavoro può essere usata con più profili di monitoraggio, ma l'area di lavoro e i profili devono trovarsi nella stessa area di Azure. Per abilitare e accedere alle funzionalità in SQL Insights, è necessario avere il [ruolo di collaboratore log Analytics](../logs/manage-access.md) nell'area di lavoro. 

## <a name="create-monitoring-user"></a>Crea utente monitoraggio 
È necessario un utente nelle distribuzioni SQL che si vuole monitorare. Seguire le procedure riportate di seguito per diversi tipi di distribuzioni SQL.

### <a name="azure-sql-database"></a>Database SQL di Azure
Aprire il database SQL di Azure con [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o l' [editor di query (anteprima)](../../azure-sql/database/connect-query-portal.md) nel portale di Azure.

Eseguire lo script seguente per creare un utente con le autorizzazioni necessarie. Sostituire *utente* con un nome utente e *mystrongpassword* con una password.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Creare lo script utente Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Verificare che l'utente sia stato creato.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verificare lo script utente Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Istanza gestita di SQL di Azure
Accedere al Istanza gestita SQL di Azure e usare [SSMS](../../azure-sql/database/connect-query-ssms.md) o uno strumento simile per eseguire lo script seguente per creare l'utente di monitoraggio con le autorizzazioni necessarie. Sostituire *utente* con un nome utente e *mystrongpassword* con una password.

 
```
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
Accedere alla macchina virtuale di Azure che esegue SQL Server e usare [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o uno strumento simile per eseguire lo script seguente per creare l'utente di monitoraggio con le autorizzazioni necessarie. Sostituire *utente* con un nome utente e *mystrongpassword* con una password.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Creare una macchina virtuale di Azure 
Sarà necessario creare una o più macchine virtuali di Azure che verranno usate per raccogliere dati per il monitoraggio di SQL.  

> [!NOTE]
>  I [profili di monitoraggio](#create-sql-monitoring-profile) specificano i dati che si raccoglieranno dai diversi tipi di SQL che si desidera monitorare. A ogni macchina virtuale di monitoraggio può essere associato un solo profilo di monitoraggio. Se è necessario disporre di più profili di monitoraggio, è necessario creare una macchina virtuale per ognuno di essi.

### <a name="azure-virtual-machine-requirements"></a>Requisiti delle macchine virtuali di Azure
Per le macchine virtuali di Azure sono previsti i requisiti seguenti.

- Sistema operativo: Ubuntu 18,04 
- Dimensioni consigliate per le macchine virtuali di Azure: Standard_B2s (2 CPU, 4 GiB di memoria) 
- Aree supportate: qualsiasi [area supportata dall'agente di monitoraggio di Azure](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Le dimensioni della macchina virtuale Standard_B2s (2 CPU, 4 GiB di memoria) supporteranno fino a 100 stringhe di connessione. Non è consigliabile allocare più di 100 connessioni a una singola macchina virtuale.

Le macchine virtuali devono essere posizionate nella stessa VNET dei sistemi SQL in modo che possano creare connessioni di rete per raccogliere i dati di monitoraggio. Se si usa la macchina virtuale di monitoraggio per monitorare SQL in esecuzione in macchine virtuali di Azure o come Istanza gestita di Azure, considerare la possibilità di collocare la macchina virtuale di monitoraggio in un gruppo di sicurezza delle applicazioni o nella stessa rete virtuale delle risorse in modo da non dover fornire un endpoint di rete pubblico per il monitoraggio di SQL Server. 

## <a name="configure-network-settings"></a>Configurare le impostazioni di rete
Ogni tipo di SQL offre metodi che consentono alla macchina virtuale di monitoraggio di accedere in modo sicuro a SQL.  Le sezioni seguenti illustrano le opzioni basate sul tipo di SQL.

### <a name="azure-sql-databases"></a>Database SQL di Azure  

SQL Insights supporta l'accesso al database SQL di Azure tramite l'endpoint pubblico, nonché la rete virtuale.

Per l'accesso tramite l'endpoint pubblico, è necessario aggiungere una regola nella pagina **delle impostazioni del firewall** e nella sezione [impostazioni del firewall IP](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) .  Per specificare l'accesso da una rete virtuale, è possibile impostare le regole del firewall per la [rete virtuale](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) e impostare i [tag di servizio richiesti dall'agente di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  [Questo articolo](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) descrive le differenze tra questi due tipi di regole del firewall.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Impostare il firewall del server" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Impostazioni del firewall." lightbox="media/sql-insights-enable/firewall-settings.png":::

> [!NOTE]
> SQL Insights attualmente non supporta l'endpoint privato di Azure per il database SQL di Azure.  È consigliabile usare i [tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) nel gruppo di sicurezza di rete o nelle impostazioni del firewall di rete virtuale supportate dall'agente di monitoraggio di [Azure](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).

### <a name="azure-sql-managed-instances"></a>Istanze gestite di SQL di Azure 

Se la macchina virtuale di monitoraggio si troverà nella stessa VNet delle risorse di SQL MI, vedere [Connetti all'interno della stessa VNet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Se la macchina virtuale di monitoraggio si troverà in un VNet diverso da quello delle risorse di SQL MI, vedere [connettersi all'interno di una VNet diversa](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Macchina virtuale di Azure e macchina virtuale SQL di Azure  
Se la macchina virtuale di monitoraggio si trova nella stessa VNet delle risorse della macchina virtuale SQL, vedere [connettersi a SQL Server all'interno di una rete virtuale](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Se la macchina virtuale di monitoraggio si troverà in una VNet diversa da quella delle risorse della macchina virtuale SQL, vedere  [connettersi a SQL Server tramite Internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Archivia la password di monitoraggio in Key Vault
È consigliabile archiviare le password di connessione utente SQL in una Key Vault anziché immetterle direttamente nelle stringhe di connessione del profilo di monitoraggio.

Quando si esegue la configurazione del profilo per il monitoraggio di SQL, è necessario disporre di una delle seguenti autorizzazioni per la risorsa Key Vault che si intende usare:

- Microsoft.Authorization/roleAssignments/write 
- Autorizzazioni Microsoft. Authorization/roleAssignments/Delete, ad esempio amministratore accesso utenti o proprietario 

Un nuovo criterio di accesso verrà creato automaticamente nell'ambito della creazione del profilo di SQL Monitoring che usa il Key Vault specificato. Usare *Consenti accesso da tutte le reti* per Key Vault impostazioni di rete.


## <a name="create-sql-monitoring-profile"></a>Crea profilo di monitoraggio SQL
Aprire SQL Insights selezionando **SQL (anteprima)** nella sezione **Insights** del menu di monitoraggio di **Azure** nel portale di Azure. Fare clic su **Crea nuovo profilo**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Crea nuovo profilo." lightbox="media/sql-insights-enable/create-new-profile.png":::

Il profilo memorizzerà le informazioni che si desidera raccogliere dai sistemi SQL.  Dispone di impostazioni specifiche per: 

- Database SQL di Azure 
- Istanze gestite di SQL di Azure 
- SQL Server in esecuzione nelle macchine virtuali  

Ad esempio, è possibile creare un profilo denominato *SQL Production* e un altro denominato *staging di SQL* con impostazioni diverse per la frequenza della raccolta dei dati, i dati da raccogliere e l'area di lavoro a cui inviare i dati. 

Il profilo viene archiviato come risorsa della [regola di raccolta dati](../agents/data-collection-rule-overview.md) nella sottoscrizione e nel gruppo di risorse selezionati. Per ogni profilo sono necessari gli elementi seguenti:

- Name. Non può essere modificato dopo la creazione.
- Posizione. Si tratta di un'area di Azure.
- Log Analytics area di lavoro per archiviare i dati di monitoraggio.
- Impostazioni di raccolta per la frequenza e il tipo di dati di monitoraggio SQL da raccogliere.

> [!NOTE]
> Il percorso del profilo deve trovarsi nella stessa posizione dell'area di lavoro Log Analytics a cui si intende inviare i dati di monitoraggio.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Dettagli del profilo." lightbox="media/sql-insights-enable/profile-details.png":::

Fare clic su **Crea profilo di monitoraggio** dopo aver immesso i dettagli per il profilo di monitoraggio. La distribuzione del profilo può richiedere fino a un minuto.  Se il nuovo profilo non è elencato nella casella combinata **profilo di monitoraggio** , fare clic sul pulsante Aggiorna per visualizzare una volta completata la distribuzione.  Dopo aver selezionato il nuovo profilo, selezionare la scheda **Gestisci profilo** per aggiungere un computer di monitoraggio che verrà associato al profilo.

### <a name="add-monitoring-machine"></a>Aggiungi computer di monitoraggio
Selezionare **Aggiungi computer di monitoraggio** per aprire un pannello del contesto per scegliere la macchina virtuale da configurare per monitorare le istanze di SQL e fornire le stringhe di connessione.

Selezionare la sottoscrizione e il nome della macchina virtuale di monitoraggio. Se si usa Key Vault per archiviare la password per l'utente di monitoraggio, selezionare le risorse Key Vault con questi segreti e immettere l'URL e il nome del segreto da usare nelle stringhe di connessione. Vedere la sezione successiva per informazioni dettagliate sull'identificazione della stringa di connessione per diverse distribuzioni SQL.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Aggiungere il computer di monitoraggio." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Aggiungi stringhe di connessione 
La stringa di connessione specifica il nome utente che deve essere utilizzato da SQL Insights per l'accesso a SQL per l'esecuzione delle viste a gestione dinamica. Se si usa un Key Vault per archiviare la password per l'utente di monitoraggio, fornire l'URL e il nome del segreto da usare. 

La stringa di connessione può variare per ogni tipo di risorsa SQL:

#### <a name="azure-sql-databases"></a>Database SQL di Azure 
Immettere la stringa di connessione nel formato seguente:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Ottenere i dettagli dalla voce di menu **stringhe di connessione** per il database.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Stringa di connessione del database SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Per monitorare un database secondario leggibile, includere il valore chiave `ApplicationIntent=ReadOnly` nella stringa di connessione.


#### <a name="azure-virtual-machines-running-sql-server"></a>Macchine virtuali di Azure che eseguono SQL Server 
Immettere la stringa di connessione nel formato seguente:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Se la macchina virtuale di monitoraggio si trova nella stessa VNET, usare l'indirizzo IP privato del server.  In caso contrario, usare l'indirizzo IP pubblico. Se si usa la macchina virtuale SQL di Azure, è possibile visualizzare la porta da usare qui nella pagina **sicurezza** per la risorsa.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Sicurezza della macchina virtuale SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Per monitorare un database secondario leggibile, includere il valore chiave `ApplicationIntent=ReadOnly` nella stringa di connessione.


### <a name="azure-sql-managed-instances"></a>Istanze gestite di SQL di Azure 
Immettere la stringa di connessione nel formato seguente:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Ottenere i dettagli dalla voce di menu **stringhe di connessione** per l'istanza gestita.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Stringa di connessione di SQL Istanza gestita" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Per monitorare un database secondario leggibile, includere il valore chiave `ApplicationIntent=ReadOnly` nella stringa di connessione.



## <a name="profile-created"></a>Profilo creato 
Selezionare **Aggiungi macchina virtuale di monitoraggio** per configurare la macchina virtuale per la raccolta di dati dalle distribuzioni SQL. Non tornare alla scheda **Panoramica** .  In pochi minuti la colonna stato dovrebbe cambiare per indicare "raccolta". verranno visualizzati i dati per i sistemi scelti per il monitoraggio.

Se i dati non vengono visualizzati, vedere [risoluzione dei problemi relativi a SQL Insights](sql-insights-troubleshoot.md) per identificare il problema. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profilo creato" lightbox="media/sql-insights-enable/profile-created.png":::

## <a name="next-steps"></a>Passaggi successivi

- Vedere [risoluzione dei problemi di SQL Insights](sql-insights-troubleshoot.md) se SQL Insights non funziona correttamente dopo l'abilitazione.
