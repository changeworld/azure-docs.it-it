---
title: Che cos'è il riconoscimento ottico dei caratteri?
titleSuffix: Azure Cognitive Services
description: Il servizio di riconoscimento ottico dei caratteri (OCR) estrae il testo visibile in un'immagine e lo restituisce come stringhe strutturate.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536731"
---
# <a name="what-is-optical-character-recognition"></a>Che cos'è il riconoscimento ottico dei caratteri?

Il servizio Riconoscimento ottico dei caratteri (OCR) consente di estrarre testo stampato o scritto a mano da immagini, ad esempio foto di segnali stradali e prodotti, nonché da fatture, fatture, report finanziari, articoli e altro &mdash; ancora. Usa modelli basati su Deep Learning e funziona con testo su un'ampia gamma di superfici e sfondi.

Le API OCR supportano l'estrazione di testo stampato in [diverse lingue.](./language-support.md) Per iniziare, seguire una [guida di avvio rapido](./quickstarts-sdk/client-library.md).

![Demo OCR](./Images/ocr-demo.gif)

Questa documentazione contiene i tipi di articoli seguenti:
* Le [guide introduttive](./quickstarts-sdk/client-library.md) sono istruzioni dettagliate che consentono di effettuare chiamate al servizio e ottenere risultati in un breve periodo di tempo. 
* Le [guide alle attività contengono](./Vision-API-How-to-Topics/call-read-api.md) istruzioni per l'uso del servizio in modi più specifici o personalizzati.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Lingue supportate
Le API OCR supportano un totale di 73 lingue per il testo in stile di stampa. Vedere l'elenco completo delle [lingue supportate da OCR.](./language-support.md#optical-character-recognition-ocr) L'OCR di tipo scritto a mano è supportato esclusivamente per l'inglese.

## <a name="input-requirements"></a>Requisiti di input

La **chiamata Read** accetta immagini e documenti come input. Hanno i requisiti seguenti:

* Formati di file supportati: JPEG, PNG, BMP, PDF e TIFF
* Per i file PDF e TIFF, vengono elaborate fino a 2000 pagine (solo le prime due pagine per il livello gratuito).
* Le dimensioni del file devono essere inferiori a 50 MB (4 MB per il livello gratuito) e dimensioni di almeno 50 x 50 pixel e al massimo 10000 x 10000 pixel. 

## <a name="read-api"></a>API di lettura 

[L'API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) di lettura Visione artificiale è la tecnologia[](./whats-new.md)OCR più recente di Azure (informazioni sulle novità) che estrae testo stampato (in diverse lingue), testo scritto a mano (solo inglese), cifre e simboli di valuta da immagini e documenti PDF a più pagine. È ottimizzato per estrarre testo da immagini con molto testo e documenti PDF a più pagine con lingue miste. Supporta il rilevamento di testo stampato e scritto a mano nella stessa immagine o documento.

![Come OCR converte immagini e documenti in output strutturato con testo estratto](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Funzionalità principali

L'API Lettura include le funzionalità seguenti. 

* Stampare l'estrazione del testo in 73 lingue
* Estrazione di testo scritto a mano in inglese
* Righe e parole di testo con punteggi di posizione e attendibilità
* Non è richiesta alcuna identificazione della lingua
* Supporto per lingue miste, modalità mista (stampa e scrittura manuale)
* Selezionare pagine e intervalli di pagine da documenti di grandi dimensioni a più pagine
* Ordine di lettura naturale per le righe di testo
* Classificazione della scrittura manuale per le righe di testo
* Disponibile come contenitore Docker senza distribuzione per la distribuzione locale

Informazioni [su come usare le funzionalità OCR.](./vision-api-how-to-topics/call-read-api.md)

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Usare l'API cloud o distribuire in locale
Le API cloud Read 3.x sono l'opzione preferita per la maggior parte dei clienti grazie alla semplicità di integrazione e alla produttività rapida. Azure e il servizio Visione artificiale gestire le esigenze di scalabilità, prestazioni, sicurezza dei dati e conformità, concentrando l'attenzione sulle esigenze dei clienti.

Per la distribuzione locale, il contenitore Docker di lettura [(anteprima)](./computer-vision-how-to-install-containers.md) consente di distribuire le nuove funzionalità OCR nel proprio ambiente locale. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati.

## <a name="ocr-api"></a>OCR API

[L'API OCR legacy](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) usa un modello di riconoscimento meno recente, supporta solo immagini ed esegue in modo sincrono, restituisce immediatamente il testo rilevato. Per un elenco delle lingue supportate, vedere la colonna OCR [delle](./language-support.md#optical-character-recognition-ocr) lingue supportate.

> [!WARNING]
> Le Visione artificiale RecognizeText 2.0 sono in fase di deprecazione a favore della nuova [API di](#read-api) lettura trattata in questo articolo. I clienti esistenti devono [passare all'uso delle operazioni di lettura](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione [all'API REST OCR (lettura) o alle](./quickstarts-sdk/client-library.md)guide introduttive alla libreria client .
- Informazioni [sull'API REST Read 3.2.](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
