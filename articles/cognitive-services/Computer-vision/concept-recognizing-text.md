---
title: Riconoscimento ottico di caratteri (OCR)-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al riconoscimento ottico dei caratteri (OCR) di immagini e documenti con testo stampato e scritto a mano usando il API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979536"
---
# <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)

API Visione artificiale di Azure include funzionalità di riconoscimento ottico dei caratteri (OCR) che estraggono testo stampato o scritto a mano dalle immagini. È possibile estrarre il testo dalle immagini, ad esempio le foto delle targhe o dei contenitori con i numeri di serie, nonché da documenti-fatture, fatture, rapporti finanziari, articoli e altro ancora.

## <a name="read-api"></a>API di lettura 

L' [API visione artificiale Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) è la tecnologia OCR più recente di Azure ([informazioni sulle novità](./whats-new.md)) che estrae il testo stampato (in diversi linguaggi), il testo scritto a mano (solo in inglese), le cifre e i simboli di valuta da immagini e documenti PDF a più pagine. È ottimizzato per estrarre testo da immagini con un elevato numero di testo e documenti PDF a più pagine con lingue miste. Supporta il rilevamento di testo stampato e scritto a mano nella stessa immagine o documento.

![Come OCR converte immagini e documenti in un output strutturato con testo Estratto](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Requisiti di input
La chiamata di **lettura** accetta immagini e documenti come input. Hanno i requisiti seguenti:

* Formati di file supportati: JPEG, PNG, BMP, PDF e TIFF
* Per i file PDF e TIFF, vengono elaborate fino a 2000 pagine (solo le prime due pagine per il livello gratuito).
* Le dimensioni del file devono essere inferiori a 50 MB (4 MB per il livello gratuito) e dimensioni di almeno 50 x 50 pixel e al massimo 10000 x 10000 pixel. 
* Le dimensioni PDF devono essere al massimo 17 x 17 pollici, corrispondenti a dimensioni della carta note o a3 e inferiori.

> [!NOTE]
> **Input lingua** 
>
> La [chiamata di lettura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ha un parametro di richiesta facoltativo per la lingua. Read supporta l'identificazione automatica della lingua e i documenti multilingue, in modo da fornire solo un codice di lingua se si vuole forzare l'elaborazione del documento come linguaggio specifico.

## <a name="ocr-demo-examples"></a>Demo OCR (esempi)

![Demo OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Passaggio 1: operazione di lettura

La [chiamata Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) dell'API Read accetta un'immagine o un documento PDF come input ed estrae il testo in modo asincrono. La chiamata restituisce con un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID operazione da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Fatturazione** 
>
> La pagina dei [prezzi di visione artificiale](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) include il piano tariffario per la lettura. Ogni immagine o pagina analizzata è una transazione. Se si chiama l'operazione con un documento PDF o TIFF contenente 100 pagine, l'operazione di lettura lo conteggierà come 100 transazioni e verranno addebitate le transazioni 100. Se sono state effettuate 50 chiamate all'operazione e ogni chiamata ha inviato un documento con 100 pagine, verranno addebitate le transazioni 50 X 100 = 5000.

## <a name="step-2-the-get-read-results-operation"></a>Passaggio 2: operazione Get Read Results

Il secondo passaggio consiste nel chiamare [Get Read results](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) Operation. Questa operazione accetta come input l'ID operazione creato dall'operazione di lettura. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 1 a 2 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione non è stata avviata. |
| |  | Running: l'operazione è in corso di elaborazione. |
| |  | non riuscito: l'operazione non è riuscita. |
| |  | Succeeded: l'operazione è riuscita. |

> [!NOTE]
> Il livello gratuito limita la frequenza delle richieste a 20 chiamate al minuto. Il livello a pagamento consente 10 richieste al secondo (RPS) che possono essere aumentate su richiesta. Usare il canale del supporto di Azure o il team dell'account per richiedere una tariffa di richiesta al secondo (RPS) superiore.

Quando il campo **stato** ha il valore **succeeded** , la risposta JSON contiene il contenuto di testo estratto dall'immagine o dal documento. La risposta JSON gestisce i raggruppamenti di righe originali delle parole riconosciute. Sono incluse le righe di testo estratte e le relative coordinate del rettangolo di delimitazione. Ogni riga di testo include tutte le parole estratte con le relative coordinate e punteggi di confidenza.

> [!NOTE]
> I dati inviati all' `Read` operazione sono temporaneamente crittografati e archiviati inattivi per un breve periodo di tempo e quindi eliminati. Ciò consente alle applicazioni di recuperare il testo estratto come parte della risposta del servizio.

## <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="natural-reading-order-output"></a>Output dell'ordine di lettura naturale
Con l' [API di lettura 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)specificare l'ordine in cui vengono restituite le righe di testo con il `readingOrder` parametro di query. Usare `natural` per un output dell'ordine di lettura più semplice, come illustrato nell'esempio seguente.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Esempio di ordine di lettura OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificazione manoscritta per le righe di testo (solo alfabeto latino)
La risposta dell' [API Read 3,2 Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) include la classificazione di ogni riga di testo con stile di grafia o meno, insieme a un punteggio di confidenza. Questa funzionalità è supportata solo per le lingue latine. Nell'esempio seguente viene illustrata la classificazione manoscritta per il testo nell'immagine.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Esempio di classificazione della grafia OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Seleziona pagine o intervalli di pagine per l'estrazione del testo
Con l' [API di anteprima di lettura 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005), per documenti di grandi dimensioni, usare il `pages` parametro di query per specificare i numeri di pagina o gli intervalli di pagine per estrarre il testo solo dalle pagine. Nell'esempio seguente viene illustrato un documento con 10 pagine, con il testo estratto per entrambi i casi, tutte le pagine (1-10) e le pagine selezionate (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Output pagine selezionate":::

## <a name="supported-languages"></a>Lingue supportate
Le API di lettura supportano un totale di 73 lingue per il testo dello stile di stampa. Vedere l'elenco completo dei [linguaggi supportati dall'OCR](./language-support.md#optical-character-recognition-ocr). L'OCR in stile manoscritto è supportato esclusivamente per l'inglese.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Usare l'API cloud o distribuire in locale
Le API cloud Read 3. x sono le opzioni preferite per la maggior parte dei clienti grazie alla facilità di integrazione e alla produttività rapida. Azure e il servizio Visione artificiale gestiscono la scalabilità, le prestazioni, la sicurezza dei dati e le esigenze di conformità pur concentrandosi sulla soddisfazione delle esigenze dei clienti.

Per la distribuzione locale, il [contenitore Docker Read (anteprima)](./computer-vision-how-to-install-containers.md) consente di distribuire le nuove funzionalità OCR nell'ambiente locale. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati.

## <a name="ocr-api"></a>API OCR

L' [API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) usa un modello di riconoscimento precedente, supporta solo le immagini e viene eseguito in modo sincrono, restituendo immediatamente il testo rilevato. Vedere le [lingue supportate dall'OCR](./language-support.md#optical-character-recognition-ocr) , quindi leggere l'API.

> [!NOTE]
> Le operazioni del computer RecognizeText 2,0 sono in fase di deprecazione, a favore della nuova API Read illustrata in questo articolo. I clienti esistenti dovrebbero [passare a utilizzando le operazioni di lettura](upgrade-api-versions.md).

## <a name="next-steps"></a>Passaggi successivi

- Introduzione all' [API REST di visione artificiale o alle guide introduttive della libreria client](./quickstarts-sdk/client-library.md).
- Informazioni sull' [API REST di lettura 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Informazioni sull' [API REST di lettura 3,2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) con supporto per un totale di 73 lingue.
