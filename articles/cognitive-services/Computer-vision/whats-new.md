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
ms.openlocfilehash: e42096fc32a504ae329d3b179004b6a123de4469
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365638"
---
# <a name="whats-new-in-computer-vision"></a>Novità di Visione artificiale

Ecco cosa c'è di nuovo nel servizio. Le novità possono essere note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere aggiornati sul servizio.

## <a name="april-2021"></a>Aprile 2021

### <a name="computer-vision-v32-ga"></a>Visione artificiale versione 3.2 ga

L Visione artificiale aPI v3.2 è ora disponibile a livello generale con gli aggiornamenti seguenti:
* Modello di assegnazione di tag alle immagini migliorato: analizza il contenuto visivo e genera tag pertinenti in base a oggetti, azioni e contenuto visualizzati nell'immagine. Questa funzionalità è disponibile tramite [l'API Tag Image.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) Per altre informazioni, [vedere la guida e la](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) panoramica sull'analisi delle immagini. [](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis)
* Modello di moderazione del contenuto aggiornato: rileva la presenza di contenuto per adulti e fornisce flag per filtrare le immagini contenenti contenuto visivo per adulti, racy e gory. Questa funzionalità è disponibile tramite [l'API Analyze.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) Per altre informazioni, [vedere la guida e la](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) panoramica sull'analisi delle immagini. [](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis)
* [OCR (Lettura) disponibile per 73 lingue,](./language-support.md#optical-character-recognition-ocr) tra cui cinese semplificato e tradizionale, giapponese, coreano e latino.
* [OCR (Lettura)](./overview-ocr.md) disponibile anche come [contenitore senza distribuzione](./computer-vision-how-to-install-containers.md?tabs=version-3-2) per la distribuzione locale.

> [!div class="nextstepaction"]
> [Vedere Visione artificiale versione 3.2 ga](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>Marzo 2021

### <a name="computer-vision-32-public-preview-update"></a>Visione artificiale 3.2 Anteprima pubblica

L Visione artificiale appaltatura pubblica dell'API v3.2 è stata aggiornata. La versione di anteprima include tutte Visione artificiale funzionalità e le API di lettura e analisi aggiornate.

> [!div class="nextstepaction"]
> [Vedere Visione artificiale 3.2 anteprima pubblica 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Febbraio 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Leggere l'anteprima pubblica dell'API v3.2 con supporto OCR per 73 lingue
Visione artificiale'anteprima pubblica dell'API di lettura v3.2, disponibile come servizio cloud e contenitore Docker, include questi aggiornamenti:
* [OCR per 73 lingue, tra](./language-support.md#optical-character-recognition-ocr) cui cinese semplificato e tradizionale, giapponese, coreano e latino.
* Ordine di lettura naturale per l'output della riga di testo (solo lingue latini)
* Classificazione dello stile di scrittura manuale per le righe di testo insieme a un punteggio di attendibilità (solo lingue latini).
* Estrarre il testo solo per le pagine selezionate per un documento a più pagine.
* Disponibile come [contenitore distroless](./computer-vision-how-to-install-containers.md?tabs=version-3-2) per la distribuzione locale.

Per altre [informazioni, vedere la guida alla procedura per](Vision-API-How-to-Topics/call-read-api.md) leggere l'API.

> [!div class="nextstepaction"]
> [Usare l'API di lettura v3.2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Gennaio 2021

### <a name="spatial-analysis-container-update"></a>Aggiornamento del contenitore analisi spaziale

È stata rilasciata una nuova versione [del contenitore Analisi](spatial-analysis-container.md) spaziale con un nuovo set di funzionalità. Questo contenitore Docker consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone e i loro movimenti negli ambienti fisici. 

* [Le operazioni di analisi](spatial-analysis-operations.md) spaziale possono ora essere configurate per rilevare se una persona indossa un viso di protezione, ad esempio una maschera. 
    * È possibile abilitare un classificatore di mascherine per le operazioni `personcount`, `personcrossingline` e `personcrossingpolygon` configurando il parametro `ENABLE_FACE_MASK_CLASSIFIER`.
    * Gli attributi `face_mask` e `face_noMask` verranno restituiti come metadati con un punteggio di confidenza per ogni persona rilevata nel flusso video
* *L'operazione personcrossingpolygon* è stata estesa per consentire il calcolo del tempo trascorso da una persona in una zona. È possibile impostare il parametro nella configurazione della zona per l'operazione su e un nuovo evento di tipo `type` `zonedwelltime` *personZoneDwellTimeEvent* includerà il campo popolato con il numero di millisecondi trascorsi dalla persona nella `durationMs` zona.
* **Modifica di rilievo:** *l'evento personZoneEvent* è stato rinominato *in personZoneEnterExitEvent*. Questo evento viene generato dall'operazione *personcrossingpolygon* quando una persona entra o esce dalla zona e fornisce informazioni direzionali con il lato numerato della zona attraversata.
* L'URL video può essere specificato come "Parametro privato/offuscato" in tutte le operazioni. L'offuscamento è ora facoltativo e funzionerà solo se `KEY` e vengono forniti come variabili di `IV` ambiente.
* La calibrazione è abilitata per impostazione predefinita per tutte le operazioni. Impostare `do_calibration: false` l'oggetto per disabilitarlo.
* Aggiunta del supporto per la ricalibrazione automatica (per impostazione predefinita disabilitata) tramite il parametro , vedere Operazioni di analisi `enable_recalibration` [spaziale](./spatial-analysis-operations.md) per informazioni dettagliate
* Parametri di calibrazione della fotocamera per `DETECTOR_NODE_CONFIG` . Per informazioni [dettagliate, vedere Operazioni di analisi](./spatial-analysis-operations.md) spaziale.


## <a name="october-2020"></a>Ottobre 2020

### <a name="computer-vision-api-v31-ga"></a>Disponibilità generale dell'API Visione artificiale v3.1

L'API Visione artificiale disponibile a livello generale è stato aggiornata alla versione 3.1.

## <a name="september-2020"></a>Settembre 2020

### <a name="spatial-analysis-container-preview"></a>Anteprima del contenitore Analisi spaziale

Il [contenitore Analisi spaziale](spatial-analysis-container.md) è ora disponibile in anteprima. La funzionalità analisi spaziale di Visione artificiale consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone e il loro movimento negli ambienti fisici. Analisi spaziale è un contenitore Docker che è possibile usare in locale. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Aggiunta del riconoscimento ottico dei caratteri per il giapponese nell'API di lettura v3.1 (anteprima pubblica)
Nell'API di lettura v3.1 (anteprima pubblica) di Visione artificiale sono state aggiunte queste funzionalità:
* Riconoscimento ottico dei caratteri (OCR) per la lingua giapponese
* Per ogni riga di testo, viene indicato se l'aspetto è in stile Scrittura manuale o Stampa, insieme a un punteggio di attendibilità (solo lingue latine).
* Per un documento di più pagine è possibile estrarre il testo solo per le pagine o l'intervallo di pagine selezionati.

* Questa versione di anteprima dell'API di lettura supporta le lingue inglese, olandese, francese, tedesco, italiano, giapponese, portoghese, cinese semplificato e spagnolo.

Per altre [informazioni, vedere la guida alle informazioni sulle API](Vision-API-How-to-Topics/call-read-api.md) di lettura.

> [!div class="nextstepaction"]
> [Altre informazioni sull'API di lettura v3.1 (anteprima pubblica 2)](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Luglio 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Aggiunta del riconoscimento ottico dei caratteri per il cinese semplificato nell'API di lettura v3.1 (anteprima pubblica)
Nell'API di lettura v3.1 (anteprima pubblica) di Visione artificiale è stato aggiunto il supporto per il cinese semplificato.

* Questa versione di anteprima dell'API di lettura supporta le lingue inglese, olandese, francese, tedesco, italiano, portoghese, cinese semplificato e spagnolo.

Per altre [informazioni, vedere la guida alle informazioni sulle API](Vision-API-How-to-Topics/call-read-api.md) di lettura.

> [!div class="nextstepaction"]
> [Altre informazioni sull'API di lettura v3.1 (anteprima pubblica 1)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maggio 2020
Visione artificiale'API v3.0 ha immesso disponibilità generale, con aggiornamenti per l'API di lettura:

* supporto per inglese, olandese, francese, tedesco, italiano, portoghese e spagnolo
* Miglioramento della precisione
* Punteggio di attendibilità per ogni parola estratta
* Nuovo formato di output

Per altre [informazioni, vedere la panoramica di OCR.](overview-ocr.md)

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
