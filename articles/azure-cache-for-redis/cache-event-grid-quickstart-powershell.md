---
title: "Guida introduttiva: Indirizzare cache di Azure per Redis eventi all'endpoint Web con PowerShell"
description: Usare Griglia di eventi di Azure per sottoscrivere cache di Azure per Redis eventi, inviare gli eventi a un webhook e gestire gli eventi in un'applicazione Web.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833887"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Guida introduttiva: Indirizzare cache di Azure per Redis eventi all'endpoint Web con PowerShell

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questa guida introduttiva si userà Azure PowerShell per sottoscrivere cache di Azure per Redis eventi, attivare un evento e visualizzare i risultati. 

In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Tuttavia, per semplificare questa guida introduttiva, si invieranno eventi a un'app Web che raccoglierà e visualizza i messaggi. Dopo aver completato i passaggi descritti in questa guida introduttiva, si può vedere che i dati dell'evento sono stati inviati all'app Web.

## <a name="setup"></a>Configurazione

Per questa guida introduttiva è necessario eseguire la versione più recente di Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

Questo esempio usa **westus2** e archivia la selezione in una variabile che verrà usata in tutta la procedura.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti di Griglia di eventi vengono distribuiti come singole risorse di Azure e devono essere forniti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

L'esempio seguente crea un gruppo di risorse denominato **gridResourceGroup** nella località **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Creare un'istanza di Azure Cache per Redis 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Per altre informazioni sulla creazione di un'istanza della cache in PowerShell, vedere le informazioni Azure PowerShell [riferimento.](/powershell/module/az.rediscache/new-azrediscache) 

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

Sostituire `<your-site-name>` con un nome univoco per l'app Web. Il nome dell'app Web deve essere univoco perché fa parte della voce DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Il sito dovrebbe essere visibile senza messaggi attualmente visualizzati.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Sito vuoto del Visualizzatore Griglia di eventi.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Sottoscrivere l'cache di Azure per Redis eventi

In questo passaggio si sottoscriverà un argomento per indicare a Griglia di eventi gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'istanza della cache creata e passa l'URL dall'app Web come endpoint per la notifica degli eventi. L'endpoint per l'app Web deve includere il suffisso `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Griglia di eventi di Azure visualizzatore.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Attivare un evento da cache di Azure per Redis

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Per altre informazioni sull'importazione in PowerShell, vedere le informazioni di [Azure PowerShell riferimento.](/powershell/module/az.rediscache/import-azrediscache) 

È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Visualizzare l'app Web per vedere l'evento appena inviato.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di continuare a lavorare con questa cache di Azure per Redis e la sottoscrizione di eventi, non pulire le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare il comando seguente per eliminare le risorse create in questa guida introduttiva.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, vedere altre informazioni sugli eventi cache di Azure per Redis eventi e su cosa può essere utile per Griglia di eventi:

- [Reazione a cache di Azure per Redis eventi](cache-event-grid.md)
- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
