---
title: Impostazioni upstream nel servizio Azure SignalR
description: Ottenere un'introduzione alle impostazioni upstream e ai protocolli dei messaggi upstream.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724057"
---
# <a name="upstream-settings"></a>Impostazioni upstream

Upstream è una funzionalità di anteprima che consente al servizio Azure SignalR di inviare messaggi ed eventi di connessione a un set di endpoint in modalità senza server. È possibile usare upstream per richiamare un metodo Hub da client in modalità senza server e consentire agli endpoint di ricevere notifiche quando le connessioni client vengono connesse o disconnesse.

> [!NOTE]
> Solo la modalità senza server può configurare le impostazioni upstream.

## <a name="details-of-upstream-settings"></a>Dettagli delle impostazioni upstream

Le impostazioni upstream sono costituite da un elenco di elementi sensibili agli ordini. Ogni elemento è costituito da:

* Modello URL, che specifica il punto in cui i messaggi vengono inviati a.
* Set di regole.
* Configurazioni di autenticazione. 

Quando si verifica l'evento specificato, le regole di un elemento vengono controllate una alla volta in ordine. I messaggi verranno inviati all'URL upstream del primo elemento corrispondente.

### <a name="url-template-settings"></a>Impostazioni modello URL

È possibile parametrizzare l'URL per supportare i vari modelli. Sono disponibili tre parametri predefiniti:

|Parametro predefinito|Descrizione|
|---------|---------|
|Hub| Un hub è un concetto di servizio Azure SignalR. Un hub è un'unità di isolamento. L'ambito degli utenti e del recapito dei messaggi è vincolato a un hub.|
|Categoria| Una categoria può essere uno dei valori seguenti: <ul><li>**connessioni**: eventi di durata della connessione. Viene generato quando una connessione client è connessa o disconnessa. Include eventi connessi e disconnessi.</li><li>**messaggi**: generato quando i client richiamano un metodo dell'hub. Include tutti gli altri eventi, ad eccezione di quelli nella categoria **connessioni** .</li></ul>|
|evento| Per la categoria **messages** , un evento è la destinazione in un [messaggio di chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) che i client inviano. Per la categoria **Connections** vengono utilizzati solo *Connected* e *disconnected* .|

Questi parametri predefiniti possono essere usati nel modello di URL. Quando si valuta l'URL upstream, i parametri verranno sostituiti con un valore specificato. Ad esempio: 
```
http://host.com/{hub}/api/{category}/{event}
```
Quando una connessione client nell'hub "chat" è connessa, viene inviato un messaggio all'URL seguente:
```
http://host.com/chat/api/connections/connected
```
Quando un client nell'hub "chat" richiama il metodo dell'hub `broadcast` , viene inviato un messaggio all'URL seguente:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Riferimento Key Vault segreto nelle impostazioni del modello URL

L'URL dell'upstream non è la crittografia dei inattivi. Se si dispone di informazioni riservate, è consigliabile usare Key Vault per salvarle, in cui il controllo degli accessi ha una migliore assicurazione. In pratica, è possibile abilitare l'identità gestita del servizio Azure SignalR e quindi concedere l'autorizzazione di lettura per un'istanza di Key Vault e usare Key Vault riferimento anziché il testo non crittografato nel modello URL upstream.

1. Aggiungere un'identità assegnata dal sistema o un'identità assegnata dall'utente. Vedere [come aggiungere identità gestite nel portale di Azure](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Concedere l'autorizzazione di lettura segreta per l'identità gestita nei criteri di accesso del Key Vault. Vedere [assegnare un criterio di accesso key Vault usando il portale di Azure](../key-vault/general/assign-access-policy-portal.md)

3. Sostituire il testo sensibile con la sintassi `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` nel modello upstream URL.

> [!NOTE]
> Il contenuto del segreto viene riletto solo quando si modificano le impostazioni upstream o si modifica l'identità gestita. Assicurarsi di aver concesso l'autorizzazione di lettura segreta all'identità gestita prima di usare il riferimento Key Vault Secret.

### <a name="rule-settings"></a>Impostazioni regola

È possibile impostare separatamente le regole per le regole dell' *Hub*, *le regole di categoria* e *le regole degli eventi* . La regola di corrispondenza supporta tre formati. Prendere le regole degli eventi come esempio:
- Usare un asterisco (*) per trovare la corrispondenza con qualsiasi evento.
- Usare una virgola (,) per unire più eventi. Ad esempio, `connected, disconnected` corrisponde agli eventi connessi e disconnessi.
- Usare il nome completo dell'evento per trovare la corrispondenza con l'evento. Ad esempio, `connected` corrisponde all'evento connesso.

> [!NOTE]
> Se si usa funzioni di Azure e il [trigger SignalR](../azure-functions/functions-bindings-signalr-service-trigger.md), il trigger SignalR esporrà un singolo endpoint nel formato seguente: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> È possibile configurare solo **le impostazioni del modello URL** in questo URL e Mantieni **le impostazioni predefinite delle regole** . Per informazioni dettagliate su come trovare e, vedere [integrazione del servizio SignalR](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) `<Function_App_URL>` `<API_KEY>` .

### <a name="authentication-settings"></a>Authentication settings

È possibile configurare l'autenticazione per ogni elemento dell'impostazione upstream separatamente. Quando si configura l'autenticazione di, un token viene impostato nell' `Authentication` intestazione del messaggio upstream. Attualmente, il servizio Azure SignalR supporta i tipi di autenticazione seguenti:
- `None`
- `ManagedIdentity`

Quando si seleziona `ManagedIdentity` , è necessario abilitare in anticipo un'identità gestita nel servizio Azure SignalR e, facoltativamente, specificare una risorsa. Per informazioni dettagliate, vedere [identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Creare le impostazioni upstream tramite il portale di Azure

1. Passare al servizio Azure SignalR.
2. Selezionare **Impostazioni** e passare alla **modalità servizio** a senza **server**. Verranno visualizzate le impostazioni upstream:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Impostazioni upstream":::

3. Aggiungere URL nel **modello URL upstream**. Impostazioni come le **regole dell'hub** mostreranno quindi il valore predefinito.
4. Per impostare le impostazioni per le **regole dell'hub**, le **regole degli eventi**, le regole di **categoria** e **l'autenticazione upstream**, selezionare il valore delle **regole dell'hub**. Viene visualizzata una pagina che consente di modificare le impostazioni:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Dettagli impostazione upstream":::

5. Per impostare **l'autenticazione upstream**, assicurarsi che sia stata abilitata per prima un'identità gestita. Selezionare quindi **Usa identità gestita**. Secondo le esigenze, è possibile scegliere qualsiasi opzione in **ID risorsa di autenticazione**. Per informazioni dettagliate, vedere [identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Crea impostazioni upstream tramite Gestione risorse modello

Per creare le impostazioni upstream usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md), impostare la `upstream` proprietà nella `properties` Proprietà. Il frammento di codice seguente mostra come impostare la `upstream` proprietà per la creazione e l'aggiornamento delle impostazioni upstream.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Protocolli senza server

Il servizio Azure SignalR invia messaggi agli endpoint che seguono i protocolli seguenti. È possibile usare l' [associazione del trigger del servizio SignalR](../azure-functions/functions-bindings-signalr-service-trigger.md) con app per le funzioni, che gestisce automaticamente questi protocolli.

### <a name="method"></a>Metodo

POST

### <a name="request-header"></a>Intestazione della richiesta

|Nome |Descrizione|
|---------|---------|
|X-ASRS-Connection-ID |ID connessione per la connessione client.|
|X-ASRS-Hub |Hub a cui appartiene la connessione client.|
|X-ASRS-Category |Categoria a cui appartiene il messaggio.|
|X-ASRS-evento |Evento a cui appartiene il messaggio.|
|X-ASRS-Signature |Codice HMAC (hash-based Message Authentication Code) usato per la convalida. Per informazioni dettagliate, vedere [firma](#signature) .|
|X-ASRS-User-Claims |Gruppo di attestazioni della connessione client.|
|X-ASRS-User-ID |Identità utente del client che invia il messaggio.|
|X-ASRS-client-query |Query della richiesta quando i client si connettono al servizio.|
|Authentication |Token facoltativo quando si usa `ManagedIdentity` . |

### <a name="request-body"></a>Testo della richiesta

#### <a name="connected"></a>Connesso

Content-Type: application/json

#### <a name="disconnected"></a>Disconnesso

Tipo di contenuto: `application/json`

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|Errore |string |Messaggio di errore di una connessione chiusa. Vuota quando le connessioni si chiudono senza errori.|

#### <a name="invocation-message"></a>Messaggio di chiamata

Content-Type: `application/json` o `application/x-msgpack`

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|InvocationId |string | Stringa facoltativa che rappresenta un messaggio di chiamata. Trovare i dettagli nelle [chiamate](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Destinazione |string | Uguale all'evento e uguale a quello della destinazione in un messaggio di [chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argomenti |Matrice di oggetti |Matrice contenente gli argomenti da applicare al metodo a cui si fa riferimento in `Target` . |

### <a name="signature"></a>Firma

Il servizio calcolerà il codice SHA256 per il `X-ASRS-Connection-Id` valore usando la chiave di accesso primaria e la chiave di accesso secondaria come `HMAC` chiave. Il servizio lo imposterà nell' `X-ASRS-Signature` intestazione quando si effettuano richieste HTTP a upstream:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Passaggi successivi

- [Identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md)
- [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](signalr-concept-serverless-development-config.md)
- [Gestire i messaggi dal servizio SignalR (associazione di trigger)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [Esempio di associazione del trigger del servizio SignalR](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)