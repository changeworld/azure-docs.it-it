---
title: Come chiamare l'API Read
titleSuffix: Azure Cognitive Services
description: Informazioni su come chiamare l'API Read e configurarne il comportamento in modo dettagliato.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 8e0ef789653181d744100ef6e179bcf328f6d704
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308626"
---
# <a name="call-the-read-api"></a>Chiamare l'API di lettura

Questa guida illustra come chiamare l'API Read per estrarre il testo dalle immagini. Verranno illustrati i diversi modi in cui è possibile configurare il comportamento di questa API in base alle esigenze.

Questa guida presuppone che sia già stata <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" creata una risorsa di visione artificiale "  target="_blank"> creare una risorsa visione artificiale </a> e ottenere una chiave di sottoscrizione e un URL dell'endpoint. Se non è stato fatto, seguire una [Guida introduttiva](../quickstarts-sdk/client-library.md) per iniziare.

## <a name="submit-data-to-the-service"></a>Inviare dati al servizio

La [chiamata Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) dell'API Read accetta un'immagine o un documento PDF come input ed estrae il testo in modo asincrono.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

La chiamata restituisce con un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID operazione da usare nel passaggio successivo.

|Intestazione risposta| Valore di esempio |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Fatturazione** 
>
> La pagina dei [prezzi di visione artificiale](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) include il piano tariffario per la lettura. Ogni immagine o pagina analizzata è una transazione. Se si chiama l'operazione con un documento PDF o TIFF contenente 100 pagine, l'operazione di lettura lo conteggierà come 100 transazioni e verranno addebitate le transazioni 100. Se sono state effettuate 50 chiamate all'operazione e ogni chiamata ha inviato un documento con 100 pagine, verranno addebitate le transazioni 50 X 100 = 5000.

## <a name="determine-how-to-process-the-data"></a>Determinare come elaborare i dati

### <a name="language-specification"></a>Specifica del linguaggio

La chiamata di [lettura](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) ha un parametro di richiesta facoltativo per la lingua. Read supporta l'identificazione automatica della lingua e i documenti multilingue, in modo da fornire solo un codice di lingua se si vuole forzare l'elaborazione del documento come linguaggio specifico.

### <a name="natural-reading-order-output-latin-languages-only"></a>Output dell'ordine di lettura naturale (solo lingue latine)
Specificare l'ordine in cui vengono restituite le righe di testo con il `readingOrder` parametro di query. Usare `natural` per un output dell'ordine di lettura più semplice, come illustrato nell'esempio seguente. Questa funzionalità è supportata solo per le lingue latine.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Esempio di ordine di lettura OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Seleziona pagine o intervalli di pagine per l'estrazione del testo
Per documenti di grandi dimensioni, utilizzare il `pages` parametro di query per specificare i numeri di pagina o gli intervalli di pagine per estrarre il testo solo da tali pagine. Nell'esempio seguente viene illustrato un documento con 10 pagine, con il testo estratto per entrambi i casi, tutte le pagine (1-10) e le pagine selezionate (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Output pagine selezionate":::

## <a name="get-results-from-the-service"></a>Ottenere risultati dal servizio

Il secondo passaggio consiste nel chiamare [Get Read results](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750) Operation. Questa operazione accetta come input l'ID operazione creato dall'operazione di lettura. 

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. 

|Valore | Significato |
|:-----|:----|
| `notStarted`| L'operazione non è stata avviata. |
| `running`| È in corso l'elaborazione dell'operazione. |
| `failed`| L'operazione non è riuscita. |
| `succeeded`| L'operazione è stata completata. |

Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 1 a 2 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

> [!NOTE]
> Il livello gratuito limita la frequenza delle richieste a 20 chiamate al minuto. Il livello a pagamento consente 10 richieste al secondo (RPS) che possono essere aumentate su richiesta. Prendere nota della identfier e dell'area geografica di Azure e aprire un ticket di supporto di Azure o contattare il team dell'account per richiedere una tariffa di richiesta al secondo (RPS) superiore.

Quando il campo **stato** presenta il `succeeded` valore, la risposta JSON contiene il contenuto di testo estratto dall'immagine o dal documento. La risposta JSON gestisce i raggruppamenti di righe originali delle parole riconosciute. Sono incluse le righe di testo estratte e le relative coordinate del rettangolo di delimitazione. Ogni riga di testo include tutte le parole estratte con le relative coordinate e punteggi di confidenza.

> [!NOTE]
> I dati inviati all' `Read` operazione sono temporaneamente crittografati e archiviati inattivi per un breve periodo di tempo e quindi eliminati. Ciò consente alle applicazioni di recuperare il testo estratto come parte della risposta del servizio.

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Classificazione manoscritta per le righe di testo (solo lingue latine)
La risposta include la classificazione di ogni riga di testo con stile di grafia o meno, insieme a un punteggio di confidenza. Questa funzionalità è supportata solo per le lingue latine. Nell'esempio seguente viene illustrata la classificazione manoscritta per il testo nell'immagine.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Esempio di classificazione della grafia OCR":::

## <a name="next-steps"></a>Passaggi successivi

Per provare l'API REST, vedere le informazioni di [riferimento sull'API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
