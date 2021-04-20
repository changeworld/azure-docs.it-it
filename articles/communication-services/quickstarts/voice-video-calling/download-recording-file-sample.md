---
title: Registrare e scaricare chiamate con Griglia di eventi - Guida Servizi di comunicazione di Azure rapida
titleSuffix: An Azure Communication Services quickstart
description: In questa guida introduttiva si apprenderà come registrare e scaricare chiamate usando Griglia di eventi.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730569"
---
# <a name="record-and-download-calls-with-event-grid"></a>Registrare e scaricare chiamate con Griglia di eventi

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Per iniziare a usare Servizi di comunicazione di Azure, registrare le chiamate di Servizi di comunicazione usando Griglia di eventi di Azure.

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Pacchetto [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) NuGet.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Creare un webhook e sottoscrivere gli eventi di registrazione
Si useranno *webhook*  ed eventi per facilitare la registrazione delle chiamate e i download di file multimediali. 

Prima di tutto, verrà creato un webhook. La risorsa di Servizi di comunicazione userà Griglia di eventi per inviare una notifica a questo webhook quando viene attivato l'evento e quindi di nuovo quando i supporti registrati sono pronti `recording` per essere scaricati.

È possibile scrivere un webhook personalizzato per ricevere queste notifiche degli eventi. È importante che questo webhook risponda ai messaggi in ingresso con il codice di convalida per sottoscrivere correttamente il webhook al servizio eventi.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


Il codice precedente dipende dal `Microsoft.Azure.EventGrid` pacchetto NuGet. Per altre informazioni sulla convalida degli endpoint di Griglia di eventi, vedere la documentazione [sulla convalida degli endpoint](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation)

Si sottoscriverà quindi questo webhook `recording` all'evento:

1. Selezionare il `Events` pannello dalla risorsa Servizi di comunicazione di Azure rete.
2. Selezionare `Event Subscription` come illustrato di seguito.
![Screenshot che mostra l'interfaccia utente di Griglia di eventi](./media/call-recording/image1-event-grid.png)
3. Configurare la sottoscrizione di eventi e `Call Recording File Status Update` selezionare come `Event Type` . Selezionare `Webhook` come `Endpoint type` .
![Creare una sottoscrizione di eventi](./media/call-recording/image2-create-subscription.png)
4. Immettere l'URL del webhook in `Subscriber Endpoint` .
![Sottoscrivere un evento](./media/call-recording/image3-subscribe-to-event.png)

Il webhook riceverà ora una notifica ogni volta che la risorsa servizi di comunicazione viene usata per registrare una chiamata.

## <a name="notification-schema"></a>Schema di notifica
Quando la registrazione è disponibile per il download, la risorsa di Servizi di comunicazione genera una notifica con lo schema di eventi seguente. Gli ID documento per la registrazione possono essere recuperati dai `documentId` campi di ogni `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Scaricare i file multimediali registrati

Quando si ottiene l'ID documento per il file che si vuole scaricare, si chiamerà le API Servizi di comunicazione di Azure seguenti per scaricare i metadati e i supporti registrati usando l'autenticazione HMAC.

Le dimensioni massime del file di registrazione sono di 1,5 GB. Quando le dimensioni del file vengono superate, il registratore suddivide automaticamente i supporti registrati in più file.

Il client deve essere in grado di scaricare tutti i file multimediali con una singola richiesta. Se si verifica un problema, il client può riprovare con un'intestazione di intervallo per evitare il ricaricamento dei segmenti già scaricati.

Per scaricare i supporti registrati: 
- Metodo: `GET` 
- URL: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Per scaricare i metadati dei supporti registrati: 
- Metodo: `GET` 
- URL: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Authentication
Per scaricare i metadati e i supporti registrati, usare l'autenticazione HMAC per autenticare la richiesta Servizi di comunicazione di Azure API.

Creare un `HttpClient` e aggiungere le intestazioni necessarie usando `HmacAuthenticationUtils` l'oggetto fornito di seguito:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Le utilità seguenti possono essere usate per gestire il flusso di lavoro HMAC.

**Creare l'hash del contenuto**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**Aggiungere intestazioni HMAC**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Pulire le risorse
Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere gli articoli seguenti:

- Vedere [l'esempio di chiamata Web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Informazioni sulle funzionalità [dell'SDK per chiamate](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Leggere altre informazioni sul [funzionamento delle chiamate](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
