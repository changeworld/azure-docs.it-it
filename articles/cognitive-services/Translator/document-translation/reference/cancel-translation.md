---
title: Annullare il metodo di conversione
titleSuffix: Azure Cognitive Services
description: Il metodo cancel translation annulla un'operazione attualmente in elaborazione o in coda.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865108"
---
# <a name="cancel-translation"></a>Annullare la traduzione

Annullare un'operazione attualmente in elaborazione o in coda. Un'operazione non verrà annullata se è già stata completata, non riuscita o annullata. Verrà restituita una richiesta non valida. Tutti i documenti che hanno completato la traduzione non verranno annullati e verranno addebitati i costi. Tutti i documenti in sospeso verranno annullati, se possibile.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `DELETE` a:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella pagina delle chiavi e _dell'endpoint_ della risorsa portale di Azure, né l'endpoint del traduttore globale, per effettuare richieste `api.cognitive.microsofttranslator.com` HTTP alla traduzione dei documenti.

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

|Query parameter (Parametro di query)|Obbligatoria|Descrizione|
|-----|-----|-----|
|id|Vero|OPERATION-ID.|

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|-----|-----|
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

| Codice di stato| Descrizione|
|-----|-----|
|200|OK. La richiesta di annullamento è stata inviata|
|401|Non autorizzato. Controllare le credenziali.|
|404|Non trovato. La risorsa non è stata trovata. 
|500|Errore interno del server.
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="cancel-translation-response"></a>Annullare la risposta di traduzione

### <a name="successful-response"></a>Risposta di esito positivo

Le informazioni seguenti vengono restituite in una risposta corretta.

|Nome|Type|Descrizione|
|--- |--- |--- |
|id|string|ID dell'operazione.|
|createdDateTimeUtc|string|Data e ora di creazione dell'operazione.|
|lastActionDateTimeUtc|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|status|Stringa|Elenco di possibili stati per il processo o il documento: <ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|riepilogo|StatusSummary|Riepilogo contenente i dettagli elencati di seguito.|
|summary.total|numero intero|Conteggio dei documenti totali.|
|summary.failed|numero intero|Numero di documenti non riusciti.|
|summary.success|numero intero|Numero di documenti tradotti correttamente.|
|summary.inProgress|numero intero|Numero di documenti in corso.|
|summary.notYetStarted|numero intero|Numero di documenti non ancora avviati.|
|summary.cancelled|numero intero|Numero di annullati.|
|summary.totalCharacterCharged|numero intero|Totale dei caratteri addebitati dall'API.|

### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|target|string|Ottiene l'origine dell'errore. Ad esempio, sarebbe "documenti" o "ID documento" per un documento non valido.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(key value pair), inner error (può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|Interno. Eroor.message|string|Ottiene un messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Risposta riuscita di esempio

L'oggetto JSON seguente è un esempio di risposta riuscita.

Codice di stato: 200

```JSON
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
```

### <a name="example-error-response"></a>Risposta di errore di esempio

L'oggetto JSON seguente è un esempio di risposta di errore. Lo schema per altri codici di errore è lo stesso.

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
