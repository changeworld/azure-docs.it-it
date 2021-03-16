---
title: Risoluzione dei problemi in Servizi di comunicazione di Azure
description: Come raccogliere le informazioni necessarie per risolvere i problemi relativi alla soluzione Servizi di comunicazione.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: daa89380894a57e58191edd95303a2160846da04
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492694"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Risoluzione dei problemi in Servizi di comunicazione di Azure

Questo documento consente di risolvere i problemi che potrebbero verificarsi all'interno della soluzione Servizi di comunicazione. Per la risoluzione dei problemi del servizio SMS, è possibile [abilitare la creazione di report di recapito con Griglia di eventi](../quickstarts/telephony-sms/handle-sms-events.md) per acquisire i dettagli di recapito SMS.

## <a name="getting-help"></a>Risorse della Guida

Invitiamo gli sviluppatori a inviare domande, suggerire funzionalità e segnalare problemi come problemi. Per facilitare questa operazione, sono disponibili una [pagina dedicata per il supporto e la guida](../support.md) che elenca le opzioni per il supporto.

Per poter risolvere determinati tipi di problemi, è possibile che vengano richieste le informazioni seguenti:

* **ID MS-CV**: questo ID viene usato per la risoluzione dei problemi relativi a chiamate e messaggi.
* **ID chiamata**: questo ID viene usato per identificare le chiamate a Servizi di comunicazione.
* **ID del messaggio SMS**: questo ID viene usato per identificare i messaggi SMS.
* **Registri chiamate**: questi registri contengono informazioni dettagliate che possono essere usate per risolvere i problemi di chiamata e di rete.


## <a name="access-your-ms-cv-id"></a>Accedere all'ID MS-CV

È possibile accedere all'ID MS-CV configurando la diagnostica nell'istanza dell'oggetto `clientOptions` durante l'inizializzazione delle librerie client. La diagnostica può essere configurata per tutte le librerie client di Azure, incluse le chiamate chat, Identity e VoIP.

### <a name="client-options-example"></a>Esempio di opzioni client

I frammenti di codice seguenti illustrano la configurazione della diagnostica. Quando si usano le librerie client con la diagnostica abilitata, i dettagli di diagnostica vengono emessi nel listener di eventi configurato:

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Accedere all'ID chiamata

Quando si compila una richiesta di supporto relativa a problemi delle chiamate nel portale di Azure, potrebbe essere chiesto di fornire l'ID della chiamata in questione. Questo ID è accessibile tramite la libreria client per le chiamate:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>Accedere all'ID del messaggio SMS

Per i problemi relativi agli SMS, è possibile ottenere l'ID del messaggio dall'oggetto risposta.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Abilitare e accedere ai registri chiamate

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice seguente può essere usato per configurare `AzureLogger` in modo da restituire i registri alla console usando la libreria client JavaScript:

```javascript
import { AzureLogger } from '@azure/logger';

AzureLogger.verbose = (...args) => { console.info(...args); }
AzureLogger.info = (...args) => { console.info(...args); }
AzureLogger.warning = (...args) => { console.info(...args); }
AzureLogger.error = (...args) => { console.info(...args); }

callClient = new CallClient({logger: AzureLogger});
```

# <a name="ios"></a>[iOS](#tab/ios)

Quando si sviluppa per iOS, i registri vengono archiviati nei file `.blog`. Si noti che non è possibile visualizzare i registri direttamente perché sono crittografati.

È possibile accedervi aprendo Xcode. Passare a Windows > Dispositivi e simulatori > Dispositivi. Selezionare il dispositivo. In App installate selezionare l'applicazione e fare clic su "Scarica contenitore".

Verrà restituito un file `xcappdata`. Fare clic con il pulsante destro del mouse su questo file e selezionare "Mostra contenuto del pacchetto". Verranno quindi visualizzati i file di `.blog` che è possibile allegare alla richiesta al supporto tecnico di Azure.

# <a name="android"></a>[Android](#tab/android)

Quando si sviluppa per Android, i registri vengono archiviati nei file `.blog`. Si noti che non è possibile visualizzare i registri direttamente perché sono crittografati.

In Android Studio passare a Device File Explorer selezionando View > Tool Windows > Device File Explorer dal simulatore e dal dispositivo. Il file `.blog` si troverà all'interno della directory dell'applicazione, che dovrebbe essere simile a `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. Il file può essere allegato alla richiesta di supporto.


---

## <a name="calling-client-library-error-codes"></a>Codici errore della libreria client per le chiamate

La libreria client per le chiamate di Servizi di comunicazione di Azure usa i codici errore seguenti per facilitare la risoluzione dei problemi relativi alle chiamate. Questi codici di errore vengono esposti tramite la proprietà `call.callEndReason` al termine di una chiamata.

| Codice di errore | Descrizione | Azione da eseguire |
| -------- | ---------------| ---------------|
| 403 | Accesso negato/Errore di autenticazione. | Verificare che il token di Servizi di comunicazione sia valido e non scaduto. |
| 404 | Chiamata non trovata. | Verificare che il numero che si sta chiamando (o la chiamata a cui ci si sta aggiungendo) esista. |
| 408 | Timeout del controller di chiamata. | Si è verificato il timeout del controller di chiamata durante l'attesa dei messaggi di protocollo dagli endpoint utente. Verificare che i client siano connessi e disponibili. |
| 410 | Errore dello stack multimediale locale o dell'infrastruttura multimediale. | Assicurarsi di usare la libreria client più recente in un ambiente supportato. |
| 430 | Non è possibile recapitare messaggi all'applicazione client. | Verificare che l'applicazione client sia in esecuzione e disponibile. |
| 480 | Endpoint client remoto non registrato. | Assicurarsi che l'endpoint remoto sia disponibile. |
| 481 | Non è stato possibile gestire la chiamata in ingresso. | Inviare una richiesta di supporto tramite il portale di Azure. |
| 487 | Chiamata annullata, rifiutata a livello locale, terminata a causa di un problema di corrispondenza dell'endpoint oppure generazione dell'offerta multimediale non riuscita. | Comportamento previsto. |
| 490, 491, 496, 487, 498 | Problemi di rete dell'endpoint locale. | Controllare la rete. |
| 500, 503, 504 | Errore dell'infrastruttura di Servizi di comunicazione. | Inviare una richiesta di supporto tramite il portale di Azure. |
| 603 | Chiamata rifiutata a livello globale dal partecipante di Servizi di comunicazione remota | Comportamento previsto. |

## <a name="related-information"></a>Informazioni correlate
- [Log e diagnostica](logging-and-diagnostics.md)
- [Metrics](metrics.md) (Metriche)
