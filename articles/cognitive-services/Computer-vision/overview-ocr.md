---
title: Che cos'è il riconoscimento ottico di caratteri?
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento ottico dei caratteri estrae il testo visibile in un'immagine e lo restituisce come stringhe strutturate.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 41b3552a633c9cebce1138fa042dbd154eee0cb5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314117"
---
# <a name="what-is-optical-character-recognition"></a>Che cos'è il riconoscimento ottico di caratteri?

Il servizio di riconoscimento ottico dei caratteri (OCR) consente di estrarre testo stampato o scritto a mano dalle immagini, ad esempio foto di segni stradali e prodotti, nonché da documenti &mdash; fatturati, fatture, report finanziari, articoli e altro ancora. USA modelli basati sull'apprendimento avanzato e funziona con testo su un'ampia gamma di superfici e sfondi.

Le API OCR supportano l'estrazione di testo stampato in [diversi linguaggi](./language-support.md). Per iniziare, seguire una [guida di avvio rapido](./quickstarts-sdk/client-library.md).

![Demo OCR](./Images/ocr-demo.gif)

Questa documentazione contiene i seguenti tipi di articoli:
* Le [guide introduttive](./quickstarts-sdk/client-library.md) sono istruzioni dettagliate che consentono di effettuare chiamate al servizio e ottenere risultati in un breve periodo di tempo. 
* Le [guide alle procedure](./Vision-API-How-to-Topics/call-read-api.md) contengono istruzioni per l'uso del servizio in modi più specifici o personalizzati.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Lingue supportate
Le API OCR supportano un totale di 73 lingue per il testo dello stile di stampa. Vedere l'elenco completo dei [linguaggi supportati dall'OCR](./language-support.md#optical-character-recognition-ocr). L'OCR in stile manoscritto è supportato esclusivamente per l'inglese.

## <a name="input-requirements"></a>Requisiti di input

La chiamata di **lettura** accetta immagini e documenti come input. Hanno i requisiti seguenti:

* Formati di file supportati: JPEG, PNG, BMP, PDF e TIFF
* Per i file PDF e TIFF, vengono elaborate fino a 2000 pagine (solo le prime due pagine per il livello gratuito).
* Le dimensioni del file devono essere inferiori a 50 MB (4 MB per il livello gratuito) e dimensioni di almeno 50 x 50 pixel e al massimo 10000 x 10000 pixel. 
* Le dimensioni PDF devono essere al massimo 17 x 17 pollici, corrispondenti a dimensioni della carta note o a3 e inferiori.

## <a name="read-api"></a>API di lettura 

L' [API visione artificiale Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) è la tecnologia OCR più recente di Azure ([informazioni sulle novità](./whats-new.md)) che estrae il testo stampato (in diversi linguaggi), il testo scritto a mano (solo in inglese), le cifre e i simboli di valuta da immagini e documenti PDF a più pagine. È ottimizzato per estrarre testo da immagini con un elevato numero di testo e documenti PDF a più pagine con lingue miste. Supporta il rilevamento di testo stampato e scritto a mano nella stessa immagine o documento.

![Come OCR converte immagini e documenti in un output strutturato con testo Estratto](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Usare l'API cloud o distribuire in locale
Le API cloud Read 3. x sono le opzioni preferite per la maggior parte dei clienti grazie alla facilità di integrazione e alla produttività rapida. Azure e il servizio Visione artificiale gestiscono la scalabilità, le prestazioni, la sicurezza dei dati e le esigenze di conformità pur concentrandosi sulla soddisfazione delle esigenze dei clienti.

Per la distribuzione locale, il [contenitore Docker Read (anteprima)](./computer-vision-how-to-install-containers.md) consente di distribuire le nuove funzionalità OCR nell'ambiente locale. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati.

## <a name="ocr-api"></a>API OCR

L' [API OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) legacy usa un modello di riconoscimento precedente, supporta solo le immagini ed esegue in modo sincrono, restituendo immediatamente il testo rilevato. Per un elenco delle lingue supportate, vedere la colonna OCR delle [lingue supportate](./language-support.md#optical-character-recognition-ocr) .

> [!WARNING]
> Le operazioni di Visione artificiale 2,0 RecognizeText sono in fase di deprecazione, a favore della nuova [API Read](#read-api) illustrata in questo articolo. I clienti esistenti dovrebbero [passare a utilizzando le operazioni di lettura](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione all' [API REST di OCR (lettura) o alle guide introduttive della libreria client](./quickstarts-sdk/client-library.md).
- Informazioni sull' [API REST di lettura 3,2](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
