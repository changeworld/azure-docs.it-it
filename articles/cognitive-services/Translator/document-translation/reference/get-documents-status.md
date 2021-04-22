---
title: Ottenere lo stato dei documenti
titleSuffix: Azure Cognitive Services
description: Il metodo get documents status restituisce lo stato di tutti i documenti in una richiesta di traduzione di documenti batch.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 8476c4891cef9d9055b16c7ac574e569ecf5b3f2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864865"
---
# <a name="get-documents-status"></a>Ottenere lo stato dei documenti

Il metodo Get documents status (Ottieni stato documenti) restituisce lo stato di tutti i documenti in una richiesta di traduzione di documenti batch.

I documenti inclusi nella risposta vengono ordinati in base all'ID documento in ordine decrescente. Se il numero di documenti nella risposta supera il limite di paging, viene usato il paging sul lato server. Le risposte impaginate indicano un risultato parziale e includono un token di continuazione nella risposta. L'assenza di un token di continuazione significa che non sono disponibili altre pagine.

$top e $skip parametri di query possono essere usati per specificare un numero di risultati da restituire e un offset per la raccolta. Il server rispetta i valori specificati dal client. Tuttavia, i client devono essere preparati a gestire le risposte che contengono dimensioni di pagina diverse o che contengono un token di continuazione.

Quando sono $top e $skip, il server deve prima applicare $skip e quindi $top alla raccolta.

> [!NOTE]
> Se il server non può rispettare $top e/o $skip, il server deve restituire un errore al client che ne informa anziché semplicemente ignorare le opzioni di query. In questo modo si riduce il rischio che il client assunsi presupposti sui dati restituiti.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella pagina delle chiavi e _dell'endpoint_ della risorsa portale di Azure, né l'endpoint del traduttore globale, per effettuare richieste HTTP alla `api.cognitive.microsofttranslator.com` traduzione dei documenti.

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

|Query parameter (Parametro di query)|Obbligatoria|Descrizione|
|--- |--- |--- |
|id|Vero|ID dell'operazione.|
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
|200|OK. Richiesta riuscita e restituisce lo stato dei documenti. HeadersRetry-After: integerETag: string|
|400|Richiesta non valida. Controllare i parametri di input.|
|401|Non autorizzato. Controllare le credenziali.|
|404|La risorsa non è stata trovata.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|


## <a name="get-documents-status-response"></a>Ottenere la risposta dello stato dei documenti

### <a name="successful-get-documents-status-response"></a>Risposta con esito positivo per ottenere lo stato dei documenti

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

|Nome|Type|Descrizione|
|--- |--- |--- |
|@nextLink|string|URL per la pagina successiva. Null se non sono disponibili altre pagine.|
|Valore|DocumentStatusDetail []|Lo stato dettagliato dei singoli documenti elencati di seguito.|
|value.path|string|Percorso del documento o della cartella.|
|value.createdDateTimeUtc|string|Data e ora di creazione dell'operazione.|
|value.lastActionDateTimeUt|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|value.status|status|Elenco di possibili stati per il processo o il documento.<ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|value.to|string|Alla lingua.|
|value.progress|string|Stato della traduzione, se disponibile.|
|value.id|string|ID documento.|
|value.characterCharged|numero intero|Caratteri addebitati dall'API.|

### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|target|string|Ottiene l'origine dell'errore. Ad esempio, sarebbe "documenti" o "ID documento" nel caso di un documento non valido.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(coppia chiave-valore), inner error (può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta con esito positivo

Di seguito è riportato un esempio di risposta con esito positivo.

```JSON
{
  "value": [
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
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
