---
title: Identificare e trascrivere automaticamente il contenuto in più lingue con Video Indexer
titleSuffix: Azure Media Services
description: Questo argomento illustra come identificare e trascrivere automaticamente il contenuto in più lingue con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532910"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Identificare e trascrivere in modo automatico il contenuto multilingue

Video Indexer supporta l'identificazione automatica della lingua e la trascrizione nel contenuto in più lingue. Questo processo comporta l'identificazione automatica della lingua parlata in segmenti diversi dall'audio, l'invio di ogni segmento del file multimediale da trascrivere e la combinazione della trascrizione a una trascrizione unificata. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Scelta dell'identificazione multilingue per l'indicizzazione con il portale

È possibile scegliere **il rilevamento in più lingue** durante il caricamento e l'indicizzazione del video. In alternativa, è possibile scegliere **il rilevamento multi-lingua**  quando si esegue di nuovo l'indicizzazione del video. La procedura seguente descrive come reindicizzare:

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Passare alla pagina **Libreria** e passare il puntatore del mouse sul nome del video da reindicizzare. 
1. Nell'angolo in basso a destra fare clic sul **pulsante Ri-indicizza video.** 
1. Nella finestra **di dialogo Re-index video (Re-index video)** scegliere **Rilevamento multi-lingua** dalla casella di riepilogo a discesa **Lingua** di origine video.

    * Quando un video viene indicizzato come multi-lingua, la pagina di informazioni dettagliate includerà tale opzione e verrà visualizzato un tipo di informazioni dettagliate aggiuntivo, che consente all'utente di visualizzare il segmento trascritto in quale lingua è "Lingua parlata".
    * La traduzione in tutte le lingue è completamente disponibile dalla trascrizione in più lingue.
    * Tutte le altre informazioni dettagliate verranno visualizzate nella lingua master rilevata, che è la lingua più visualizzata nell'audio.
    * I sottotitoli codificati sul lettore sono disponibili anche in più lingue.

![Funzionalità del portale](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Scelta dell'identificazione multilingue per l'indicizzazione con l'API

Quando si esegue [l'indicizzazione o la reindicizzazione](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) di un video usando l'API, scegliere `multi-language detection` l'opzione nel `sourceLanguage` parametro .

### <a name="model-output"></a>Output del modello

Il modello recupererà tutte le lingue rilevate nel video in un unico elenco

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Inoltre, ogni istanza nella sezione trascrizione includerà la lingua in cui è stata trascritta

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

* Set di lingue supportate: inglese, francese, tedesco, spagnolo.
* Supporto per contenuto in più lingue con un massimo di tre lingue supportate.
* Se l'audio contiene lingue diverse dall'elenco supportato precedente, il risultato è imprevisto.
* Lunghezza minima del segmento da rilevare per ogni lingua: 15 secondi.
* L'offset di rilevamento della lingua è di 3 secondi in media.
* È previsto che il riconoscimento vocale sia continuo. Le frequenti alternazioni tra linguaggi possono influire sulle prestazioni dei modelli.
* La voce di parlanti non nativi può influire sulle prestazioni del modello, ad esempio quando i parlanti usano la lingua nativa e passano a un'altra lingua.
* Il modello è progettato per riconoscere un parlato conversazionale spontaneo con un'acustica audio ragionevole (non comandi vocali, canto e così via).
* La creazione e la modifica di progetti non sono attualmente disponibili per i video in più lingue.
* I modelli linguistici personalizzati non sono disponibili quando si usa il rilevamento multi-lingua.
* L'aggiunta di parole chiave non è supportata.
* Quando si esportano file di sottotitoli codificati, l'indicazione della lingua non viene visualizzata.
* L'API di trascrizione dell'aggiornamento non supporta file in più lingue.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
