---
title: Operazioni get per la conversione di documenti
titleSuffix: Azure Cognitive Services
description: Il metodo Get Operations restituisce un elenco di richieste batch inviate e lo stato di ogni richiesta.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613024"
---
# <a name="document-translation-get-operations"></a>Conversione di documenti: operazioni Get

Il metodo Get Operations restituisce un elenco di richieste batch inviate e lo stato di ogni richiesta. Questo elenco contiene solo le richieste batch inviate dall'utente (in base alla sottoscrizione). Lo stato di ogni richiesta è ordinato in base all'ID.

Se il numero di richieste supera il limite di paging, viene utilizzato il paging lato server. Le risposte impaginate indicano un risultato parziale e includono un token di continuazione nella risposta. L'assenza di un token di continuazione significa che non è disponibile alcuna pagina aggiuntiva.

$top e $skip parametri di query possono essere utilizzati per specificare un numero di risultati da restituire e un offset per la raccolta.

Il server rispetta i valori specificati dal client. Tuttavia, i client devono essere preparati a gestire le risposte che contengono una dimensione di pagina diversa o contengono un token di continuazione.

Quando vengono inclusi sia $top che $skip, il server deve prima applicare $skip e quindi $top sulla raccolta. 

> [!NOTE]
> Se il server non è in grado di rispettare $top e/o $skip, il server deve restituire un errore al client in modo da informarlo anziché ignorare semplicemente le opzioni di query. In questo modo si riduce il rischio che il client faccia supposizioni sui dati restituiti.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio di traduzione documenti richiedono un endpoint del dominio personalizzato**.
> * Non è possibile usare l'endpoint trovato nelle chiavi di risorsa portale di Azure e nella pagina dell' _endpoint_ né nell'endpoint di conversione globale, `api.cognitive.microsofttranslator.com` per eseguire richieste HTTP alla conversione dei documenti.

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

|Query parameter (Parametro di query)|Obbligatoria|Descrizione|
|--- |--- |--- |
|$skip|Falso|Ignorare le voci $skip nella raccolta. Quando vengono specificati sia $top che $skip, $skip viene applicato per primo.|
|$top|Falso|Prendere le voci $top nella raccolta. Quando vengono specificati sia $top che $skip, $skip viene applicato per primo.|

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|--- |--- |
|200|OK. La richiesta è stata completata e restituisce lo stato di tutte le operazioni. HeadersRetry-after: integerETag: stringa|
|400|Richiesta non valida. Richiesta non valida. Controllare i parametri di input.|
|401|Non autorizzato. Verificare le credenziali.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="get-operations-response"></a>Ottenere la risposta delle operazioni

### <a name="successful-get-operations-response"></a>Risposta operazioni get riuscita

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|id|string|ID dell'operazione.|
|createdDateTimeUtc|string|Data/ora di creazione dell'operazione.|
|lastActionDateTimeUtc|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|status|string|Elenco di stati possibili per il processo o il documento: <ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|riepilogo|StatusSummary[]|Riepilogo contenente i dettagli elencati di seguito.|
|Riepilogo. totale|numero intero|Conteggio dei documenti totali.|
|Riepilogo. operazione non riuscita|numero intero|Conteggio dei documenti non riuscito.|
|Riepilogo. operazione riuscita|numero intero|Conteggio dei documenti tradotti correttamente.|
|Riepilogo. in corso|numero intero|Conteggio dei documenti in corso.|
|Riepilogo. notYetStarted|numero intero|Conteggio dei documenti non ancora avviati.|
|Riepilogo. annullato|numero intero|Conteggio dei documenti annullati.|
|Riepilogo. totalCharacterCharged|numero intero|Numero totale di caratteri addebitati.|

###<a name="error-response"></a>Risposta di errore

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene il messaggio di errore di alto livello.|
|target|string|Ottiene l'origine dell'errore. Ad esempio, sarebbe "Documents" o "Document ID" nel caso di un documento non valido.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, che è conforme alle linee guida dell'API servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, Message e optional Properties Target, Details (key value pair), Inner Error (può essere annidato).|
|innerError. code|string|Ottiene la stringa di errore del codice.|
|innerError. Message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta riuscita

Di seguito è riportato un esempio di una risposta con esito positivo.

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

Di seguito è riportato un esempio di una risposta di errore. Lo schema per altri codici di errore è lo stesso.

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

Per altre informazioni sull'uso della traduzione dei documenti e della libreria client, seguire la Guida introduttiva.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](../get-started-with-document-translation.md)
