---
title: Usare Griglia di eventi di Azure con gli eventi nello schema CloudEvents
description: Questo articolo descrive come usare lo schema CloudEvents per gli eventi in Griglia di eventi di Azure. Il servizio supporta gli eventi nell'implementazione JSON di CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737903"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Usare lo schema CloudEvents v1.0 con Griglia di eventi
Oltre allo [schema di eventi predefinito](event-schema.md), Griglia di eventi di Azure supporta in modo nativo gli eventi nell'[implementazione JSON di CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e nel [binding del protocollo HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) è una [specifica aperta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) per la descrizione dei dati degli eventi.

CloudEvents semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo di eventi basati sul cloud. Questo schema consente di applicare strumenti uniformi, modalità standard di routing e gestione degli eventi e metodi universali per la deserializzazione dello schema dell'evento esterno. Con uno schema comune, è possibile integrare più facilmente il lavoro tra le piattaforme.

CloudEvents viene compilato da diversi [collaboratori](https://github.com/cloudevents/spec/blob/master/community/contributors.md), tra cui Microsoft, tramite [Cloud Native Computing Foundation](https://www.cncf.io/). È attualmente disponibile come versione 1.0.

Questo articolo descrive come usare lo schema CloudEvents con Griglia di eventi.

## <a name="cloudevent-schema"></a>Schema CloudEvent

Di seguito è riportato un esempio di un evento di archiviazione BLOB di Azure nel formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Per una descrizione dettagliata dei campi disponibili, i relativi tipi e definizioni, vedere [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

I valori delle intestazioni per gli eventi recapitati nello schema CloudEvents e nello schema Griglia di eventi sono gli stessi, ad eccezione di `content-type`. Per lo schema CloudEvents, il valore dell'intestazione è `"content-type":"application/cloudevents+json; charset=utf-8"` . Per lo schema di griglia di eventi, il valore dell'intestazione è `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Configurare Griglia di eventi per CloudEvents

È possibile utilizzare griglia di eventi sia per l'input che per l'output degli eventi nello schema CloudEvents. Nella tabella seguente vengono descritte le possibili trasformazioni:

 Risorsa griglia di eventi | Schema di input       | Schema di recapito
|---------------------|-------------------|---------------------
| Argomenti di sistema       | Schema di Griglia di eventi | Schema di griglia di eventi o schema CloudEvents
| Argomenti/domini utente | Schema di Griglia di eventi | Schema di griglia di eventi o schema CloudEvents
| Argomenti/domini utente | Schema di CloudEvents | Schema di CloudEvents
| Argomenti/domini utente | Schema personalizzato     | Schema personalizzato, schema di griglia di eventi o schema CloudEvents
| PartnerTopics       | Schema di CloudEvents | Schema di CloudEvents

Per tutti gli schemi di eventi, griglia di eventi richiede la convalida quando si esegue la pubblicazione in un argomento di griglia di eventi e quando si crea una sottoscrizione di eventi.

Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

### <a name="input-schema"></a>Schema di input

Si imposta lo schema di input per un argomento personalizzato quando si crea l'argomento personalizzato.

Per l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Per PowerShell, usare:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Schema di output.

Si imposta lo schema di output quando si crea la sottoscrizione dell'evento.

Per l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Per PowerShell, usare:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Attualmente non è possibile usare un trigger di Griglia di eventi per un'app di Funzioni di Azure quando l'evento viene recapitato nello schema CloudEvents. Usare un trigger HTTP. Per esempi di implementazione di un trigger HTTP che riceve gli eventi nello schema CloudEvents, vedere [Usare CloudEvents con Funzioni di Azure](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Convalida degli endpoint con CloudEvents v1.0

Se si ha già familiarità con griglia di eventi, si potrebbe essere a conoscenza dell'handshake di convalida dell'endpoint per impedire abusi. CloudEvents v 1.0 implementa la propria [semantica di protezione da abusi](webhook-event-delivery.md) usando il metodo delle opzioni http. Per ulteriori informazioni, vedere la pagina [relativa agli hook Web HTTP 1,1 per il recapito degli eventi, versione 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando si usa lo schema CloudEvents per l'output, griglia di eventi usa la protezione dagli abusi di CloudEvents v 1.0 al posto del meccanismo di convalida degli eventi di griglia di eventi.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Usare CloudEvents con Funzioni di Azure

Il [binding di griglia di eventi di funzioni di Azure](../azure-functions/functions-bindings-event-grid.md) non supporta in modo nativo CloudEvents, quindi le funzioni attivate da http vengono usate per leggere i messaggi CloudEvents. Quando si usa un trigger HTTP per leggere CloudEvents, è necessario scrivere il codice per il trigger della griglia di eventi automaticamente:

* Invia una risposta di convalida a una [richiesta di convalida della sottoscrizione](../event-grid/webhook-event-delivery.md)
* Richiama la funzione una volta per ogni elemento della matrice di eventi contenuta nel corpo della richiesta.

Per informazioni sull'URL da usare per richiamare la funzione localmente o quando viene eseguita in Azure, vedere la documentazione di [riferimento sull'associazione di trigger http](../azure-functions/functions-bindings-http-webhook.md).

Il seguente codice C# di esempio relativo a un trigger HTTP simula il comportamento del trigger Griglia di eventi. Usare questo esempio per gli eventi nello schema CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Il seguente codice JavaScript di esempio relativo a un trigger HTTP simula il comportamento del trigger Griglia di eventi. Usare questo esempio per gli eventi nello schema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Ti invitiamo a testare, commentare e [contribuire a CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
