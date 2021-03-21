---
title: Servizio Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Integrare Traduttore in applicazioni, siti Web, strumenti e altre soluzioni per offrire esperienze utente multilingue.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: traduttore, traduzione testuale, traduzione automatica, servizio di traduzione
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657723"
---
# <a name="what-is-the-translator-service"></a>Che cos'è il servizio Translator?

Translator è un servizio di traduzione automatica basato sul cloud e fa parte della famiglia di API cognitive di [Servizi cognitivi di Azure](../../index.yml?panel=ai&pivot=products) usate per lo sviluppo di app intelligenti. Traduttore è facilmente integrabile in applicazioni, siti Web, strumenti e soluzioni. Consente di aggiungere esperienze utente multilingue in [lingue e dialetti 90](./language-support.md) e può essere usato per la traduzione di testo con qualsiasi sistema operativo.

Questa documentazione contiene i tipi di articolo seguenti:  

* Nelle [**guide introduttive**](quickstart-translator.md) vengono fornite istruzioni introduttive per l'esecuzione di richieste al servizio.  
* Le [**guide alle procedure**](translator-how-to-signup.md) contengono istruzioni per l'uso del servizio in modi più specifici o personalizzati.  
* I [**concetti**](character-counts.md) forniscono spiegazioni approfondite delle funzionalità e delle funzionalità del servizio.  
* Le [**esercitazioni**](tutorial-wpf-translation-csharp.md) sono guide più lunghe che illustrano come usare il servizio come componente in soluzioni aziendali più ampie.  


## <a name="about-microsoft-translator"></a>Informazioni su Microsoft Translator

Translator fornisce molti prodotti e servizi Microsoft e viene usato da migliaia di aziende in tutto il mondo nelle proprie applicazioni e flussi di lavoro.

È anche disponibile il servizio di traduzione vocale, basato su Translator, tramite i [servizi Voce di Azure](../speech-service/index.yml). Combina le funzionalità dell'API Traduzione vocale e del Servizio riconoscimento vocale personalizzato (anteprima) in un unico servizio completamente personalizzabile. 

## <a name="language-support"></a>Lingue supportate

Translator fornisce supporto multilingue per la traduzione testuale, la traslitterazione, il rilevamento delle lingue e i dizionari. Per un elenco completo, vedere il [supporto per le lingue](language-support.md) o accedere all'elenco a livello di codice con l'[API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Traduzione automatica neurale di Microsoft Translator

La traduzione automatica neurale è il nuovo standard per traduzioni automatiche di qualità elevata basate sull'intelligenza artificiale. Sostituisce la tecnologia di traduzione automatica statistica legacy che ha raggiunto uno stato stazionario di qualità a metà del 2010.

La traduzione automatica neurale fornisce traduzioni migliori rispetto alla traduzione automatica statistica non solo dal punto di vista della qualità della traduzione non elaborata, ma anche perché suonano più fluide e umane. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole. La traduzione automatica statistica accetta solo il contesto immediato di alcune parole prima e dopo ogni parola.

I modelli della traduzione automatica neurale sono alla base dell'API e non sono visibili agli utenti finali. L'unica differenza più evidente è la migliore qualità della traduzione, specialmente per lingue quali il cinese, il giapponese e l'arabo.

Altre informazioni sul [funzionamento della traduzione automatica neurale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Migliorare le traduzioni con il traduttore personalizzato

 Il [convertitore personalizzato](customization.md), un'estensione del servizio di conversione, può essere usato per personalizzare il sistema di conversione neurale e migliorare la traduzione per la terminologia e lo stile specifici.

Con il traduttore personalizzato è possibile creare sistemi di traduzione per gestire la terminologia usata nell'azienda o nel settore. Il sistema di traduzione personalizzato può integrarsi facilmente con le applicazioni, i flussi di lavoro, i siti Web e i dispositivi esistenti, tramite il normale traduttore, usando il parametro category.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un servizio di conversione](./translator-how-to-signup.md) per ottenere le chiavi di accesso e l'endpoint.
- Per chiamare rapidamente il servizio Translator, provare l'[avvio rapido](quickstart-translator.md).
- La [documentazione di riferimento delle API](./reference/v3-0-reference.md) offre la documentazione tecnica per le API.
- [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
