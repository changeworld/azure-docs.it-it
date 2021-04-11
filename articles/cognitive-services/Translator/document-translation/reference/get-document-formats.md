---
title: Metodo per la conversione di documenti Get Document formats
titleSuffix: Azure Cognitive Services
description: Il metodo Get Document formats restituisce un elenco di formati di documenti supportati.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 3cd74f70416d2049a0fb969371507c292dd47fdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613063"
---
# <a name="document-translation-get-document-formats"></a>Traduzione di documenti: ottenere i formati di documento

Il metodo Get Document formats restituisce un elenco di formati di documenti supportati dal servizio di conversione dei documenti. L'elenco include l'estensione di file comune e il tipo di contenuto se si usa l'API di caricamento.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio di traduzione documenti richiedono un endpoint del dominio personalizzato**.
> * Non è possibile usare l'endpoint trovato nelle chiavi di risorsa portale di Azure e nella pagina dell' _endpoint_ né nell'endpoint di conversione globale, `api.cognitive.microsofttranslator.com` per eseguire richieste HTTP alla conversione dei documenti.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|-----|-----|
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|-----|-----|
|200|OK. Restituisce l'elenco dei formati di file di documenti supportati.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="file-format-response"></a>Risposta al formato del file

### <a name="successful-fileformatlistresult-response"></a>Risposta fileFormatListResult riuscita

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|Valore|FileFormat []|FileFormat [] contiene i dettagli elencati di seguito.|
|value. Format|string[]|Tipi di contenuto supportati per questo formato.|
|value. FileExtensions|string[]|Estensione di file supportata per questo formato.|
|value. contentTypes|string[]|Nome del formato.|
|value. Versions|String[]|Versione supportata.|

### <a name="error-response"></a>Risposta di errore

|Nome|Tipo|Descrizione|
|--- |--- |--- |
 |codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene il messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, che è conforme alle linee guida dell'API servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, Message e optional Properties Target, Details (key value pair), Inner Error (può essere annidato).|
|innerError. code|string|Ottiene la stringa di errore del codice.|
|innerError. Message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta riuscita
Di seguito è riportato un esempio di una risposta con esito positivo.

Codice di stato: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
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