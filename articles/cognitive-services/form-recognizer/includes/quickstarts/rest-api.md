---
title: 'Guida introduttiva: API REST del riconoscitore del modulo'
description: Usare l'API REST di riconoscimento form per creare un'app per l'elaborazione di form che estrae le coppie chiave/valore e i dati della tabella dai documenti personalizzati.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: e0769f77bffaab0e04b492b19a5ea131cec64f06
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104674"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Questa guida usa cURL per eseguire chiamate alle API REST. È anche disponibile un [codice di esempio in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) che illustra come chiamare le API REST con Python.

## <a name="prerequisites"></a>Prerequisiti

* [cURL](https://curl.haxx.se/windows/) installato.
* [PowerShell versione 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)o un'applicazione della riga di comando simile.
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di un set di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451) (scaricare ed estrarre *sample_data.zip*).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
  * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Un URL di un'immagine di una ricevuta. Per questo argomento di avvio rapido è possibile usare un'[immagine di esempio](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
* Un URL dell'immagine di un biglietto da visita. Per questo argomento di avvio rapido è possibile usare un'[immagine di esempio](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).
* Un URL dell'immagine di una fattura. Per questa guida di avvio rapido, è possibile usare un [documento di esempio](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).

## <a name="analyze-layout"></a>Analizzare il layout

È possibile utilizzare il riconoscimento moduli per analizzare ed estrarre tabelle, contrassegni di selezione, testo e struttura nei documenti, senza dover eseguire il training di un modello. Per ulteriori informazioni sull'estrazione del layout, vedere la [Guida concettuale sul layout](../../concept-layout.md). Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `\"{your-document-url}` con uno degli URL di esempio.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Si riceverà una risposta `202 (Success)` che include un'intestazione **Operation-Location**. Il valore di questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati. Nell'esempio seguente, la stringa dopo `analyzeResults/` è l'ID operazione.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Ottenere i risultati del layout

Dopo aver chiamato l'API **[Analyze Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** , chiamare l'API **[Get Analyze Layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** per ottenere lo stato dell'operazione e i dati estratti. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `{resultId}` con l'ID operazione del passaggio precedente.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Esaminare i risultati

Si riceverà una risposta `200 (success)` con il contenuto JSON.

Vedere l'immagine di fattura seguente e il corrispondente output JSON.
* Il nodo `"readResults"` contiene ogni riga di testo con il rispettivo posizionamento del rettangolo di selezione nella pagina. 
* Il nodo `"selectionMarks"` (in v2.1.preview) mostra ogni indicatore di selezione (casella di controllo, pulsante di opzione) e se il relativo stato è "selezionato" o "non selezionato". 
* La `"pageResults"` sezione include le tabelle estratte. Per ogni tabella vengono estratti il testo, l'indice di riga e di colonna, lo spanning di righe e colonne, il rettangolo di delimitazione e altro.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Documento di rendiconto su un progetto Contoso con una tabella.":::

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

Questo output è stato abbreviato per semplicità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

Questo output è stato abbreviato per semplicità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

---

## <a name="analyze-invoices"></a>Analizzare fatture

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

Per iniziare ad analizzare una fattura, usare il comando cURL seguente. Per ulteriori informazioni sull'analisi delle fatture, vedere la [Guida concettuale](../../concept-invoices.md)relativa alla fatturazione. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{your invoice URL}` con l'indirizzo URL del documento della fattura.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Si riceverà una risposta `202 (Success)` che include un'intestazione **Operation-Location**. Il valore di questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Ottenere i risultati delle fatture

Dopo aver chiamato l'API **[analizza fattura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** , è possibile chiamare l'API **[Get Analyze result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** per ottenere lo stato dell'operazione e i dati estratti. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `{resultId}` con l'ID operazione del passaggio precedente.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Esaminare i risultati

Si riceverà una risposta `200 (Success)` con un output JSON. 
* Il `"readResults"` campo contiene ogni riga di testo estratta dalla fattura.
* `"pageResults"`Include le tabelle e i contrassegni di selezione estratti dalla fattura.
* Il `"documentResults"` campo contiene informazioni chiave/valore per le parti più rilevanti della fattura.

Vedere la fattura seguente e il corrispondente output JSON. 

* [Esempio di fattura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Questo contenuto JSON è stato abbreviato per migliorare la leggibilità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Questa funzionalità non è disponibile nella versione dell'API selezionata.

---

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Per eseguire il training di un modello personalizzato, è necessario un set di dati di training in un BLOB del servizio di archiviazione di Azure. Sono necessari almeno cinque form compilati (documenti PDF e/o immagini) dello stesso tipo/struttura. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di dati di training.

Il training senza dati con etichetta è l'operazione predefinita ed è più semplice. In alternativa, è possibile etichettare manualmente alcuni o tutti i dati di training in anticipo. Si tratta di una procedura più complessa, ma il risultato è un training del modello più efficace.

> [!NOTE]
> È anche possibile eseguire il training dei modelli con un'interfaccia utente grafica, ad esempio con lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Eseguire il training di un modello senza etichette

Per eseguire il training di un modello di Riconoscimento modulo con i documenti del contenitore BLOB di Azure, chiamare l'API **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `{SAS URL}` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recupero dell'URL di firma di accesso condiviso":::

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Si riceverà una risposta `201 (Success)` con l'intestazione **Location**. Il valore di questa intestazione corrisponde all'ID del nuovo modello da sottoporre a training.

### <a name="train-a-model-with-labels"></a>Eseguire il training di un modello con etichette

Per eseguire il training con etichette, è necessario avere file speciali di informazioni sulle etichette (`\<filename\>.pdf.labels.json`) nel contenitore di archiviazione BLOB insieme ai documenti di training. Lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md) fornisce un'interfaccia utente che consente di creare questi file di etichette. Una volta disponibili, è possibile chiamare l'API del **[modello di training personalizzato](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** , con il `"useLabelFile"` parametro impostato su `true` nel corpo JSON.

Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `{SAS URL}` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recupero dell'URL di firma di accesso condiviso":::

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Si riceverà una risposta `201 (Success)` con l'intestazione **Location**. Il valore di questa intestazione corrisponde all'ID del nuovo modello da sottoporre a training.

### <a name="get-training-results"></a>Ottenere i risultati del training

Dopo aver avviato il training, usare una nuova operazione, **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** , per verificare lo stato dell'operazione. Passare l'ID modello in questa chiamata API per controllare lo stato del training:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo,
1. Sostituire `{subscription key}` con la chiave di sottoscrizione
1. Sostituire `{model ID}` con l'ID modello ricevuto nel passaggio precedente

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. Osservare il campo `"status"`. Al termine del training, avrà il valore `"ready"`. Se il training del modello non è stato completato, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Il campo `"modelId"` contiene l'ID del modello da sottoporre a training. Servirà per il passaggio successivo.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Quindi, usare il modello appena sottoposto a training per analizzare un documento da cui estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `{model ID}` con l'ID modello ricevuto nella sezione precedente.
1. Sostituire `{SAS URL}` con un URL di firma di accesso condiviso per il file in archiviazione di Azure. Seguire la procedura decritta nella sezione Training, ma invece di ottenere un URL di firma di accesso condiviso per l'intero contenitore BLOB ottenerne uno per il file specifico da analizzare.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Si riceverà una risposta `202 (Success)` che include l'intestazione **Operation-Location**. Il valore di questa intestazione include un ID risultati da usare per tenere traccia dei risultati dell'operazione di analisi. Salvare questo ID risultati per il passaggio successivo.

### <a name="get-the-analyze-results"></a>Ottenere i risultati dell'analisi

Chiamare l'API Get **[Analyze result form](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** per eseguire una query sui risultati dell'operazione di analisi.

1. Sostituire `{Endpoint}` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `{result ID}` con l'ID ricevuto nella sezione precedente.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. L'output è stato abbreviato per semplicità. Osservare il campo `"status"` verso la fine. Al termine dell'operazione di analisi, avrà il valore `"succeeded"`. Se l'operazione di analisi non è stata completata, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Nei modelli personalizzati sottoposti a training senza etichette, le associazioni di coppie chiave/valore e le tabelle si trovano nel `"pageResults"` nodo dell'output JSON. Nei modelli personalizzati sottoposti a training con etichette, le associazioni di coppie chiave/valore si trovano nel `"documentResults"` nodo. Se è stata specificata anche l'estrazione di testo normale tramite il parametro URL *includeTextDetails*, il nodo `"readResults"` mostrerà il contenuto e le posizioni di tutto il testo nel documento.

Questo output JSON di esempio è stato abbreviato per semplicità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```  

---

### <a name="improve-results"></a>Migliorare i risultati

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Analizzare ricevute

Questa sezione illustra come analizzare ed estrarre i campi comuni dalle ricevute degli Stati Uniti, usando un modello di ricezione con training preliminare. Per ulteriori informazioni sull'analisi della ricezione, vedere la [Guida concettuale relativa alle ricevute](../../concept-receipts.md). Per iniziare ad analizzare una ricevuta, chiamare l'API **[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** usando il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{your receipt URL}` con l'indirizzo URL dell'immagine di una ricevuta.
1. Sostituire `{subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Si riceverà una risposta `202 (Success)` che include un'intestazione **Operation-Location**. Il valore di questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati. Nell'esempio seguente, la stringa dopo `operations/` è l'ID operazione.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Ottenere i risultati della ricevuta

Dopo aver chiamato l'API **Analyze Receipt**, chiamare l'API **[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** per ottenere lo stato dell'operazione e i dati estratti. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `{operationId}` con l'ID operazione del passaggio precedente.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Esaminare i risultati

Si riceverà una risposta `200 (Success)` con un output JSON. Il primo campo, `"status"`, indica lo stato dell'operazione. Se l'operazione non è stata completata, il valore di `"status"` sarà `"running"` o `"notStarted"` e si dovrà chiamare nuovamente l'API, manualmente o tramite uno script. Si consiglia di attendere almeno un secondo tra le chiamate.

Il nodo `"readResults"` contiene tutto il testo riconosciuto, se si imposta il parametro facoltativo *includeTextDetails* su `true`. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il nodo `"documentResults"` contiene i valori specifici della ricevuta individuati dal modello. Qui si troveranno utili coppie chiave-valore, come l'imposta, il totale, l'indirizzo del fornitore e così via.

Osservare l'immagine di ricevuta seguente e il corrispondente output JSON.

![Ricevuta di un negozio Contoso](../../media/contoso-allinone.jpg)

Questo output è stato abbreviato per migliorare la leggibilità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Analizzare biglietti da visita

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)  

In questa sezione viene illustrato come analizzare ed estrarre campi comuni da schede business inglesi, usando un modello con training preliminare. Per ulteriori informazioni sull'analisi dei biglietti da visita, vedere la [Guida concettuale](../../concept-business-cards.md)relativa ai biglietti da visita. Per iniziare ad analizzare un biglietto da visita, chiamare l'API **[Analyze Business Card](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** usando il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{your business card URL}` con l'indirizzo URL dell'immagine di una ricevuta.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Si riceverà una risposta `202 (Success)` che include un'intestazione **Operation-Location**. Il valore di questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Ottenere i risultati dei biglietti da visita

Dopo aver chiamato l'API di **analisi dei biglietti da visita**, chiamare l'API di **[recupero dei risultati dell'analisi dei biglietti da visita](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** per ottenere lo stato dell'operazione e i dati estratti. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `{resultId}` con l'ID operazione del passaggio precedente.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Esaminare i risultati

Si riceverà una risposta `200 (Success)` con un output JSON. 

Il nodo `"readResults"` contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il nodo `"documentResults"` contiene i valori specifici del biglietto da visita individuati dal modello. Qui sono disponibili le informazioni di contatto utili, come il nome della società, il nome e cognome, il numero di telefono e così via.

![Biglietto da visita della società Contoso](../../media/business-card-english.jpg)

Questo output JSON di esempio è stato abbreviato per migliorare la leggibilità. Vedere l' [output di esempio completo su GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Lo script visualizzerà le risposte sulla console fino al completamento dell'operazione di **analisi biglietto da visita**.

### <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Questa funzionalità non è disponibile nella versione dell'API selezionata.

---

## <a name="manage-custom-models"></a>Gestire i modelli personalizzati

### <a name="get-a-list-of-custom-models"></a>Ottenere un elenco di modelli personalizzati

Usare l'API **[List Custom Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** nel comando seguente per restituire un elenco di tutti i modelli personalizzati che appartengono alla sottoscrizione.

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Si riceverà una risposta `200` di operazione riuscita con dati JSON simili ai seguenti. L'elemento `"modelList"` contiene tutti i modelli creati e le relative informazioni.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Ottenere un modello specifico

Per recuperare informazioni dettagliate su un modello personalizzato specifico, usare l'API di **[recupero del modello personalizzato](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** nel comando seguente.

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `{modelId}` con l'ID del modello personalizzato da cercare.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Si riceverà una risposta `200` di operazione riuscita con dati JSON simili ai seguenti.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminare un modello dall'account della risorsa

È inoltre possibile eliminare un modello dall'account facendo riferimento al relativo ID. Questo comando chiama l'API del **[modello personalizzato Delete](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** per eliminare il modello usato nella sezione precedente.

1. Sostituire `{Endpoint}` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `{subscription key}` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `{modelId}` con l'ID del modello personalizzato da cercare.

### <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Si riceverà una `204` risposta di esito positivo, che indica che il modello è contrassegnato per l'eliminazione. Gli elementi del modello verranno rimossi entro 48 ore.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata usata l'API REST Riconoscimento modulo per eseguire il training di modelli e analizzare moduli in vari modi. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Informazioni su Riconoscimento modulo](../../overview.md)