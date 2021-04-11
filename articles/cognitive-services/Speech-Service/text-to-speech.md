---
title: Panoramica di sintesi vocale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di sintesi vocale nel servizio di riconoscimento vocale consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale naturale, ad esempio. Questo articolo offre una panoramica dei vantaggi e delle funzionalità del servizio di sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: sintesi vocale
ms.openlocfilehash: 73e37fde4b3c2dd1aeb6ab171c3726f1b4353949
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106089"
---
# <a name="what-is-text-to-speech"></a>Che cos'è la sintesi vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In questa panoramica vengono illustrati i vantaggi e le funzionalità del servizio di sintesi vocale, che consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale. Usare le voci neurali simili a quelle umane oppure creare una voce personalizzata univoca per il prodotto o il marchio. Per un elenco completo delle voci, dei linguaggi e delle impostazioni locali supportate, vedere [linguaggi supportati](language-support.md#text-to-speech).

Questa documentazione contiene i tipi di articolo seguenti:

* Nelle **guide introduttive** vengono fornite istruzioni introduttive per l'esecuzione di richieste al servizio.
* Le **guide alle procedure** contengono istruzioni per l'uso del servizio in modi più specifici o personalizzati.
* I **concetti** forniscono spiegazioni approfondite delle funzionalità e delle funzionalità del servizio.
* Le **esercitazioni** sono guide più lunghe che illustrano come usare il servizio come componente in soluzioni aziendali più ampie.

> [!NOTE]
> Il riconoscimento vocale Bing è stato ritirato il 15 ottobre 2019. Se le applicazioni, gli strumenti o i prodotti usano le API riconoscimento vocale Bing o Riconoscimento vocale personalizzato, sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.
> - [Eseguire la migrazione dal riconoscimento vocale Bing al servizio riconoscimento vocale](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Funzionalità di base

* Sintesi vocale: usare l' [SDK di riconoscimento vocale](./get-started-text-to-speech.md) o l' [API REST](rest-text-to-speech.md) per convertire la sintesi vocale usando le voci standard, Neural o Custom.

* Sintesi asincrona dell'audio lungo: usare l' [API Long audio](long-audio-api.md) per sintetizzare in modo asincrono i file da sintesi vocale più di 10 minuti (ad esempio, libri audio o lezioni). Diversamente dalla sintesi eseguita con l'SDK per il riconoscimento vocale o con l'API REST di sintesi vocale, le risposte non vengono restituite in tempo reale. Si prevede che le richieste vengano inviate in modo asincrono, viene eseguito il polling delle risposte e che l'audio sintetizzato viene scaricato quando reso disponibile dal servizio. Sono supportate solo le voci neurali personalizzate.

* Voci neurali: le reti neurali profonde vengono usate per superare i limiti della sintesi vocale tradizionale per quanto riguarda lo stress e l'intonazione nel linguaggio parlato. Le stime e la sintesi vocale di prosodia vengono eseguite simultaneamente e ciò comporta output più fluidi e con suono naturale. Le voci neurali possono essere usate per rendere le interazioni con chatbot e gli assistenti vocali più naturali e accattivanti, convertire testi digitali come e-book in Audiolibri e migliorare i sistemi di navigazione in auto. Con la prosodia naturale e una chiara articolazione di parole, le voci neurali riducono significativamente la fatica in ascolto quando si interagisce con i sistemi di intelligenza artificiale. Per un elenco completo delle voci neurali, vedere [linguaggi supportati](language-support.md#text-to-speech).

* Modificare gli stili di pronuncia con SSML-Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML usato per personalizzare gli output di sintesi vocale. Con SSML è possibile modificare il pitch, aggiungere pause, migliorare la pronuncia, accelerare o rallentare la velocità di pronuncia, aumentare o diminuire il volume e attribuire più voci a un singolo documento. Vedere le [procedure](speech-synthesis-markup.md) per la regolazione degli stili di pronuncia.

* Visemes- [visemes](how-to-speech-synthesis-viseme.md) è la chiave che si pone in un discorso osservato, inclusa la posizione dei labbri, la mascella e la lingua durante la produzione di un fonema particolare. Visemes hanno una forte correlazione con voci e fonemi. Usando gli eventi viseme nell'SDK vocale, è possibile generare dati di animazione facciali, che possono essere usati per animare i visi nelle comunicazioni di lettura del labbro, istruzione, intrattenimento e servizio clienti.

> [!NOTE]
> Gli eventi viseme sono attualmente supportati solo per la `en-US-AriaNeural` voce.

## <a name="get-started"></a>Introduzione

Per iniziare a usare sintesi vocale, vedere la [Guida introduttiva](get-started-text-to-speech.md) . Il servizio di sintesi vocale è disponibile tramite l' [SDK vocale](speech-sdk.md), l' [API REST](rest-text-to-speech.md)e l'interfaccia della riga di comando [vocale](spx-overview.md)

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per sintesi vocale è disponibile su GitHub. Questi esempi coprono la conversione da sintesi vocale nei linguaggi di programmazione più diffusi.

- [Esempi di sintesi vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="customization"></a>Personalizzazione

Oltre alle voci neurali, è possibile creare e ottimizzare voci personalizzate specifiche per il prodotto o il marchio. Per iniziare sono disponibili solo alcuni file audio e le trascrizioni associate. Per altre informazioni, vedere [Introduzione alla voce personalizzata](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota sui prezzi

Quando si usa il servizio di sintesi vocale, viene addebitato ogni carattere convertito in sintesi vocale, inclusa la punteggiatura. Anche se il documento SSML non è fatturabile, gli elementi facoltativi utilizzati per regolare la conversione del testo in sintesi vocale, ad esempio fonemi e pitch, vengono conteggiati come caratteri fatturabili. Ecco un elenco degli elementi fatturabili:

- Testo passato al servizio di sintesi vocale nel corpo SSML della richiesta
- Tutti i markup all'interno del campo di testo del corpo della richiesta nel formato SSML, ad eccezione dei `<speak>` `<voice>` tag e
- Lettere, punteggiatura, spazi, tabulazioni, markup e tutti gli spazi vuoti
- Ogni elemento di codice definito in Unicode

Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ogni carattere di lingua cinese, giapponese e coreano viene conteggiato come due caratteri per la fatturazione.

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk.md)
- [API REST: Sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

- [Ricevi una sottoscrizione gratuita per il servizio riconoscimento vocale](overview.md#try-the-speech-service-for-free)
- [Ottenere Speech SDK](speech-sdk.md)
