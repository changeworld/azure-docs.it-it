---
title: Panoramica della voce neurale personalizzata-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La voce neurale personalizzata è una funzionalità di sintesi vocale che consente di creare una voce sintetica personalizzata per le applicazioni, fornendo i propri dati audio come esempio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713100"
---
# <a name="what-is-custom-neural-voice"></a>Che cos'è la voce neurale personalizzata?

La voce neurale personalizzata è una funzionalità di sintesi [vocale](./text-to-speech.md) che consente di creare una voce sintetica personalizzata personalizzata per le applicazioni, fornendo i propri dati audio come esempio di esempio. Il testo da sintesi vocale converte il testo in sintesi vocale usando un modello di apprendimento automatico che suona come una voce scelta. Con l' [API REST](./rest-text-to-speech.md), è possibile abilitare le app in modo che parlino con [voci predefinite](./language-support.md#neural-voices) o con modelli [vocali personalizzati](./how-to-custom-voice-prepare-data.md) sviluppati tramite la funzionalità per la voce neurale personalizzata. La voce neurale personalizzata è basata sulla tecnologia di TTS neurale che crea una voce naturale che è spesso indistinguibile rispetto a una voce umana.
La voce realistica e naturale della voce neurale personalizzata può rappresentare marchi, impersonificare computer e consentire agli utenti di interagire con le applicazioni in modo naturale.

> [!NOTE]
> La funzionalità per la voce neurale personalizzata richiede la registrazione e l'accesso è limitata in base ai criteri di idoneità e utilizzo di Microsoft. I clienti che desiderano utilizzare questa funzionalità sono tenuti a registrare i propri casi di utilizzo tramite il [modulo di immissione](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Nozioni di base sulla voce neurale personalizzata

La tecnologia di TTS neurale sottostante usata per la voce neurale personalizzata è costituita da tre componenti principali: analizzatore di testo, modello acustico neurale e vocoder neurale. Per generare voce sintetica naturale da testo, il testo viene innanzitutto inserito in analizzatore di testo, che fornisce l'output sotto forma di sequenza fonema. Un fonema è un'unità di base del suono che distingue una parola da un'altra in una determinata lingua. Una sequenza di fonemi definisce le pronunce delle parole fornite nel testo. 

Successivamente, la sequenza fonema passa al modello di analisi neurale per prevedere le funzionalità acustiche che definiscono i segnali vocali, ad esempio il timbro, lo stile, la velocità, le intonazioni e i modelli di stress. Infine, il vocoder neurale converte le funzionalità acustiche in onde acustiche in modo da generare la voce sintetica.

![Immagine introduttiva per la voce neurale personalizzata.](./media/custom-voice/cnv-intro.png)

I modelli vocali neurali TTS vengono sottoposti a training usando reti neurali profonde basate sugli esempi di registrazione delle voci umane. In questo [Blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)viene descritto il funzionamento del linguaggio TTS neurale con i modelli di sintesi vocale neurale all'avanguardia. Nel Blog viene inoltre illustrato come un modello di base universale può essere adattato con meno di 2 ore di dati vocali (o meno di 2.000 espressioni registrate) da un altoparlante di destinazione e apprendere come parlare della voce dell'oratore di destinazione. Per informazioni su come viene eseguito il training di un vocoder neurale, vedere il [post di Blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Con la funzionalità di personalizzazione di una voce neurale personalizzata, è possibile adattare il motore TTS Neural per adattarlo meglio agli scenari utente. Per creare una voce neurale personalizzata, utilizzare [speech studio](https://speech.microsoft.com/customvoice) per caricare l'audio registrato e gli script corrispondenti, eseguire il training del modello e distribuire la voce in un endpoint personalizzato. A seconda del caso d'uso, è possibile usare la voce neurale personalizzata per convertire il testo in linguaggio vocale in tempo reale (ad esempio, usato in un assistente virtuale intelligente) o generare contenuto audio offline (ad esempio, usato come in un libro audio o istruzioni nelle applicazioni di e-learning) con l'input di testo fornito dall'utente. Questa operazione viene resa disponibile tramite l' [API REST](./rest-text-to-speech.md), l' [SDK vocale](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)o un [portale Web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termini e definizioni

| **Termine**      | **Definizione**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modello vocale   | Modello di sintesi vocale che può simulare le caratteristiche vocali univoche di un altoparlante di destinazione. Un *modello vocale* è noto anche come *carattere vocale* o *voce sintetica*. Un modello vocale è un set di parametri in formato binario che non è leggibile e non contiene registrazioni audio. Non può essere decodificato per derivare o costruire l'audio di una voce umana. |
| Speaker  | Utenti o speaker di destinazione le cui voci vengono registrate e usate per creare modelli vocali destinati a sembrare la voce del talento vocale.                                                                                                                                                                                                                                                   |
| TTS standard  | Il metodo standard, o "tradizionale", che suddivide la lingua parlata in frammenti fonetici in modo che possano essere ricombinati e abbinati usando la programmazione classica o i metodi statistici.                                                                                                                                                                                                    |
| TTS neurale    | Il linguaggio TTS neurale sintetizza il riconoscimento vocale usando reti neurali profonde che hanno "appreso" il modo in cui le fonetiche vengono combinate in sintesi umana naturale, anziché usare la programmazione procedurale o i metodi statistici. Oltre alle registrazioni di un talento vocale di destinazione, Neural TTS usa una libreria di origine o un modello di base compilato con registrazioni vocali da molti altoparlanti diversi.          |
| Dati di training | Set di dati di training per la voce neurale personalizzato che include le registrazioni audio del talento vocale e le trascrizioni del testo associate.                                                                                                                                                                                                                                                               |
| Utente tipo       | Un utente descrive chi deve essere questa voce. Una progettazione di tipo persona efficace informa tutte le creazioni vocali, sia che si tratti di scegliere un modello di voce disponibile già creato, che a partire da zero mediante il cast e la registrazione di un nuovo talento vocale.                                                                                                |
| Script        | Uno script è un file di testo che contiene le espressioni che devono essere pronunciate dal talento vocale. Il termine "*espressioni*" comprende sia frasi complete che frasi più brevi.                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Uso responsabile dell'intelligenza artificiale

Per informazioni su come usare responsabilmente la voce neurale personalizzata, vedere la [Nota sulla trasparenza](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Le note sulla trasparenza di Microsoft sono destinate a comprendere il funzionamento della tecnologia di intelligenza artificiale, le scelte che i proprietari del sistema possono influenzare sulle prestazioni e sul comportamento del sistema e sull'importanza di considerare l'intero sistema, tra cui la tecnologia, le persone e l'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Voce personalizzata](how-to-custom-voice.md)
* [Creare e usare un endpoint Voice personalizzato](how-to-custom-voice-create-voice.md)