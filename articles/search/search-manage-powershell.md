---
title: Script di PowerShell con il modulo AZ. search
titleSuffix: Azure Cognitive Search
description: Creare e configurare un servizio ricerca cognitiva di Azure con PowerShell. È possibile aumentare o ridurre le prestazioni di un servizio, gestire chiavi API di amministrazione e query ed eseguire query per ottenere informazioni sul sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: c992693bfb278ac559feb6fa82fa947086ceafbb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381138"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gestire il servizio ricerca cognitiva di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

È possibile eseguire i cmdlet e gli script di PowerShell in Windows, Linux o in [Azure cloud Shell](../cloud-shell/overview.md) per creare e configurare Azure ricerca cognitiva. Il modulo **AZ. search** estende [Azure PowerShell](/powershell/) con la parità completa alle [API REST di gestione di ricerca](/rest/api/searchmanagement) e la possibilità di eseguire le attività seguenti:

> [!div class="checklist"]
> * [Elencare i servizi di ricerca in una sottoscrizione](#list-search-services)
> * [Restituisci informazioni sul servizio](#get-search-service-information)
> * [Creare o eliminare un servizio](#create-or-delete-a-service)
> * [Creazione di un servizio con un endpoint privato](#create-a-service-with-a-private-endpoint)
> * [Rigenera chiavi API di amministrazione](#regenerate-admin-keys)
> * [Creare o eliminare chiavi API di query](#create-or-delete-query-keys)
> * [Scalabilità verticale con repliche e partizioni](#scale-replicas-and-partitions)
> * [Creare una risorsa di collegamento privato condiviso](#create-a-shared-private-link-resource)

In alcuni casi, vengono poste domande sulle attività *non* presenti nell'elenco precedente. Attualmente, non è possibile usare il modulo **AZ. search** o l'API REST di gestione per modificare il nome, l'area o il livello del server. Le risorse dedicate vengono allocate quando viene creato un servizio. Di conseguenza, la modifica dell'hardware sottostante (tipo di percorso o nodo) richiede un nuovo servizio. Analogamente, non sono disponibili strumenti o API per il trasferimento di contenuto, ad esempio un indice, da un servizio a un altro.

All'interno di un servizio, la creazione e la gestione dei contenuti sono tramite [servizio di ricerca API REST](/rest/api/searchservice/) o [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Sebbene non esistano comandi di PowerShell dedicati per il contenuto, è possibile scrivere script di PowerShell che chiama le API REST o .NET per creare e caricare gli indici.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Controllare le versioni e caricare i moduli

Gli esempi in questo articolo sono interattivi e richiedono autorizzazioni elevate. È necessario installare Azure PowerShell ( **AZ** Module). Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Verifica della versione di PowerShell (5,1 o versione successiva)

PowerShell locale deve essere 5,1 o versione successiva in qualsiasi sistema operativo supportato.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carica Azure PowerShell

Se non si è certi se **AZ** è installato, eseguire il comando seguente come passaggio di verifica. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alcuni sistemi non caricano automaticamente i moduli. Se viene visualizzato un errore nel comando precedente, provare a caricare il modulo e, in caso di errore, tornare alle istruzioni di installazione per vedere se non è stato eseguito alcun passaggio.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Connettersi ad Azure con un token di accesso del browser

È possibile usare le credenziali di accesso del portale per connettersi a una sottoscrizione in PowerShell. In alternativa, è possibile [eseguire l'autenticazione in modo non interattivo con un'entità servizio](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se si contengono più sottoscrizioni di Azure, impostare la sottoscrizione di Azure. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Elencare i servizi in una sottoscrizione

I comandi seguenti sono da [**AZ. resources**](/powershell/module/az.resources), restituendo informazioni sulle risorse e i servizi esistenti già sottoposti a provisioning nella sottoscrizione. Se non si conosce il numero di servizi di ricerca già creati, questi comandi restituiscono tali informazioni, salvando un viaggio nel portale.

Il primo comando restituisce tutti i servizi di ricerca.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Dall'elenco dei servizi, restituire informazioni su una risorsa specifica.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

I risultati dovrebbero essere simili all'output seguente.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importazione AZ. search

I comandi di [**AZ. search**](/powershell/module/az.search) non sono disponibili finché non si carica il modulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Elenca tutti i comandi AZ. search

Come passaggio di verifica, restituire un elenco di comandi disponibili nel modulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

I risultati dovrebbero essere simili all'output seguente.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Se si dispone di una versione precedente del pacchetto, aggiornare il modulo per ottenere la funzionalità più recente.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Ottenere informazioni sul servizio di ricerca

Dopo aver importato **AZ. search** e aver stabilito il gruppo di risorse contenente il servizio di ricerca, eseguire [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) per restituire la definizione del servizio, inclusi il nome, l'area, il livello e i conteggi della replica e della partizione.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

I risultati dovrebbero essere simili all'output seguente.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Creare o eliminare un servizio

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey) viene usato per [creare un nuovo servizio di ricerca](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
I risultati dovrebbero essere simili all'output seguente.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Creare un servizio con regole IP

A seconda dei requisiti di sicurezza, potrebbe essere necessario creare un servizio di ricerca con un [firewall IP configurato](service-configure-firewall.md). A tale scopo, definire innanzitutto le regole IP e passarle al parametro, `IPRuleList` come illustrato di seguito.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Creare un servizio con un'identità gestita assegnata dal sistema

In alcuni casi, ad esempio quando si [Usa l'identità gestita per connettersi a un'origine dati](search-howto-managed-identities-storage.md), sarà necessario attivare l' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md). Questa operazione viene eseguita aggiungendo `-IdentityType SystemAssigned` al comando.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Creazione di un servizio con un endpoint privato

Gli [endpoint privati](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello [spazio di indirizzi della rete virtuale](../virtual-network/private-ip-addresses.md) per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Per altri dettagli, vedere la documentazione relativa alla [creazione di un endpoint privato per Azure ricerca cognitiva](service-create-private-endpoint.md)

Nell'esempio seguente viene illustrato come creare un servizio di ricerca con un endpoint privato.

Per prima cosa, distribuire un servizio di ricerca con `PublicNetworkAccess` impostato su `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Successivamente, creare una rete virtuale, una connessione di rete privata e l'endpoint privato.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Infine, creare una zona DNS privata. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

Per altri dettagli sulla creazione di endpoint privati in PowerShell, vedere questa [Guida introduttiva al collegamento privato](https://docs.microsoft.com/azure/private-link/create-private-endpoint-powershell)

### <a name="manage-private-endpoint-connections"></a>Gestire le connessioni endpoint privato

Oltre a creare una connessione all'endpoint privato, è anche possibile `Get` , `Set` e `Remove` la connessione.

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) viene utilizzato per recuperare una connessione all'endpoint privato e per visualizzarne lo stato.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) viene usato per aggiornare la connessione. Nell'esempio seguente viene impostata una connessione all'endpoint privato rifiutata:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) viene usato per eliminare la connessione all'endpoint privato.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Riscrivere una chiave amministratore

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) viene usato per eseguire il rollover delle [chiavi API](search-security-api-keys.md)di amministrazione. Vengono create due chiavi amministratore con ogni servizio per l'accesso autenticato. Le chiavi sono obbligatorie per ogni richiesta. Entrambe le chiavi di amministrazione sono equivalenti dal punto di vista funzionale, concedendo l'accesso in scrittura completo a un servizio di ricerca con la possibilità di recuperare informazioni oppure di creare ed eliminare qualsiasi oggetto. Esistono due chiavi in modo che sia possibile utilizzarne una mentre si sostituisce l'altra. 

È possibile rigenerare solo una alla volta, specificata come `primary` `secondary` chiave o. Per il servizio senza interruzioni, ricordarsi di aggiornare tutto il codice client per l'uso di una chiave secondaria durante il rollover della chiave primaria. Evitare di modificare le chiavi mentre le operazioni sono in corso.

Come si può immaginare, se si rigenerano le chiavi senza aggiornare il codice client, le richieste che usano la chiave precedente avranno esito negativo. La rigenerazione di tutte le nuove chiavi non blocca definitivamente il servizio ed è comunque possibile accedere al servizio tramite il portale. Dopo la rigenerazione delle chiavi primarie e secondarie, è possibile aggiornare il codice client per l'uso delle nuove chiavi e le operazioni riprenderanno di conseguenza.

I valori per le chiavi API vengono generati dal servizio. Non è possibile fornire una chiave personalizzata per l'uso di ricerca cognitiva di Azure. Analogamente, non esiste alcun nome definito dall'utente per le chiavi API di amministrazione. I riferimenti alla chiave sono stringhe fisse, `primary` ovvero o `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

I risultati dovrebbero essere simili all'output seguente. Entrambe le chiavi vengono restituite anche se si modifica solo una alla volta.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Creare o eliminare chiavi di query

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) viene usato per creare [chiavi API](search-security-api-keys.md) di query per l'accesso in sola lettura dalle app client a un indice ricerca cognitiva di Azure. Le chiavi di query vengono usate per eseguire l'autenticazione a un indice specifico allo scopo di recuperare i risultati della ricerca. Le chiavi di query non concedono l'accesso in sola lettura ad altri elementi nel servizio, ad esempio un indice, un'origine dati o un indicizzatore.

Non è possibile fornire una chiave per l'uso di ricerca cognitiva di Azure. Le chiavi API vengono generate dal servizio.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Ridimensionare le repliche e le partizioni

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) viene usato per [aumentare o diminuire le repliche e le partizioni](search-capacity-planning.md) per modificare le risorse fatturabili all'interno del servizio. Aumentare le repliche o le partizioni aggiunge la fattura, con costi fissi e variabili. Se è necessaria una capacità di elaborazione aggiuntiva temporanea, è possibile aumentare le repliche e le partizioni per gestire il carico di lavoro. Nell'area monitoraggio della pagina del portale di panoramica sono presenti riquadri sulla latenza delle query, le query al secondo e la limitazione, che indica se la capacità corrente è adeguata.

L'aggiunta o la rimozione della Resourcing può richiedere del tempo. Le modifiche alla capacità si verificano in background, consentendo la continuazione dei carichi di lavoro esistenti. La capacità aggiuntiva viene utilizzata per le richieste in ingresso non appena è pronta, senza che sia necessaria alcuna configurazione aggiuntiva. 

La rimozione della capacità può risultare problematica. Per evitare le richieste Eliminate, è consigliabile arrestare tutti i processi di indicizzazione e di indicizzatore prima di ridurre la capacità. Se ciò non è fattibile, è possibile valutare la possibilità di ridurre in modo incrementale la capacità, una replica e una partizione alla volta, fino a raggiungere i nuovi livelli di destinazione.

Una volta inviato il comando, non è possibile terminarlo a metà. Prima di rivedere i conteggi, sarà necessario attendere il completamento del comando.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

I risultati dovrebbero essere simili all'output seguente.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Creare una risorsa di collegamento privato condiviso

Gli endpoint privati delle risorse protette create tramite le API di Azure ricerca cognitiva vengono definiti risorse di *collegamento privato condiviso*. Questo perché si sta "condividendo" l'accesso a una risorsa, ad esempio un account di archiviazione, che è stato integrato con il [servizio di collegamento privato di Azure](https://azure.microsoft.com/services/private-link/).

Se si usa un indicizzatore per indicizzare i dati in Azure ricerca cognitiva e l'origine dati si trova in una rete privata, è possibile creare una [connessione all'endpoint privato](../private-link/private-endpoint-overview.md) in uscita per raggiungere i dati.

Un elenco completo delle risorse di Azure per cui è possibile creare endpoint privati in uscita da Azure ricerca cognitiva è disponibile [qui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) insieme ai valori **ID gruppo** correlati.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) viene usato per creare la risorsa di collegamento privato condivisa. Tenere presente che alcune configurazioni potrebbero essere necessarie per l'origine dati prima di eseguire questo comando.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) consente di recuperare le risorse di collegamento privato condivise e di visualizzarne lo stato.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Prima di poter usare la connessione, è necessario approvare la connessione con il comando seguente.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) viene usato per eliminare la risorsa di collegamento privato condivisa.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Per informazioni dettagliate sulla configurazione delle risorse di collegamento privato condivise, vedere la documentazione relativa alla [creazione di connessioni dell'indicizzatore tramite un endpoint privato](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Passaggi successivi

Compilare un [Indice](search-what-is-an-index.md), [eseguire una query su un indice](search-query-overview.md) usando il portale, le API REST o .NET SDK.

* [Creare un indice di Ricerca cognitiva di Azure nel portale di Azure](search-get-started-portal.md)
* [Configurare un indicizzatore per caricare i dati da altri servizi](search-indexer-overview.md)
* [Eseguire query su un indice di ricerca cognitiva di Azure usando Esplora ricerche nella portale di Azure](search-explorer.md)
* [Come usare ricerca cognitiva di Azure in .NET](search-howto-dotnet-sdk.md)