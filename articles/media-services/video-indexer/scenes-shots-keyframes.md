---
title: Video Indexer scene, riprese e fotogrammi chiave
titleSuffix: Azure Media Services
description: Questo argomento offre una panoramica dei fotogrammi Video Indexer scene, riprese e fotogrammi chiave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532425"
---
# <a name="scenes-shots-and-keyframes"></a>Scene, scatti e fotogrammi chiave

Video Indexer supporta la segmentazione dei video in unità temporali in base alle proprietà strutturali e semantiche. Questa funzionalità consente ai clienti di esplorare, gestire e modificare facilmente il contenuto video in base a granularità variabili. Ad esempio, in base a scene, riprese e fotogrammi chiave, descritti in questo argomento.   

![Scene, scatti e fotogrammi chiave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Rilevamento della scena  
 
Video Indexer determina quando una scena cambia nel video in base ai segnali visivi. Una scena rappresenta un singolo evento ed è costituita da una serie di scatti consecutivi, correlati semanticamente. Un'anteprima della scena è il primo fotogramma chiave dello shot sottostante. L'indicizzatore video segmenta un video in scene in base alla coerenza dei colori tra le riprese consecutive e recupera l'ora di inizio e di fine di ogni scena. Il rilevamento della scena è considerato un'attività complessa perché comporta la quantificazione degli aspetti semantici dei video.

> [!NOTE]
> Applicabile ai video che contengono almeno 3 scene.

## <a name="shot-detection"></a>Rilevamento inquadratura

Video Indexer determina quando una ripresa cambia nel video in base ai segnali visivi, tracciando transizioni sia improve che graduali nella combinazione di colori dei fotogrammi adiacenti. I metadati dello shot includono un'ora di inizio e di fine, nonché l'elenco di fotogrammi chiave inclusi in tale ripresa. Le riprese sono fotogrammi consecutivi acquisiti contemporaneamente dalla stessa fotocamera.

## <a name="keyframe-detection"></a>Rilevamento dei fotogrammi chiave

Video Indexer seleziona i fotogrammi che meglio rappresentano ogni ripresa. I fotogrammi chiave sono i fotogrammi rappresentativi selezionati dall'intero video in base alle proprietà estetiche (ad esempio, contrasto e stabilità). Video Indexer recupera un elenco di ID fotogramma chiave come parte dei metadati dello shot, in base ai quali i clienti possono estrarre il fotogramma chiave come immagine ad alta risoluzione.  

### <a name="extracting-keyframes"></a>Estrazione di fotogrammi chiave

Per estrarre fotogrammi chiave ad alta risoluzione per il video, è prima necessario caricare e indicizzare il video.

![Fotogrammi chiave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Con il sito Video Indexer web

Per estrarre fotogrammi chiave usando il Video Indexer, caricare e indicizzare il video. Al termine del processo di indicizzazione, fare clic sul **pulsante Scarica** e selezionare **Artefatti (ZIP).** Verrà scaricata la cartella artifacts nel computer. 

![Screenshot che mostra l'elenco a discesa "Download" con "Artifacts" selezionato.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Decomprimere e aprire la cartella. Nella *_KeyframeThumbnail* e si trovano tutti i fotogrammi chiave estratti dal video. 

#### <a name="with-the-video-indexer-api"></a>Con l'API Video Indexer

Per ottenere fotogrammi chiave usando l'API Video Indexer, caricare e indicizzare il video usando [la chiamata Carica](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) video. Al termine del processo di indicizzazione, chiamare [Get Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index). Verranno così visualizzate tutte le informazioni dettagliate Video Indexer estratte dal contenuto in un file JSON.  

Si otterrà un elenco di ID fotogrammi chiave come parte dei metadati di ogni ripresa. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Sarà ora necessario eseguire ognuno di questi ID fotogramma chiave nella [chiamata Get Thumbnails.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) In questo modo, ogni immagine del fotogramma chiave verrà scaricata nel computer. 

## <a name="editorial-shot-type-detection"></a>Rilevamento del tipo di ripresa editoriale

I fotogrammi chiave sono associati alle riprese nel codice JSON di output. 

Il tipo di ripresa associato a un singolo colpo nel codice JSON insights rappresenta il tipo editoriale. Queste caratteristiche del tipo di ripresa possono risultare utili quando si modificano video in clip, trailer o quando si cerca uno stile specifico di fotogramma chiave per scopi musicali. I diversi tipi vengono determinati in base all'analisi del primo fotogramma chiave di ogni ripresa. Gli spari sono identificati dalla scala, dalle dimensioni e dalla posizione dei visi visualizzati nel primo fotogramma chiave. 

Le dimensioni e la scala dello shot vengono determinate in base alla distanza tra la fotocamera e i visi visualizzati nel fotogramma. Usando queste proprietà, Video Indexer rileva i tipi di ripresa seguenti:

* Wide: mostra il corpo di un'intera persona.
* Media: mostra la parte superiore e il viso di una persona.
* Da vicino: mostra principalmente il viso di una persona.
* Primo livello: mostra il viso di una persona che riempie lo schermo. 

I tipi di ripresa possono essere determinati anche in base alla posizione dei caratteri del soggetto rispetto al centro del fotogramma. Questa proprietà definisce i tipi di ripresa seguenti in Video Indexer:

* Viso sinistro: una persona viene visualizzata sul lato sinistro della cornice.
* Viso centrale: una persona viene visualizzata nell'area centrale della cornice.
* Viso destro: una persona viene visualizzata sul lato destro della cornice.
* Esterno: una persona viene visualizzata in un ambiente esterno.
* Interno: una persona viene visualizzata in un ambiente interno.

Caratteristiche aggiuntive:

* Due scatti: mostra i visi di due persone di medie dimensioni.
* Più visi: più di due persone.


## <a name="next-steps"></a>Passaggi successivi

[Esaminare l Video Indexer output prodotto dall'API](video-indexer-output-json-v2.md#scenes)
