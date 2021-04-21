---
title: Ottenere lo stato delle traduzioni
titleSuffix: Azure Cognitive Services
description: Il metodo get translations status restituisce un elenco di richieste batch inviate e lo stato di ogni richiesta.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: a67544dc4f654a692338c76099daa19934a9ea45
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836209"
---
# <a name="get-translations-status"></a>Ottenere lo stato delle traduzioni

Il metodo Get translations status restituisce un elenco di richieste batch inviate e lo stato di ogni richiesta. Questo elenco contiene solo le richieste batch inviate dall'utente (in base alla sottoscrizione). Lo stato di ogni richiesta è ordinato in base all'ID.

Se il numero di richieste supera il limite di paging, viene usato il paging sul lato server. Le risposte impaginate indicano un risultato parziale e includono un token di continuazione nella risposta. L'assenza di un token di continuazione significa che non sono disponibili altre pagine.

$top e $skip parametri di query possono essere usati per specificare un numero di risultati da restituire e un offset per la raccolta.

Il server rispetta i valori specificati dal client. Tuttavia, i client devono essere preparati a gestire le risposte che contengono dimensioni di pagina diverse o che contengono un token di continuazione.

Quando sono $top e $skip, il server deve prima applicare $skip e quindi $top alla raccolta. 

> [!NOTE]
> Se il server non è in grado di rispettare $top e/o $skip, il server deve restituire un errore al client che ne informa anziché ignorare semplicemente le opzioni di query. In questo modo si riduce il rischio che il client presunzioni sui dati restituiti.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Di traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella  pagina chiavi ed endpoint della risorsa portale di Azure, né l'endpoint del traduttore globale, , per effettuare richieste HTTP a `api.cognitive.microsofttranslator.com` Document Translation.

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

|Query parameter (Parametro di query)|Obbligatoria|Descrizione|
|--- |--- |--- |
|$skip|Falso|Ignorare le $skip nella raccolta. Quando vengono $top e $skip, $skip viene applicato per primo.|
|$top|Falso|Accetta le $top nella raccolta. Quando vengono $top e $skip, $skip viene applicato per primo.|

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|--- |--- |
|200|OK. Richiesta completata e restituisce lo stato di tutte le operazioni. HeadersRetry-After: integerETag: string|
|400|Richiesta non valida. Richiesta non valida. Controllare i parametri di input.|
|401|Non autorizzato. Controllare le credenziali.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="get-translations-status-response"></a>Ottenere la risposta dello stato delle traduzioni

### <a name="successful-get-translations-status-response"></a>Risposta di stato get translations completata

Le informazioni seguenti vengono restituite in una risposta corretta.

|Nome|Type|Descrizione|
|--- |--- |--- |
|id|string|ID dell'operazione.|
|createdDateTimeUtc|string|Data e ora di creazione dell'operazione.|
|lastActionDateTimeUtc|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|status|Stringa|Elenco di possibili stati per il processo o il documento: <ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|riepilogo|StatusSummary[]|Riepilogo contenente i dettagli elencati di seguito.|
|summary.total|numero intero|Conteggio dei documenti totali.|
|summary.failed|numero intero|Numero di documenti non riusciti.|
|summary.success|numero intero|Numero di documenti tradotti correttamente.|
|summary.inProgress|numero intero|Numero di documenti in corso.|
|summary.notYetStarted|numero intero|Numero di documenti non ancora avviati.|
|summary.cancelled|numero intero|Numero di documenti annullati.|
|summary.totalCharacterCharged|numero intero|Numero totale di caratteri addebitati.|

### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene il messaggio di errore di alto livello.|
|target|string|Ottiene l'origine dell'errore. Ad esempio, sarebbe "documenti" o "ID documento" nel caso di un documento non valido.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(key value pair), inner error (questo può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene un messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Risposta riuscita di esempio

Di seguito è riportato un esempio di risposta corretta.

```JSON
{
  "value": [
    {
      "id": "727bf148-f327-47a0-9481-abae6362f11e",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Succeeded",
      "summary": {
        "total": 10,
        "failed": 1,
        "success": 9,
        "inProgress": 0,
        "notYetStarted": 0,
        "cancelled": 0,
        "totalCharacterCharged": 0
      }
    }
  ]
}
```

### <a name="example-error-response"></a>Risposta di errore di esempio

Di seguito è riportato un esempio di risposta di errore. Lo schema per altri codici di errore è lo stesso.

Codice di stato: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Seguire la guida introduttiva per altre informazioni sull'uso della traduzione dei documenti e della libreria client.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione di documenti](../get-started-with-document-translation.md)
