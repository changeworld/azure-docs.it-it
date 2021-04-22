---
title: Ottenere il metodo di stato del documento
titleSuffix: Azure Cognitive Services
description: Il metodo get document status restituisce lo stato per un documento specifico.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 4c6e82af46a012ad53dfa1cc1db1252ef2c0443e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864938"
---
# <a name="get-document-status"></a>Ottenere lo stato del documento

Il metodo Get Document Status restituisce lo stato per un documento specifico. Il metodo restituisce lo stato della traduzione per un documento specifico in base all'ID richiesta e all'ID documento.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
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
|documentId|Vero|ID del documento.|
|id|Vero|ID del batch.|
## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|--- |--- |
|200|OK. La richiesta ha avuto esito positivo e viene accettata dal servizio. Vengono restituiti i dettagli dell'operazione. HeadersRetry-After: integerETag: string|
|401|Non autorizzato. Controllare le credenziali.|
|404|Non trovato. La risorsa non è stata trovata.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="get-document-status-response"></a>Ottenere la risposta dello stato del documento

### <a name="successful-get-document-status-response"></a>Risposta di operazione riuscita per ottenere lo stato del documento

|Nome|Type|Descrizione|
|--- |--- |--- |
|path|string|Percorso del documento o della cartella.|
|createdDateTimeUtc|string|Data e ora di creazione dell'operazione.|
|lastActionDateTimeUtc|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|status|Stringa|Elenco di possibili stati per il processo o il documento: <ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|in|string|Codice lingua di due lettere di Lingua. Vedere l'elenco delle lingue.|
|progress|d'acquisto|Stato della traduzione, se disponibile|
|id|string|ID documento.|
|characterCharged|numero intero|Caratteri addebitati dall'API.|

### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(key value pair), inner error (può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta con esito positivo
L'oggetto JSON seguente è un esempio di risposta con esito positivo.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Risposta di errore di esempio

L'oggetto JSON seguente è un esempio di risposta di errore. Lo schema per altri codici di errore è lo stesso.

Codice di stato: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Seguire la guida introduttiva per altre informazioni sull'uso della traduzione dei documenti e della libreria client.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione di documenti](../get-started-with-document-translation.md)
