---
title: Ottenere il metodo per ottenere i formati di documento supportati
titleSuffix: Azure Cognitive Services
description: Il metodo get supported document formats restituisce un elenco di formati di documento supportati.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e47f3363a9e09a3e371c751e0bdd1143cfc72314
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864902"
---
# <a name="get-supported-document-formats"></a>Ottenere i formati di documento supportati

Il metodo Get supported document formats restituisce un elenco di formati di documento supportati dal servizio di traduzione dei documenti. L'elenco include l'estensione di file comune e il tipo di contenuto se si usa l'API di caricamento.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Di traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella  pagina chiavi ed endpoint della risorsa portale di Azure, né l'endpoint del traduttore globale, , per effettuare richieste HTTP a `api.cognitive.microsofttranslator.com` Document Translation.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|-----|-----|
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|-----|-----|
|200|OK. Restituisce l'elenco dei formati di file di documento supportati.|
|500|Errore interno del server.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="file-format-response"></a>Risposta del formato di file

### <a name="successful-fileformatlistresult-response"></a>Risposta fileFormatListResult riuscita

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

|Nome|Type|Descrizione|
|--- |--- |--- |
|Valore|FileFormat []|FileFormat[] contiene i dettagli elencati di seguito.|
|value.format|string[]|Tipi di contenuto supportati per questo formato.|
|value.fileExtensions|string[]|Estensione di file supportata per questo formato.|
|value.contentTypes|string[]|Nome del formato.|
|value.versions|String[]|Versione supportata.|

### <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
 |codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(key value pair), inner error (questo può essere annidato).|
|innerError.code|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene un messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Risposta riuscita di esempio
Di seguito è riportato un esempio di risposta corretta.

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