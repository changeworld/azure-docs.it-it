---
title: Informazioni sulla Visione artificiale
titleSuffix: Azure Cognitive Services
description: Il servizio Visione artificiale consente di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: visione artificiale, applicazioni di visione artificiale, servizio visione artificiale
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286135"
---
# <a name="what-is-computer-vision"></a>Informazioni sulla Visione artificiale

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Il servizio Visione artificiale di Azure consente di accedere ad algoritmi avanzati che elaborano le immagini e restituiscono informazioni, in base alle caratteristiche visive a cui si è interessati. 

| Servizio|Descrizione|
|---|---|
|[Riconoscimento ottico dei caratteri (OCR)](overview-ocr.md)|Il servizio OCR (Optical Character Recognition) estrae il testo dalle immagini. È possibile usare la nuova API di lettura per estrarre il testo stampato e scritto a mano da foto e documenti. USA modelli basati sull'apprendimento avanzato e funziona con testo su un'ampia gamma di superfici e sfondi. Sono inclusi documenti aziendali, fatture, ricevute, poster, biglietti da visita, lettere e lavagne. Le API OCR supportano l'estrazione di testo stampato in [diversi linguaggi](./language-support.md). Per iniziare, seguire la [Guida introduttiva a OCR](quickstarts-sdk/client-library.md) .|
|[Analisi delle immagini](overview-image-analysis.md)| Il servizio di analisi delle immagini estrae molte funzionalità visive da immagini, ad esempio oggetti, visi, contenuto per adulti e descrizioni di testo generate automaticamente. Per iniziare, seguire la [Guida introduttiva all'analisi delle immagini](quickstarts-sdk/image-analysis-client-library.md) .|
| [Analisi spaziale](intro-to-spatial-analysis-public-preview.md)| Il servizio di analisi spaziale analizza la presenza e lo spostamento di persone su un feed video e produce eventi a cui altri sistemi possono rispondere. Installare il [contenitore di analisi spaziale](spatial-analysis-container.md) per iniziare.|

## <a name="computer-vision-for-digital-asset-management"></a>Visione artificiale per la gestione delle risorse digitali

Visione artificiale può ottimizzare molti scenari di gestione delle risorse digitali. La gestione delle risorse digitali è il processo aziendale per l'organizzazione, l'archiviazione e il recupero di risorse multimediali elaborate e per la gestione dei diritti e delle autorizzazioni digitali. Una società potrebbe ad esempio voler raggruppare e identificare le immagini in base a logo visibili, visi, oggetti, colori e così via. Oppure si potrebbe voler [generare didascalie per le immagini](./Tutorials/storage-lab-tutorial.md) automaticamente e allegare parole chiave in modo che siano disponibili per la ricerca. Per una soluzione di gestione delle risorse digitali all-in-one che usa Servizi cognitivi, Ricerca cognitiva di Azure e creazione di report intelligenti, vedere la [guida su Solution Accelerator per Knowledge Mining](https://github.com/Azure-Samples/azure-search-knowledge-mining) in GitHub. Per altri esempi di gestione delle risorse digitali, vedere il repository [Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) (Modelli per la soluzione Visione artificiale).

## <a name="image-requirements"></a>Requisiti dell'immagine

Visione artificiale può analizzare immagini che rispettano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere superiori a 50 x 50 pixel
  - Per l'API di lettura (Read), le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 10000 x 10000 pixel.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Seguire una guida introduttiva per implementare ed eseguire un servizio nel linguaggio di sviluppo preferito.

* [Guida introduttiva: riconoscimento ottico del carattere (OCR)](quickstarts-sdk/client-library.md)
* [Guida introduttiva: analisi immagini](quickstarts-sdk/image-analysis-client-library.md)
* [Guida introduttiva: contenitore di analisi spaziale](spatial-analysis-container.md)
