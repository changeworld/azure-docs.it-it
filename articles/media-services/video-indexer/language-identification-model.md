---
title: Usare Video Indexer per identificare automaticamente le lingue parlate - Azure
titleSuffix: Azure Media Services
description: Questo articolo descrive come usare il Video Indexer di identificazione della lingua per identificare automaticamente la lingua parlata in un video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600638"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificare in modo automatico la lingua parlata con il modello di identificazione della lingua

Video Indexer supporta l'identificazione automatica della lingua (LID), ovvero il processo di identificazione automatica del contenuto della lingua parlata dall'audio e dell'invio del file multimediale da trascrivere nella lingua identificata dominante. 

Attualmente LID supporta: inglese, spagnolo, francese, tedesco, italiano, cinese tradizionale, giapponese, russo e portoghese (brasile). 

Assicurarsi di esaminare la sezione [Linee guida e limitazioni riportata](#guidelines-and-limitations) di seguito.

## <a name="choosing-auto-language-identification-on-indexing"></a>Scelta dell'identificazione automatica della lingua per l'indicizzazione

Quando si indicizza [o si esegue di nuovo l'indicizzazione](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) di un video usando l'API, scegliere `auto detect` l'opzione nel `sourceLanguage` parametro .

Quando si usa il  portale, passare ai video dell'account nella Video Indexer [home page](https://www.videoindexer.ai/) e passare il puntatore del mouse sul nome del video che si vuole indicizzare nuovamente. Nell'angolo in basso a destra fare clic sul pulsante Di nuovo indice. Nella finestra **di dialogo Re-index video (Indicizza** di nuovo video) scegliere *Auto detect (Rilevamento* automatico) nella casella di riepilogo a discesa **Video source language** (Lingua di origine video).

![rilevamento automatico](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Output del modello

Video Indexer trascrive il video in base alla lingua più probabile se l'attendibilità per tale lingua è `> 0.6` . Se la lingua non può essere identificata in modo sicuro, presuppone che la lingua parlata sia l'inglese. 

Il linguaggio dominante del modello è disponibile nel file JSON insights `sourceLanguage` come attributo (in root/videos/insights). Un punteggio di attendibilità corrispondente è disponibile anche `sourceLanguageConfidence` nell'attributo .

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

* L'identificazione automatica della lingua (LID) supporta le lingue seguenti: 

    inglese, spagnolo, francese, tedesco, italiano, cinese, giapponese, russo e portoghese (brasile).
* Anche se Video Indexer supporta l'arabo (standard moderno e levantine), hindi e coreano, queste lingue non sono supportate in LID.
* Se l'audio contiene lingue diverse dall'elenco supportato in precedenza, il risultato è imprevisto.
* Se Video Indexer la lingua con attendibilità sufficiente ( ), la lingua `>0.6` di fallback è l'inglese.
* Non è disponibile alcun supporto corrente per i file con audio in lingue miste. Se l'audio contiene lingue miste, il risultato è imprevisto. 
* L'audio di bassa qualità può influire sui risultati del modello.
* Il modello richiede almeno un minuto di voce nell'audio.
* Il modello è progettato per riconoscere un parlato conversazionale spontaneo (non comandi vocali, canti e così via).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica](video-indexer-overview.md)
* [Identificare e trascrivere in modo automatico il contenuto multilingue](multi-language-identification-transcription.md)
