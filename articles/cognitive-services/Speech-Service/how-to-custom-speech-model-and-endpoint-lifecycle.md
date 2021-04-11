---
title: Ciclo di vita del modello e dell'endpoint del servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato fornisce modelli di base per l'adattamento e consente di creare modelli personalizzati dai dati. Questo articolo descrive le sequenze temporali per i modelli e per gli endpoint che usano tali modelli.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385026"
---
# <a name="model-and-endpoint-lifecycle"></a>Ciclo di vita di modelli ed endpoint

La nostra sintesi vocale standard (non personalizzata) si basa su modelli di intelligenza artificiale che chiamiamo modelli di base. Nella maggior parte dei casi, viene trainato un modello di base diverso per ogni lingua parlata supportata.  Il servizio di riconoscimento vocale viene aggiornato con nuovi modelli di base a intervalli di pochi mesi per migliorare l'accuratezza e la qualità.  
Con Riconoscimento vocale personalizzato, i modelli personalizzati vengono creati adattando un modello di base scelto con i dati dello scenario specifico del cliente. Una volta creato un modello personalizzato, tale modello non verrà aggiornato o modificato, anche se il modello di base corrispondente da cui è stato adattato viene aggiornato nel servizio di riconoscimento vocale standard.  
Questo criterio consente di usare un modello personalizzato specifico per un lungo periodo di tempo dopo avere un modello personalizzato che soddisfi le proprie esigenze.  Tuttavia, è consigliabile ricreare periodicamente il modello personalizzato in modo che sia possibile adattarsi al modello di base più recente per sfruttare i vantaggi offerti dall'accuratezza e dalla qualità migliorate.

Altri termini chiave correlati al ciclo di vita del modello includono:

* **Adattamento**: acquisizione di un modello di base e personalizzazione del proprio dominio/scenario tramite dati di testo e/o dati audio.
* **Decodifica**: uso di un modello ed esecuzione del riconoscimento vocale (decodifica dell'audio nel testo).
* **Endpoint**: distribuzione specifica dell'utente di un modello di base o di un modello personalizzato accessibile *solo* a un determinato utente.

### <a name="expiration-timeline"></a>Sequenza temporale scadenza

Poiché nuovi modelli e nuove funzionalità diventano disponibili e meno recenti, i modelli meno accurati vengono ritirati, vedere le sequenze temporali seguenti per la scadenza del modello e dell'endpoint:

**Modelli di base** 

* Adattamento: disponibile per un anno. Una volta importato, il modello è disponibile per un anno per la creazione di modelli personalizzati. Dopo un anno, è necessario creare nuovi modelli personalizzati da una versione più recente del modello di base.  
* Decodifica: disponibile per due anni dopo l'importazione. Quindi, è possibile creare un endpoint e usare la trascrizione batch per due anni con questo modello. 
* Endpoint: disponibile nella stessa sequenza temporale della decodifica.

**Modelli personalizzati**

* Decodifica: disponibile per due anni dopo la creazione del modello. È quindi possibile usare il modello personalizzato per due anni (batch/tempo reale/test) dopo che è stato creato. Dopo due anni, *è necessario* ripetere il training del modello perché il modello di base sarà in genere deprecato per l'adattamento.  
* Endpoint: disponibile nella stessa sequenza temporale della decodifica.

Quando un modello di base o un modello personalizzato scade, viene sempre eseguito il fallback alla *versione più recente del modello di base*. Quindi, l'implementazione non verrà mai interrotta, ma potrebbe diventare meno accurata per *i dati specifici* se i modelli personalizzati raggiungono la scadenza. È possibile visualizzare la scadenza per un modello nelle posizioni seguenti nell'area Riconoscimento vocale personalizzato di speech studio:

* Riepilogo del training del modello
* Dettagli di training del modello
* Riepilogo della distribuzione
* Dettagli distribuzione

Di seguito è riportato un esempio di formato del riepilogo di training del modello:

![Riepilogo di training ](media/custom-speech/custom-speech-model-training-with-expiry.png) del modello e anche dalla pagina dei dettagli di training del modello:

![Dettagli di training del modello](media/custom-speech/custom-speech-model-details-with-expiry.png)

È anche possibile controllare le date di scadenza tramite [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) le [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) API di riconoscimento vocale e personalizzate sotto la `deprecationDates` proprietà nella risposta JSON.

Di seguito è riportato un esempio dei dati di scadenza della chiamata API GetModel. Il **DEPRECATIONDATES** Mostra la scadenza del modello: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Si noti che è possibile aggiornare il modello in un endpoint di riconoscimento vocale personalizzato senza tempi di inattività cambiando il modello usato dall'endpoint nella sezione distribuzione di speech studio o tramite l'API riconoscimento vocale personalizzato.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Cosa accade quando i modelli scadono e come aggiornarli
Che cosa accade quando un modello scade e come aggiornare il modello dipende dal modo in cui viene usato.
### <a name="batch-transcription"></a>Trascrizione Batch
Se un modello scade e viene usato con la trascrizione della trascrizione [batch](batch-transcription.md) , avrà esito negativo con un errore 4xx. Per evitare che questo aggiorni il `model` parametro nel codice JSON inviato nel corpo della richiesta **create trascrizione** per puntare a un modello di base più recente o a un modello personalizzato più recente. È anche possibile rimuovere la `model` voce da JSON per usare sempre il modello di base più recente.
### <a name="custom-speech-endpoint"></a>Endpoint riconoscimento vocale personalizzato
Se un modello scade usato da un [endpoint vocale personalizzato](how-to-custom-speech-train-model.md), il servizio eseguirà automaticamente il fallback all'uso del modello di base più recente per la lingua in uso. , si sta usando è possibile selezionare **distribuzione** nel menu **riconoscimento vocale personalizzato** nella parte superiore della pagina e quindi fare clic sul nome dell'endpoint per visualizzarne i dettagli. Nella parte superiore della pagina dei dettagli verrà visualizzato un pulsante **Aggiorna modello** che consente di aggiornare facilmente il modello usato da questo endpoint senza tempi di inattività. È anche possibile apportare questa modifica a livello di codice usando l'API REST del [**modello di aggiornamento**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) .

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello e distribuirlo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)