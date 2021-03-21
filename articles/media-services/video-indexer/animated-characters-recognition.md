---
title: Rilevamento dei caratteri animati con Video Indexer
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare il rilevamento di caratteri animati con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854751"
---
# <a name="animated-character-detection-preview"></a>Rilevamento di caratteri animati (anteprima)

Servizi multimediali di Azure Video Indexer supporta il rilevamento, il raggruppamento e il riconoscimento di caratteri nel contenuto animato tramite l'integrazione con la [visione personalizzata di servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Questa funzionalità è disponibile sia tramite il portale che tramite l'API.

Dopo aver caricato un video animato con un modello di animazione specifico, Video Indexer estrae i fotogrammi chiave, rileva i caratteri animati in questi frame, raggruppa un carattere simile e sceglie l'esempio migliore. Invia quindi i caratteri raggruppati a Visione personalizzata che identifica i caratteri in base ai modelli su cui è stato eseguito il training. 

Prima di iniziare a eseguire il training del modello, i caratteri vengono rilevati senza alcun nome. Quando si aggiungono nomi e si esegue il training del modello, il Video Indexer riconoscerà i caratteri e li definirà di conseguenza.

## <a name="flow-diagram"></a>Diagramma di flusso

Il diagramma seguente illustra il flusso del processo di rilevamento dei caratteri animati.

![Diagramma di flusso](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Account

A seconda di un tipo di account di Video Indexer, sono disponibili diversi set di funzionalità. Per informazioni su come connettere l'account ad Azure, vedere [creare un account video Indexer connesso ad Azure](connect-to-azure.md).

* Account di valutazione: Video Indexer usa un account di Visione personalizzata interno per creare un modello e connetterlo all'account Video Indexer. 
* Account a pagamento: è possibile connettere l'account Visione personalizzata all'account di Video Indexer (se non si dispone già di un account, è necessario creare prima un account).

### <a name="trial-vs-paid"></a>Versione di valutazione e a pagamento

|Funzionalità|Versione di valutazione|Paid|
|---|---|---|
|Account Visione personalizzata|Gestito dietro le quinte da Video Indexer. |L'account Visione personalizzata è connesso al Video Indexer.|
|Numero di modelli di animazione|Uno|Fino a 100 modelli per account (limitazione Visione personalizzata).|
|Training del modello|Video Indexer addestra il modello per i nuovi caratteri esempi aggiuntivi di caratteri esistenti.|Il proprietario dell'account esegue il training del modello quando è pronto per apportare modifiche.|
|Opzioni avanzate in Visione personalizzata|Nessun accesso al portale di Visione personalizzata.|È possibile modificare i modelli nel portale di Visione personalizzata.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Usare il rilevamento dei caratteri animati con il portale e l'API

Per informazioni dettagliate, vedere [usare il rilevamento dei caratteri animati con il portale e l'API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Limitazioni

* Attualmente, la funzionalità di identificazione dell'animazione non è supportata in East-Asia area.
* I caratteri che sembrano essere piccoli o lontani nel video potrebbero non essere identificati correttamente se la qualità del video è insufficiente.
* Si consiglia di usare un modello per set di caratteri animati, ad esempio per una serie animata.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)