---
title: Script dell'interfaccia della riga di comando di Azure con il modulo AZ search
titleSuffix: Azure Cognitive Search
description: Creare e configurare un servizio ricerca cognitiva di Azure con l'interfaccia della riga di comando di Azure. È possibile aumentare o ridurre le prestazioni di un servizio, gestire chiavi API di amministrazione e query ed eseguire query per ottenere informazioni sul sistema.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 456aaf20c0b6d198ae353490d961a69a319b6601
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045111"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Gestire il servizio ricerca cognitiva di Azure con l'interfaccia della riga di comando di Azure
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Interfaccia della riga di comando di Azure](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

È possibile eseguire comandi e script dell'interfaccia della riga di comando di Azure in Windows, macOS, Linux o in [Azure cloud Shell](../cloud-shell/overview.md) per creare e configurare Azure ricerca cognitiva. Il modulo [**AZ Search**](/cli/azure/search) estende l' [interfaccia](/cli/) della riga di comando di Azure con la parità completa alle [API REST di gestione di ricerca](/rest/api/searchmanagement) e la possibilità di eseguire le attività seguenti:

> [!div class="checklist"]
> * [Elencare i servizi di ricerca in una sottoscrizione](#list-search-services)
> * [Restituisci informazioni sul servizio](#get-search-service-information)
> * [Creare o eliminare un servizio](#create-or-delete-a-service)
> * [Creazione di un servizio con un endpoint privato](#create-a-service-with-a-private-endpoint)
> * [Rigenera chiavi API di amministrazione](#regenerate-admin-keys)
> * [Creare o eliminare chiavi API di query](#create-or-delete-query-keys)
> * [Scalabilità verticale con repliche e partizioni](#scale-replicas-and-partitions)
> * [Creare una risorsa di collegamento privato condiviso](#create-a-shared-private-link-resource)

In alcuni casi, vengono poste domande sulle attività *non* presenti nell'elenco precedente. Attualmente, non è possibile usare il modulo **AZ Search** o l'API REST di gestione per modificare il nome, l'area o il livello del server. Le risorse dedicate vengono allocate quando viene creato un servizio. Di conseguenza, la modifica dell'hardware sottostante (tipo di percorso o nodo) richiede un nuovo servizio. Analogamente, non sono disponibili strumenti o API per il trasferimento di contenuto, ad esempio un indice, da un servizio a un altro.

All'interno di un servizio, la creazione e la gestione dei contenuti sono tramite [servizio di ricerca API REST](/rest/api/searchservice/) o [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Sebbene non esistano comandi di PowerShell dedicati per il contenuto, è possibile scrivere script che chiamano API REST o .NET per creare e caricare gli indici.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Elencare i servizi in una sottoscrizione

I comandi seguenti sono da [**AZ Resource**](/cli/azure/resource), restituendo informazioni sulle risorse e i servizi esistenti già sottoposti a provisioning nella sottoscrizione. Se non si conosce il numero di servizi di ricerca già creati, questi comandi restituiscono tali informazioni, salvando un viaggio nel portale.

Il primo comando restituisce tutti i servizi di ricerca.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Dall'elenco dei servizi, restituire informazioni su una risorsa specifica.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Elenca tutti i comandi AZ search

È possibile visualizzare le informazioni sui sottogruppi e i comandi disponibili in [**AZ Search**](/cli/azure/search) dall'interno dell'interfaccia della riga di comando. In alternativa, è possibile esaminare la [documentazione](/cli/azure/search).

Per visualizzare i sottogruppi disponibili in `az search` , eseguire il comando seguente.

```azurecli-interactive
az search --help
```

La risposta dovrebbe essere simile all'output seguente.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

All'interno di ogni sottogruppo sono disponibili più comandi. È possibile visualizzare i comandi disponibili per il `service` sottogruppo eseguendo la riga seguente.

```azurecli-interactive
az search service --help
```

È anche possibile visualizzare gli argomenti disponibili per un determinato comando.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Ottenere informazioni sul servizio di ricerca

Se si conosce il gruppo di risorse che contiene il servizio di ricerca, eseguire [**AZ Search Service Show**](/cli/azure/search/service#az_search_service_show) per restituire la definizione del servizio, inclusi i conteggi dei nomi, delle aree, dei livelli e delle partizioni.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Creare o eliminare un servizio

Per [creare un nuovo servizio di ricerca](search-create-service-portal.md), usare il comando [**AZ Search Service create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

I risultati dovrebbero essere simili all'output seguente:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Creare un servizio con regole IP

A seconda dei requisiti di sicurezza, potrebbe essere necessario creare un servizio di ricerca con un [firewall IP configurato](service-configure-firewall.md). A tale scopo, passare gli indirizzi IP pubblici (v4) o gli intervalli CIDR all' `ip-rules` argomento, come illustrato di seguito. Le regole devono essere separate da una virgola ( `,` ) o da un punto e virgola ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Creare un servizio con un'identità gestita assegnata dal sistema

In alcuni casi, ad esempio quando si [Usa l'identità gestita per connettersi a un'origine dati](search-howto-managed-identities-storage.md), sarà necessario attivare l' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md). Questa operazione viene eseguita aggiungendo `--identity-type SystemAssigned` al comando.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Creazione di un servizio con un endpoint privato

Gli [endpoint privati](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi della rete virtuale](../virtual-network/private-ip-addresses.md) per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Per altri dettagli, vedere la documentazione relativa alla [creazione di un endpoint privato per Azure ricerca cognitiva](service-create-private-endpoint.md)

Nell'esempio seguente viene illustrato come creare un servizio di ricerca con un endpoint privato.

Per prima cosa, distribuire un servizio di ricerca con `PublicNetworkAccess` impostato su `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Successivamente, creare una rete virtuale e l'endpoint privato.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Infine, creare una zona DNS privata. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Per altre informazioni sulla creazione di endpoint privati in PowerShell, vedere questa [Guida introduttiva al collegamento privato](../private-link/create-private-endpoint-cli.md)

### <a name="manage-private-endpoint-connections"></a>Gestire le connessioni endpoint privato

Oltre a creare una connessione all'endpoint privato, è anche possibile `show` , `update` e `delete` la connessione.

Per recuperare una connessione a un endpoint privato e visualizzarne lo stato, usare [**AZ Search private-endpoint-Connection Show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Per aggiornare la connessione, usare [**AZ Search private-endpoint-Connection Update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). Nell'esempio seguente viene impostata una connessione all'endpoint privato rifiutata:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Per eliminare la connessione all'endpoint privato, usare [**AZ Search private-endpoint-Connection Delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Riscrivere una chiave amministratore

Per eseguire il rollover delle [chiavi API](search-security-api-keys.md)di amministrazione, usare [**AZ Search Admin-Key Renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Vengono create due chiavi amministratore con ogni servizio per l'accesso autenticato. Le chiavi sono obbligatorie per ogni richiesta. Entrambe le chiavi di amministrazione sono equivalenti dal punto di vista funzionale, concedendo l'accesso in scrittura completo a un servizio di ricerca con la possibilità di recuperare informazioni oppure di creare ed eliminare qualsiasi oggetto. Esistono due chiavi in modo che sia possibile utilizzarne una mentre si sostituisce l'altra. 

È possibile rigenerare solo una alla volta, specificata come `primary` `secondary` chiave o. Per il servizio senza interruzioni, ricordarsi di aggiornare tutto il codice client per l'uso di una chiave secondaria durante il rollover della chiave primaria. Evitare di modificare le chiavi mentre le operazioni sono in corso.

Come si può immaginare, se si rigenerano le chiavi senza aggiornare il codice client, le richieste che usano la chiave precedente avranno esito negativo. La rigenerazione di tutte le nuove chiavi non blocca definitivamente il servizio ed è comunque possibile accedere al servizio tramite il portale. Dopo la rigenerazione delle chiavi primarie e secondarie, è possibile aggiornare il codice client per l'uso delle nuove chiavi e le operazioni riprenderanno di conseguenza.

I valori per le chiavi API vengono generati dal servizio. Non è possibile fornire una chiave personalizzata per l'uso di ricerca cognitiva di Azure. Analogamente, non esiste alcun nome definito dall'utente per le chiavi API di amministrazione. I riferimenti alla chiave sono stringhe fisse, `primary` ovvero o `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

I risultati dovrebbero essere simili all'output seguente. Entrambe le chiavi vengono restituite anche se si modifica solo una alla volta.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Creare o eliminare chiavi di query

Per creare [chiavi API](search-security-api-keys.md) di query per l'accesso in sola lettura dalle app client a un indice di ricerca cognitiva di Azure, usare [**AZ search query-Key create**](/cli/azure/search/query-key#az_search_query_key_create). Le chiavi di query vengono usate per eseguire l'autenticazione a un indice specifico allo scopo di recuperare i risultati della ricerca. Le chiavi di query non concedono l'accesso in sola lettura ad altri elementi nel servizio, ad esempio un indice, un'origine dati o un indicizzatore.

Non è possibile fornire una chiave per l'uso di ricerca cognitiva di Azure. Le chiavi API vengono generate dal servizio.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Ridimensionare le repliche e le partizioni

Per [aumentare o diminuire le repliche e le partizioni,](search-capacity-planning.md) usare [**AZ Search Service Update**](/cli/azure/search/service#az_search_service_update). Aumentare le repliche o le partizioni aggiunge la fattura, con costi fissi e variabili. Se è necessaria una capacità di elaborazione aggiuntiva temporanea, è possibile aumentare le repliche e le partizioni per gestire il carico di lavoro. Nell'area monitoraggio della pagina del portale di panoramica sono presenti riquadri sulla latenza delle query, le query al secondo e la limitazione, che indica se la capacità corrente è adeguata.

L'aggiunta o la rimozione della Resourcing può richiedere del tempo. Le modifiche alla capacità si verificano in background, consentendo la continuazione dei carichi di lavoro esistenti. La capacità aggiuntiva viene utilizzata per le richieste in ingresso non appena è pronta, senza che sia necessaria alcuna configurazione aggiuntiva. 

La rimozione della capacità può risultare problematica. Per evitare le richieste Eliminate, è consigliabile arrestare tutti i processi di indicizzazione e di indicizzatore prima di ridurre la capacità. Se ciò non è fattibile, è possibile valutare la possibilità di ridurre in modo incrementale la capacità, una replica e una partizione alla volta, fino a raggiungere i nuovi livelli di destinazione.

Una volta inviato il comando, non è possibile terminarlo a metà. Prima di rivedere i conteggi, sarà necessario attendere il completamento del comando.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Oltre ad aggiornare il numero di repliche e partizioni, è anche possibile aggiornare `ip-rules` , `public-access` e `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Creare una risorsa di collegamento privato condiviso

Gli endpoint privati delle risorse protette create tramite le API di Azure ricerca cognitiva vengono definiti risorse di *collegamento privato condiviso*. Questo perché si sta "condividendo" l'accesso a una risorsa, ad esempio un account di archiviazione, che è stato integrato con il [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

Se si usa un indicizzatore per indicizzare i dati in Azure ricerca cognitiva e l'origine dati si trova in una rete privata, è possibile creare una [connessione all'endpoint privato](../private-link/private-endpoint-overview.md) in uscita per raggiungere i dati.

Un elenco completo delle risorse di Azure per cui è possibile creare endpoint privati in uscita da Azure ricerca cognitiva è disponibile [qui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) insieme ai valori **ID gruppo** correlati.

Per creare la risorsa di collegamento privato condiviso, usare [**AZ Search Shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Tenere presente che alcune configurazioni potrebbero essere necessarie per l'origine dati prima di eseguire questo comando.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Per recuperare le risorse di collegamento privato condivise e visualizzarne lo stato, usare [**AZ Search Shared-private-link-Resource List**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Prima di poter usare la connessione, è necessario approvare la connessione con il comando seguente. È necessario recuperare l'ID della connessione all'endpoint privato dalla risorsa figlio. In questo caso, si ottiene l'ID connessione da AZ storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Per eliminare la risorsa di collegamento privato condiviso, usare [**AZ Search Shared-private-link-Resource Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Per informazioni dettagliate sulla configurazione delle risorse di collegamento privato condivise, vedere la documentazione relativa alla [creazione di connessioni dell'indicizzatore tramite un endpoint privato](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Passaggi successivi

Compilare un [Indice](search-what-is-an-index.md), [eseguire una query su un indice](search-query-overview.md) usando il portale, le API REST o .NET SDK.

* [Creare un indice di Ricerca cognitiva di Azure nel portale di Azure](search-get-started-portal.md)
* [Configurare un indicizzatore per caricare i dati da altri servizi](search-indexer-overview.md)
* [Eseguire query su un indice di ricerca cognitiva di Azure usando Esplora ricerche nella portale di Azure](search-explorer.md)
* [Come usare ricerca cognitiva di Azure in .NET](search-howto-dotnet-sdk.md)