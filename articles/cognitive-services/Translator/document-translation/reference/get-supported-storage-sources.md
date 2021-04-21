---
title: Metodo Get supported storage sources (Ottenere le origini di archiviazione supportate)
titleSuffix: Azure Cognitive Services
description: Il metodo get supported storage sources (Ottieni origini di archiviazione supportate) restituisce un elenco di origini di archiviazione supportate.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 97212a0da47a25330def26a5bff2b39ff840a23d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836191"
---
# <a name="get-supported-storage-sources"></a>Ottenere le origini di archiviazione supportate

Il metodo Get supported storage sources (Ottieni origini di archiviazione supportate) restituisce un elenco di origini/opzioni di archiviazione supportate dal servizio traduzione documenti.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella pagina delle chiavi e _dell'endpoint_ della risorsa portale di Azure, né l'endpoint del traduttore globale, per effettuare richieste `api.cognitive.microsofttranslator.com` HTTP alla traduzione dei documenti.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|--- |--- |
|200|OK. Richiesta riuscita e restituisce l'elenco di origini di archiviazione.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Ottenere la risposta delle origini di archiviazione supportate

### <a name="successful-get-supported-storage-sources-response"></a>Risposta di ottenere correttamente le origini di archiviazione supportate
Tipo di base per la restituzione dell'elenco nell'API Ottenere le origini di archiviazione supportate.

|Nome|Type|Descrizione|
|--- |--- |--- |
|Valore|string []|Elenco di oggetti .|


### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(coppia chiave-valore), inner error (può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta con esito positivo

Di seguito è riportato un esempio di risposta con esito positivo.

```JSON
{
  "value": [
    "AzureBlob"
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