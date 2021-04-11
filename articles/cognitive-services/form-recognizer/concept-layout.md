---
title: Layout-riconoscimento form
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi del layout con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467018"
---
# <a name="form-recognizer-layout-service"></a>Servizio di layout riconoscimento moduli

Il riconoscitore di form di Azure può estrarre testo, tabelle, contrassegni di selezione e informazioni sulla struttura dai documenti usando il servizio di layout. L'API di layout consente ai clienti di prendere documenti in un'ampia gamma di formati e restituire rappresentazioni dei dati strutturati dei documenti. Combina le potenti funzionalità di [riconoscimento ottico dei caratteri (OCR)](../computer-vision/concept-recognizing-text.md) con modelli di apprendimento avanzato per estrarre testo, tabelle, contrassegni di selezione e struttura del documento. 

## <a name="what-does-the-layout-service-do"></a>Cosa funziona il servizio di layout?

L'API di layout estrae testo, tabelle, contrassegni di selezione e informazioni di struttura da documenti con precisione eccezionale e restituisce una risposta JSON organizzata, strutturata. I documenti possono avere diversi formati e qualità, tra cui immagini acquisite tramite telefono, documenti analizzati e PDF digitali. L'API di layout estrae in modo accurato l'output strutturato da tutti i documenti.

![Esempio di layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio di layout riconoscimento moduli, passare allo strumento dell'interfaccia utente di esempio online:

> [!div class="nextstepaction"]
> [Strumento di test OCR del modulo (FOTT)](https://fott-preview.azurewebsites.net)

È necessaria una sottoscrizione di Azure ([crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e un endpoint e una chiave del sistema di [riconoscimento del modulo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) per provare l'API del layout di riconoscimento del modulo. 

![Schermata dell'interfaccia utente di esempio; il testo, le tabelle e i contrassegni di selezione di un documento vengono analizzati](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisiti di input 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Operazione di analisi del layout

Prima di tutto, chiamare l'operazione di [analisi del layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) . Analizza layout accetta un documento (file di immagine, TIFF o PDF) come input ed estrae il testo, le tabelle, i contrassegni di selezione e la struttura del documento. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Output dell'ordine di lettura naturale (solo alfabeto latino)

È possibile specificare l'ordine in cui vengono restituite le righe di testo con il `readingOrder` parametro di query. Usare `natural` per un output dell'ordine di lettura più semplice, come illustrato nell'esempio seguente. Questa funzionalità è supportata solo per le lingue latine.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Esempio di ordine di lettura layout" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Selezionare i numeri di pagina o gli intervalli per l'estrazione del testo

Per documenti di grandi dimensioni con più pagine, usare il `pages` parametro di query per indicare numeri di pagina o intervalli di pagine specifici per l'estrazione del testo. Nell'esempio seguente viene illustrato un documento con 10 pagine, con il testo estratto per entrambi i casi, tutte le pagine (1-10) e le pagine selezionate (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Output layout pagine selezionate":::

## <a name="the-get-analyze-layout-result-operation"></a>Operazione Get Analyze result layout

Il secondo passaggio consiste nel chiamare l'operazione [Get Analyze result layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Questa operazione accetta come input l'ID del risultato creato dall'operazione di analisi del layout. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. 

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | `notStarted`: L'operazione di analisi non è stata avviata.<br /><br />`running`: È in corso l'operazione di analisi.<br /><br />`failed`: L'operazione di analisi non è riuscita.<br /><br />`succeeded`: L'operazione di analisi ha avuto esito positivo.|

Chiamare questa operazione in modo iterativo fino a quando non viene restituito il `succeeded` valore. Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

Quando il campo **stato** presenta il `succeeded` valore, la risposta JSON includerà il layout Estratto, il testo, le tabelle e i contrassegni di selezione. I dati estratti includono righe e parole di testo estratti, caselle delimitatori, aspetto del testo con indicazione scritta a mano, tabelle e contrassegni di selezione con l'opzione selezionata o deselezionata. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificazione manoscritta per le righe di testo (solo alfabeto latino)

La risposta include la classificazione di ogni riga di testo con stile di grafia o meno, insieme a un punteggio di confidenza. Questa funzionalità è supportata solo per le lingue latine. Nell'esempio seguente viene illustrata la classificazione manoscritta per il testo nell'immagine.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="esempio di classificazione della grafia":::

### <a name="sample-json-output"></a>Output JSON di esempio

La risposta all'operazione *Get Analyze layout result* è una rappresentazione strutturata del documento con tutte le informazioni estratte. Vedere qui per un [file di documento di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e il relativo [output di layout di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)di output strutturato.

L'output JSON è costituito da due parti:

* `readResults` il nodo contiene tutti i contrassegni di testo e di selezione riconosciuti. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. 
* `pageResults` il nodo contiene le tabelle e le celle estratte con i rispettivi rettangoli di delimitazione, la confidenza e un riferimento alle righe e alle parole in "readResults".

## <a name="example-output"></a>Output di esempio

### <a name="text"></a>Testo

L'API layout estrae testo da documenti (PDF, TIFF) e immagini (JPG, PNG, BMP) con più angoli e colori di testo. Accetta foto di documenti, fax, stampati e/o manoscritti (solo in inglese) e modalità miste. Il testo viene estratto con le informazioni fornite su righe, parole, caselle di delimitazione, punteggi di confidenza e stile (scritti a mano). Tutte le informazioni sul testo sono incluse nella `readResults` sezione dell'output JSON. 

### <a name="tables"></a>Tabelle

L'API layout estrae le tabelle da documenti (PDF, TIFF) e immagini (JPG, PNG, BMP). È possibile analizzare, fotografare o digitalizzare documenti. Le tabelle possono essere complesse con celle o colonne unite, con o senza bordi e con angoli dispari. Le informazioni sulle tabelle estratte includono il numero di colonne e righe, l'intervallo di righe e l'intervallo di colonne. Ogni cella viene estratta con il relativo rettangolo di delimitazione e fa riferimento al testo estratto nella `readResults` sezione. Le informazioni sulla tabella si trovano nella `pageResults` sezione dell'output JSON. 

![Esempio di tabelle](./media/tables-example.jpg)

### <a name="selection-marks"></a>Contrassegni di selezione

L'API layout estrae anche i contrassegni di selezione dai documenti. I contrassegni di selezione estratti includono il rettangolo di selezione, la confidenza e lo stato (selezionato/deselezionato). Le informazioni sul contrassegno di selezione vengono estratte nella `readResults` sezione dell'output JSON. 

## <a name="next-steps"></a>Passaggi successivi

* Provare la propria estrazione del layout usando lo [strumento dell'interfaccia utente di esempio di form Recognizer](https://fott-preview.azurewebsites.net/)
* Completare una [Guida introduttiva](quickstarts/client-library.md) per il riconoscimento dei moduli per iniziare a estrarre i layout nel linguaggio di sviluppo preferito.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)
* [Documentazione di riferimento per l'API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
