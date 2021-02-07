---
title: "Guida introduttiva: eseguire il routing di cache di Azure per eventi Redis all'endpoint Web con l'interfaccia della riga"
description: Usare griglia di eventi di Azure per sottoscrivere la cache di Azure per gli eventi Redis, attivare un evento e visualizzare i risultati.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806427"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Guida introduttiva: eseguire il routing di cache di Azure per eventi Redis all'endpoint Web con l'interfaccia della riga

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questa Guida introduttiva si userà l'interfaccia della riga di comando di Azure per sottoscrivere gli eventi di cache di Azure per Redis, attivare un evento e visualizzare i risultati.

In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Tuttavia, per semplificare questa Guida introduttiva, sarà possibile inviare eventi a un'app Web che raccoglierà e visualizzerà i messaggi. Quando si completano i passaggi descritti in questa Guida introduttiva, si noterà che i dati dell'evento sono stati inviati all'app Web.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa Guida introduttiva è necessario eseguire la versione più recente dell'interfaccia della riga di comando di Azure (2.0.70 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti di griglia di eventi vengono distribuiti come singole risorse di Azure e devono essere sottoposti a provisioning in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). 

L'esempio seguente crea un gruppo di risorse denominato `<resource_group_name>` nella località *westcentralus*.  Sostituire `<resource_group_name>` con un nome univoco per il gruppo di risorse.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Creare un'istanza della cache

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere l'argomento, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

Sostituire `<your-site-name>` con un nome univoco per l'app Web. Il nome dell'app Web deve essere univoco perché fa parte della voce DNS.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Il sito dovrebbe essere visibile senza messaggi attualmente visualizzati.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Sottoscrivere la cache di Azure per l'istanza di redis

In questo passaggio si sottoscrive un argomento per indicare a griglia di eventi gli eventi di cui si vuole tenere traccia e la posizione in cui inviare tali eventi. L'esempio seguente sottoscrive la cache di Azure per l'istanza di redis creata e passa l'URL dall'app Web come endpoint per la notifica degli eventi. Sostituire `<event_subscription_name>` con un nome per la sottoscrizione di eventi. Per `<resource_group_name>` e `<cache_name>` usare i valori creati in precedenza.

L'endpoint per l'app Web deve includere il suffisso `/api/updates/`.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visualizzatore griglia di eventi di Azure.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Attivare un evento dalla cache di Azure per Redis

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint. Esportiamo i dati archiviati nella cache di Azure per l'istanza di Redis. Utilizzare di nuovo i valori per `{cache_name}` e `{resource_group_name}` creati in precedenza.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

È stato attivato l'evento e Griglia di eventi ha inviato il messaggio all'endpoint configurato al momento della sottoscrizione. Visualizzare l'app Web per vedere l'evento appena inviato.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di continuare a usare questa cache di Azure per l'istanza di redis e la sottoscrizione di eventi, non pulire le risorse create in questa Guida introduttiva. Se non si prevede di continuare, usare il comando seguente per eliminare le risorse create in questa Guida introduttiva.

Sostituire `<resource_group_name>` con il gruppo di risorse creato in precedenza.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, altre informazioni sugli eventi di cache di Azure per Redis e sulla griglia di eventi possono essere utili:

- [Reazione alla cache di Azure per gli eventi Redis](cache-event-grid.md)
- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
