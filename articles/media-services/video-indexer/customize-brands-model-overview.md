---
title: Personalizzare il modello dei marchi con Video Indexer - Azure
titleSuffix: Azure Media Services
description: Questo articolo fornisce informazioni generali sul modello dei marchi in Video Indexer e come personalizzarlo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586075"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizzare un modello dei marchi con Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, il nome Microsoft viene citato in un contenuto audio o video o se appare nel testo visivo di un video, Video Indexer lo rileva come un marchio. I marchi vengono contraddistinti senza ambiguità dagli altri termini in base al contesto.

Il rilevamento del marchio è utile in un'ampia gamma di scenari aziendali come l'archiviazione e l'individuazione di contenuti, la pubblicità contestuale, l'analisi dei social media, l'analisi comparativa di punti vendita al dettaglio e molto altro ancora. La funzione di rilevamento del marchio di Video Indexer consente inoltre di indicizzare citazioni del marchio nel parlato e nel testo visivo tramite il database dei marchi di Bing o mediante la personalizzazione compilando un modello dei marchi personalizzato per ogni account di Video Indexer. La funzionalità del modello Custom Brands consente di specificare se Video Indexer rileverà le marche dal database dei marchi Bing, escludere determinati marchi (essenzialmente creando un elenco di marchi non approvati) e includere i marchi che devono far parte del modello che potrebbero non trovarsi nel database dei marchi di Bing (essenzialmente creando un elenco di marchi approvati). Il modello Marchi personalizzato creato sarà disponibile solo nell'account in cui è stato creato.

## <a name="out-of-the-box-detection-example"></a>Esempio di rilevamento predefinito

Nella presentazione "Microsoft Build 2017 Day 2", il marchio "Microsoft Windows" viene visualizzato più volte. talvolta nella trascrizione, altre volte come testo visivo e mai come verbatim. Video Indexer rileva con alta precisione che un termine è un marchio in base al contesto e copre oltre 90.000 marchi predefiniti (in costante aggiornamento). Al minuto 02:25 Video Indexer identifica il brand nel parlato e al minuto 02:40 lo rileva dal testo visivo come parte del logo Windows.

![Panoramica sui marchi](./media/content-model-customization/brands-overview.png)

Se si parla di "windows" (finestre) in ambito edilizio, la parola "windows" non verrà rilevata come marchio grazie ad avanzati algoritmi di Machine Learning in grado di risolvere l'ambiguità in base al contesto. Lo stesso vale per Box (scatola), Apple (mela) e Fox (volpe). Il rilevamento del marchio viene usato per tutte le lingue supportate.  

## <a name="next-steps"></a>Passaggi successivi

Per portare i propri marchi, consultare gli argomenti seguenti:

[Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)

[Personalizzare il modello dei marchi usando il sito Web](customize-brands-model-with-website.md)
