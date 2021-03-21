---
title: Concetti di Video Indexer-Azure
titleSuffix: Azure Media Services Video Indexer
description: Questo articolo fornisce una breve panoramica di servizi multimediali di Azure Video Indexer la terminologia e i concetti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633982"
---
# <a name="video-indexer-concepts"></a>Concetti di Video Indexer

Questo articolo fornisce una breve panoramica di servizi multimediali di Azure Video Indexer la terminologia e i concetti.

## <a name="audiovideocombined-insights"></a>Informazioni dettagliate audio/video/combinate

Quando si caricano i video in Video Indexer, vengono analizzati sia gli oggetti visivi che l'audio eseguendo modelli di intelligenza artificiale diversi. Mentre Video Indexer analizza il video, le informazioni dettagliate vengono estratte dai modelli di intelligenza artificiale. Per ulteriori informazioni, vedere [Panoramica](video-indexer-overview.md).

## <a name="confidence-scores"></a>Punteggi di attendibilità 

Il Punteggio di confidenza indica la confidenza in un'analisi. È un numero compreso tra 0,0 e 1,0. Maggiore sarà il punteggio, maggiore sarà l'attendibilità della risposta. Ad esempio, 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Moderazione del contenuto

Usare i modelli di moderazione dei contenuti testuali e visivi per proteggere gli utenti dal contenuto inappropriato e verificare che i contenuti pubblicati corrispondano ai valori dell'organizzazione. È possibile bloccare automaticamente determinati video o avvisare gli utenti sul contenuto. Per altre informazioni, vedere [Insights: moderazione del contenuto visivo e testuale](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blocchi   

I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa.  

## <a name="project-and-editor"></a>Progetto ed editor

Il sito Web di [video Indexer](https://www.videoindexer.ai/) consente di usare informazioni approfondite del video per: trovare il contenuto multimediale appropriato, individuare le parti a cui si è interessati e usare i risultati per creare un progetto completamente nuovo. Una volta creato, è possibile eseguire il rendering e il download del progetto da Video Indexer e usarlo nelle applicazioni di modifica o nei flussi di lavoro downstream.

Di seguito sono riportati alcuni scenari in cui questa funzionalità può risultare utile: 

* Creazione di filmati principali per i trailer.
* Uso di vecchie clip di video nei cast di notizie.
* Creazione di contenuto più breve per i social media.

Per ulteriori informazioni, vedere [utilizzare l'editor per creare progetti](use-editor-create-project.md).

## <a name="keyframes"></a>KeyFrames

Video Indexer seleziona i frame che meglio rappresentano ogni scatto. I fotogrammi chiave sono i fotogrammi rappresentativi selezionati dall'intero video in base alle proprietà estetiche, ad esempio contrasto e stabilità. Per altre informazioni, vedere [Scene, scatti e fotogrammi chiave](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>Intervallo di tempo e intervallo di tempo rettificato   

TimeRange è l'intervallo di tempo del video originale. Intervallo di tempo rispetto alla playlist corrente. Poiché è possibile creare una playlist da righe diverse di video diversi, è possibile prendere un video di 1 ora e usarne solo una riga, ad esempio, 10:00-10:15. In questo caso si avrà una playlist con 1 riga, in cui l'intervallo di tempo è 10:00-10:15 ma l'intervallo di tempo rettificato è 00:00-00:15. 

## <a name="widgets"></a>Widget

Video Indexer supporta l'incorporamento dei widget nelle app. Per altre informazioni, vedere [incorporare video Indexer widget nelle app](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Informazioni dettagliate riepilogate  

Le informazioni dettagliate riepilogate contengono una visualizzazione aggregata dei dati: visi, argomenti ed emozioni. Ad esempio, invece di esaminare singolarmente le migliaia di intervalli di tempo e controllare quali visi contengono, le informazioni dettagliate riepilogate mostrano tutti i visi e, per ognuno di essi, gli intervalli di tempo in cui viene visualizzato e la percentuale di tempo per cui viene visualizzato.  

## <a name="next-steps"></a>Passaggi successivi

- [panoramica](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
