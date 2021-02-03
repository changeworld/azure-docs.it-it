---
title: Novità di Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra le novità di Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: f10319de67a105b4b5e4641c4171ccd0a6e63440
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490878"
---
# <a name="whats-new-in-computer-vision"></a>Novità di Visione artificiale

Ecco cosa c'è di nuovo nel servizio. Le novità possono essere note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere aggiornati sul servizio.

## <a name="january-2021"></a>Gennaio 2021

### <a name="spatial-analysis-container-update"></a>Aggiornamento del contenitore Analisi spaziale

È stata rilasciata una nuova versione del [contenitore Analisi spaziale](spatial-analysis-container.md) che include un nuovo set di funzionalità. Questo contenitore Docker consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone e i loro movimenti negli ambienti fisici. 

* È ora possibile configurare le [operazioni di analisi spaziale](spatial-analysis-operations.md) per rilevare se una persona sta indossando una protezione facciale, ad esempio una mascherina. 
    * È possibile abilitare un classificatore di mascherine per le operazioni `personcount`, `personcrossingline` e `personcrossingpolygon` configurando il parametro `ENABLE_FACE_MASK_CLASSIFIER`.
    * Gli attributi `face_mask` e `face_noMask` verranno restituiti come metadati con un punteggio di confidenza per ogni persona rilevata nel flusso video
* L'operazione *personcrossingpolygon* è stata estesa per consentire il calcolo del tempo di permanenza di una persona in una zona. È possibile impostare il `type` parametro nella configurazione della zona per l'operazione su `zonedwelltime` e un nuovo evento di tipo *personZoneDwellTimeEvent* includerà il `durationMs` campo popolato con il numero di millisecondi impiegato dall'utente nella zona.
* **Modifica di rilievo**: l'evento *personZoneEvent* è stato rinominato in *personZoneEnterExitEvent*. Questo evento viene generato dall'operazione *personcrossingpolygon* quando una persona immette o esce dalla zona e fornisce informazioni direzionali con il lato numerato della zona che è stata superata.
* L'URL del video può essere specificato come "parametro privato/offuscato" in tutte le operazioni. L'offuscamento è ora facoltativo e funzionerà solo se `KEY` e `IV` vengono fornite come variabili di ambiente.
* La taratura è abilitata per impostazione predefinita per tutte le operazioni. Impostare `do_calibration: false` per disabilitarlo.
* Aggiunto il supporto per la ritaratura automatica (disabilitato per impostazione predefinita) tramite il `enable_recalibration` parametro. per informazioni dettagliate, vedere [operazioni di analisi spaziale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations)
* Parametri di calibrazione della fotocamera per il `DETECTOR_NODE_CONFIG` . Per informazioni dettagliate, vedere [operazioni di analisi spaziale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) .


## <a name="october-2020"></a>Ottobre 2020

### <a name="computer-vision-api-v31-ga"></a>Disponibilità generale dell'API Visione artificiale v3.1

L'API Visione artificiale disponibile a livello generale è stato aggiornata alla versione 3.1.

## <a name="september-2020"></a>Settembre 2020

### <a name="spatial-analysis-container-preview"></a>Anteprima del contenitore Analisi spaziale

Il [contenitore Analisi spaziale](spatial-analysis-container.md) è ora disponibile in anteprima. La funzionalità Analisi spaziale di Visione artificiale consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone e i loro movimenti negli ambienti fisici. Analisi spaziale è un contenitore Docker che è possibile usare in locale. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Aggiunta del riconoscimento ottico dei caratteri per il giapponese nell'API di lettura v3.1 (anteprima pubblica)
Nell'API di lettura v3.1 (anteprima pubblica) di Visione artificiale sono state aggiunte queste funzionalità:
* Riconoscimento ottico dei caratteri (OCR) per la lingua giapponese
* Per ogni riga di testo, viene indicato se l'aspetto è in stile Scrittura manuale o Stampa, insieme a un punteggio di attendibilità (solo lingue latine).
* Per un documento di più pagine è possibile estrarre il testo solo per le pagine o l'intervallo di pagine selezionati.

* Questa versione di anteprima dell'API di lettura supporta le lingue inglese, olandese, francese, tedesco, italiano, giapponese, portoghese, cinese semplificato e spagnolo.

Per altre informazioni, vedere la [panoramica dell'API di lettura](concept-recognizing-text.md).

> [!div class="nextstepaction"]
> [Altre informazioni sull'API di lettura v3.1 (anteprima pubblica 2)](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Luglio 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Aggiunta del riconoscimento ottico dei caratteri per il cinese semplificato nell'API di lettura v3.1 (anteprima pubblica)
Nell'API di lettura v3.1 (anteprima pubblica) di Visione artificiale è stato aggiunto il supporto per il cinese semplificato.

* Questa versione di anteprima dell'API di lettura supporta le lingue inglese, olandese, francese, tedesco, italiano, portoghese, cinese semplificato e spagnolo.

Per altre informazioni, vedere la [panoramica dell'API di lettura](concept-recognizing-text.md).

> [!div class="nextstepaction"]
> [Altre informazioni sull'API di lettura v3.1 (anteprima pubblica 1)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maggio 2020
API Visione artificiale v3.0 è ora in stato di disponibilità generale, con aggiornamenti per [API di lettura](concept-recognizing-text.md):

* supporto per inglese, olandese, francese, tedesco, italiano, portoghese e spagnolo
* Miglioramento della precisione
* Punteggio di attendibilità per ogni parola estratta
* Nuovo formato di output

## <a name="march-2020"></a>Marzo 2020

* TLS 1.2 viene ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza di Servizi cognitivi di Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Gennaio 2020

### <a name="read-api-30-public-preview"></a>Anteprima pubblica dell'API di lettura 3.0

È ora possibile usare la versione 3.0 dell'API di lettura per estrarre testo stampato o scritto a mano dalle immagini. Rispetto alle versioni precedenti, la 3.0 offre:
* Miglioramento della precisione
* Nuovo formato di output
* Punteggio di attendibilità per ogni parola estratta
* Supporto delle lingue spagnolo e inglese con il parametro di lingua aggiuntivo

Per iniziare a usare la versione 3.0 dell'API, seguire l'argomento di [avvio rapido sull'estrazione di testo](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3).

## <a name="cognitive-service-updates"></a>Aggiornamenti dei Servizi cognitivi

[Annunci di aggiornamento di Azure per Servizi cognitivi](https://azure.microsoft.com/updates/?product=cognitive-services)
