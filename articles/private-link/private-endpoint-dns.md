---
title: Configurazione DNS dell'endpoint privato di Azure
description: Informazioni sulla configurazione DNS dell'endpoint privato di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: 42c5b315c9c3560c400c685448a11dc61bf64eb6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215613"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configurazione DNS dell'endpoint privato di Azure

È importante configurare correttamente le impostazioni DNS per risolvere l'indirizzo IP dell'endpoint privato per il nome di dominio completo (FQDN) della stringa di connessione.

I servizi di Microsoft Azure esistenti potrebbero avere già una configurazione DNS per un endpoint pubblico. È necessario eseguire l'override di questa configurazione per connettersi usando l'endpoint privato. 
 
L'interfaccia di rete associata all'endpoint privato contiene le informazioni per la configurazione del DNS. Le informazioni sull'interfaccia di rete includono FQDN e indirizzi IP privati per la risorsa di collegamento privato. 
 
Per configurare le impostazioni DNS per gli endpoint privati si può procedere nei modi seguenti: 
- **Usare il file host (consigliato solo per i test)**. È possibile usare il file host in una macchina virtuale per eseguire l'override del DNS.  
- **Usare una zona DNS privato**. È possibile usare le [zone DNS private](../dns/private-dns-privatednszone.md) per sostituire la risoluzione DNS per un endpoint privato. Una zona DNS privato può essere collegata alla rete virtuale per risolvere domini specifici.
- **Usare il server di trasmissione DNS (facoltativo)**. Per eseguire l'override della risoluzione DNS per una risorsa di collegamento privato, è possibile usare il server di trasmissione DNS. Creare una regola di invio DNS per usare una zona DNS privata nel [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ospitata in una rete virtuale.

> [!IMPORTANT]
> Non è consigliabile eseguire l'override di una zona che viene usata attivamente per risolvere gli endpoint pubblici. Le connessioni alle risorse non potranno essere risolte correttamente senza inoltro DNS al DNS pubblico. Per evitare problemi, creare un nome di dominio diverso o usare il nome suggerito per ogni servizio indicato di seguito. 

## <a name="azure-services-dns-zone-configuration"></a>Configurazione della zona DNS dei servizi di Azure
Azure crea un record DNS di nome canonico (CNAME) nel DNS pubblico. Il record CNAME reindirizza la risoluzione al nome di dominio privato. È possibile eseguire l'override della risoluzione con l'indirizzo IP privato degli endpoint privati. 
 
Le applicazioni non devono modificare l'URL di connessione. Quando si esegue la risoluzione in un servizio DNS pubblico, il server DNS viene risolto negli endpoint privati. Il processo non influisce sulle applicazioni esistenti. 

> [!IMPORTANT]
> Le reti private che usano già la zona DNS privata per un determinato tipo possono connettersi alle risorse pubbliche solo se non dispongono di connessioni a endpoint privato. in caso contrario, nella zona DNS privata è necessaria una configurazione DNS corrispondente per completare la sequenza di risoluzione DNS. 

Per i servizi di Azure, usare i nomi di zona consigliati come descritto nella tabella seguente:

| Tipo di risorsa collegamento privato/sottorisorsa |Nome zona DNS privato | Server d'inoltro zona DNS pubblico |
|---|---|---|
| Automazione di Azure/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Database SQL di Azure (Microsoft. SQL/Servers)/sqlServer | privatelink.database.windows.net | database.windows.net |
| Analisi delle sinapsi di Azure (Microsoft. SQL/Servers)/sqlServer  | privatelink.database.windows.net | database.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/table (tabella, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Queue (Queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/file (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Account di archiviazione (Microsoft. storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake file System Gen2 (Microsoft. storage/storageAccounts)/Data Lake file System Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Database di Azure per PostgreSQL - Server singolo (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Database di Azure per MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Database di Azure per MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Servizio Azure Kubernetes-API Kubernetes (Microsoft. servizio contenitore/managedClusters)/gestione | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Ricerca di Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Registro Azure Container (Microsoft.ContainerRegistry/registries) / registro | privatelink.azurecr.io | azurecr.io |
| Configurazione app di Azure (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Backup di Azure (Microsoft.RecoveryServices/vaults) / vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Site Recovery (Microsoft. RecoveryServices/Vaults)/Vault | {Region}. privatelink. siterecovery. WindowsAzure. com | {Region}. hypervrecoverymanager. WindowsAzure. com |
| Hub eventi di Azure (Microsoft. EventHub/namespaces)/spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Bus di servizio di Azure (Microsoft.ServiceBus/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Hub Azure Internet (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Inoltro di Azure (Microsoft.Relay/namespaces) / spazio dei nomi | privatelink.servicebus.windows.net | servicebus.windows.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/topics) / argomento | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Griglia di eventi di Azure (Microsoft.EventGrid/domains) / dominio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| App Web di Azure (Microsoft. Web/sites)/siti | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)/amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>instances.azureml.ms<br/>aznbcontent.net |
| SignalR (Microsoft. SignalRService/SignalR)/SignalR | privatelink.service.signalr.net | service.signalr.net |
| Monitoraggio di Azure (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Servizi cognitivi (Microsoft. CognitiveServices/accounts)/account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Sincronizzazione file di Azure (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/DataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/portale |  privatelink.adf.azure.com  |  adf.azure.com  |
| Cache di Azure per Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> Da usare con l'endpoint compatibile con l'hub eventi predefinito dell'hub Internet. Per altre informazioni, vedere [supporto dei collegamenti privati per l'endpoint predefinito dell'hub](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>Cina

| Tipo di risorsa collegamento privato/sottorisorsa |Nome zona DNS privato | Server d'inoltro zona DNS pubblico |
|---|---|---|
| Database SQL di Azure (Microsoft. SQL/Servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Database di Azure per PostgreSQL - Server singolo (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Database di Azure per MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Database di Azure per MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |

## <a name="dns-configuration-scenarios"></a>Scenari di configurazione DNS

Il nome di dominio completo (FQDN) dei servizi viene risolto automaticamente in un indirizzo IP pubblico. Per risolvere l'indirizzo IP privato dell'endpoint privato, modificare la configurazione DNS.

DNS è un componente fondamentale per garantire il corretto funzionamento dell'applicazione risolvendo correttamente l'indirizzo IP dell'endpoint privato.

In base alle proprie preferenze, con la risoluzione DNS integrata sono disponibili gli scenari seguenti:

- [Carichi di lavoro di rete virtuale senza server DNS personalizzato](#virtual-network-workloads-without-custom-dns-server)
- [Carichi di lavoro locali con server d'inoltro DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Rete virtuale e carichi di lavoro locali usando un server di un server d'invio](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> Il [proxy DNS del firewall di Azure](../firewall/dns-settings.md#dns-proxy) può essere usato come server d'inoltro DNS per i carichi di lavoro [locali](#on-premises-workloads-using-a-dns-forwarder) e i carichi di [lavoro della rete virtuale usando un server di inoltro DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Carichi di lavoro di rete virtuale senza server DNS personalizzato

Questa configurazione è appropriata per i carichi di lavoro della rete virtuale senza un server DNS personalizzato. In questo scenario, il client esegue una query per l'indirizzo IP dell'endpoint privato per il servizio DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Il servizio DNS di Azure sarà responsabile della risoluzione DNS delle zone DNS privato.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete virtuale client

- DNS privato zona [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)  con [record di tipo a](../dns/dns-zones-records.md#record-types)

- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Lo screenshot seguente illustra la sequenza di risoluzione DNS dei carichi di lavoro della rete virtuale con la zona DNS privata:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rete virtuale singola e DNS fornito da Azure":::

È possibile estendere questo modello alle reti virtuali con peering associate allo stesso endpoint privato. [Aggiungere nuovi collegamenti della rete virtuale](../dns/private-dns-virtual-network-links.md) alla zona DNS privata per tutte le reti virtuali con peering.

> [!IMPORTANT]
> Per questa configurazione è necessaria una singola zona DNS privata. Per creare più zone con lo stesso nome per reti virtuali diverse è necessario eseguire operazioni manuali per unire i record DNS.

> [!IMPORTANT]
> Se si usa un endpoint privato in un modello hub e spoke da una sottoscrizione diversa, riusare la stessa zona DNS privata nell'hub.

In questo scenario è presente una topologia di rete [hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) . Le reti spoke condividono un endpoint privato. Le reti virtuali spoke sono collegate alla stessa zona DNS privata. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub-spoke con DNS fornito da Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Carichi di lavoro locali con server d'inoltro DNS

Per i carichi di lavoro locali per risolvere il nome di dominio completo di un endpoint privato, usare un server di un server di un server d'utilità per risolvere la [zona DNS pubblica](#azure-services-dns-zone-configuration) del servizio di Azure in Azure.

Lo scenario seguente è relativo a una rete locale che dispone di un server d'invio DNS in Azure. Questo server d'inoltre risolve le query DNS tramite un server d'avanzamento a livello di server nel [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS fornito da Azure. 

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Rete virtuale [connessa a locale](/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure 
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md) con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la sequenza di risoluzione DNS da una rete locale. La configurazione usa un server di distribuzione DNS distribuito in Azure. La risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Rete locale che usa DNS di Azure":::

Questa configurazione può essere estesa per una rete locale in cui è già presente una soluzione DNS. La soluzione DNS locale è configurata per l'invio del traffico DNS a DNS di Azure tramite un [Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)d'istruzione. Il server d'avanzamento condizionale fa riferimento al server di distribuzione DNS distribuito in Azure.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration)

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale con una soluzione DNS personalizzata 
- Rete virtuale [connessa a locale](/azure/architecture/reference-architectures/hybrid-networking/)
- Server d'inoltro DNS distribuito in Azure
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)  con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente illustra la risoluzione DNS da una rete locale. La risoluzione DNS viene trasmessa in modo condizionale ad Azure. La risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> L'invio condizionale deve essere eseguito al server d'istruzione [pubblico di zona DNS pubblico](#azure-services-dns-zone-configuration)consigliato. Ad esempio: `database.windows.net` invece di **privatelink**. database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Inoltro locale al servizio DNS di Azure":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Rete virtuale e carichi di lavoro locali usando un server di un server d'invio

Per i carichi di lavoro che accedono a un endpoint privato da reti virtuali e locali, usare un server di trasmissione DNS per risolvere la [zona DNS pubblica](#azure-services-dns-zone-configuration) del servizio di Azure distribuita in Azure.

Lo scenario seguente è relativo a una rete locale con reti virtuali in Azure. Entrambe le reti accedono all'endpoint privato che si trova in una rete Hub condivisa.

Questo server d'inoltre DNS è responsabile della risoluzione di tutte le query DNS tramite un server di un server d'invio al servizio DNS fornito da Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Per questa configurazione è necessaria una singola zona DNS privata. Tutte le connessioni client effettuate da reti virtuali locali e con [peering](../virtual-network/virtual-network-peering-overview.md) devono usare anche la stessa zona DNS privata.

> [!NOTE]
> Questo scenario usa la zona DNS privata consigliata del database SQL di Azure. Per altri servizi, è possibile modificare il modello usando il riferimento seguente: [configurazione della zona DNS dei servizi di Azure](#azure-services-dns-zone-configuration).

Per configurare correttamente, sono necessarie le risorse seguenti:

- Rete locale
- Rete virtuale [connessa a locale](/azure/architecture/reference-architectures/hybrid-networking/)
- [Rete virtuale con peering](../virtual-network/virtual-network-peering-overview.md) 
- Server d'inoltro DNS distribuito in Azure
- DNS privato zone [privatelink.database.Windows.NET](../dns/private-dns-privatednszone.md)  con [record di tipo a](../dns/dns-zones-records.md#record-types)
- Informazioni sull'endpoint privato (nome del record FQDN e indirizzo IP privato)

Il diagramma seguente mostra la risoluzione DNS per reti locali e virtuali. La risoluzione sta usando un server di trasmissione DNS. La risoluzione viene eseguita da una zona DNS privata [collegata a una rete virtuale](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Scenario ibrido":::

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sugli endpoint privati](private-endpoint-overview.md)
