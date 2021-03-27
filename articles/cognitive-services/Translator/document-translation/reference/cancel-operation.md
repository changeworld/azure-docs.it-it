---
title: Metodo di annullamento dell'operazione di traduzione documenti
titleSuffix: Azure Cognitive Services
description: Il metodo Cancel Operations Annulla un'operazione attualmente in fase di elaborazione o in coda.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613068"
---
# <a name="document-translation-cancel-operations"></a>Traduzione di documenti: operazioni di annullamento

Annulla un'operazione attualmente in esecuzione o in coda. Un'operazione non verrà annullata se è già stata completata o non è riuscita o è stata annullata. Verrà restituita una richiesta non valida. Tutti i documenti che hanno completato la conversione non verranno annullati e verranno addebitati. Se possibile, tutti i documenti in sospeso verranno annullati.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `DELETE` a:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio di traduzione documenti richiedono un endpoint del dominio personalizzato**.
> * Non è possibile usare l'endpoint trovato nelle chiavi di risorsa portale di Azure e nella pagina dell' _endpoint_ né nell'endpoint di conversione globale, `api.cognitive.microsofttranslator.com` per eseguire richieste HTTP alla conversione dei documenti.

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

|Query parameter (Parametro di query)|Obbligatoria|Descrizione|
|-----|-----|-----|
|id|Vero|ID operazione.|

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
|401|Non autorizzato. Verificare le credenziali.|
|404|Non trovato. La risorsa non è stata trovata. 
|500|Errore interno del server.
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="cancel-operations-response"></a>Risposta operazioni di annullamento

### <a name="successful-response"></a>Risposta di esito positivo

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|id|string|ID dell'operazione.|
|createdDateTimeUtc|string|Data/ora di creazione dell'operazione.|
|lastActionDateTimeUtc|string|Data e ora in cui lo stato dell'operazione è stato aggiornato.|
|status|string|Elenco di stati possibili per il processo o il documento: <ul><li>Cancellati</li><li>Cancelling</li><li>Non riuscito</li><li>NotStarted</li><li>In esecuzione</li><li>Completato</li><li>ValidationFailed</li></ul>|
|riepilogo|StatusSummary|Riepilogo contenente i dettagli elencati di seguito.|
|Riepilogo. totale|numero intero|Conteggio dei documenti totali.|
|Riepilogo. operazione non riuscita|numero intero|Conteggio dei documenti non riuscito.|
|Riepilogo. operazione riuscita|numero intero|Conteggio dei documenti tradotti correttamente.|
|Riepilogo. in corso|numero intero|Conteggio dei documenti in corso.|
|Riepilogo. notYetStarted|numero intero|Conteggio dei documenti non ancora avviati.|
|Riepilogo. annullato|numero intero|Numero di annullati.|
|Riepilogo. totalCharacterCharged|numero intero|Caratteri totali addebitati dall'API.|

### <a name="error-response"></a>Risposta di errore

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene il messaggio di errore di alto livello.|
|target|string|Ottiene l'origine dell'errore. Ad esempio, sarebbe "Documents" o "Document ID" per un documento non valido.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, che è conforme alle linee guida dell'API servizi cognitivi. Contiene le proprietà obbligatorie proprietà ErrorCode, messaggio e proprietà facoltative destinazione, dettagli (coppia chiave-valore), errore interno (può essere annidato).|
|innerError. code|string|Ottiene la stringa di errore del codice.|
|interna. Errore. Message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta riuscita

L'oggetto JSON seguente è un esempio di risposta corretta.

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

Per altre informazioni sull'uso della traduzione dei documenti e della libreria client, seguire la Guida introduttiva.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](../get-started-with-document-translation.md)
