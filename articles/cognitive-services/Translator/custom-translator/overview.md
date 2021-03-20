---
title: Informazioni su Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator offre funzionalità simili a quelle offerte da Microsoft Translator Hub per la traduzione automatica statistica (SMT, Statistical Machine Translation), ma in esclusiva per i sistemi di traduzione automatica neurale (NMT, Neural Machine Translation).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 89ab25f45cd35a86d2ff4416ceb851ae6cd5e115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657162"
---
# <a name="what-is-custom-translator"></a>Informazioni su Custom Translator

[Custom Translator](https://portal.customtranslator.azure.ai) è una funzionalità del servizio Microsoft Translator, che consente alle agenzie di traduzione, agli sviluppatori di app e ai provider di servizi linguistici di creare sistemi di traduzione automatica neurale (NMT). I sistemi di traduzione personalizzata si integrano senza problemi nelle applicazioni, nei flussi di lavoro e nei siti Web esistenti.

I sistemi di traduzione basati su [Custom Translator](https://portal.customtranslator.azure.ai) sono disponibili tramite la stessa, [sicura](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)[API Traduzione testuale V3](../reference/v3-0-translate.md?tabs=curl) di Microsoft Translator basata sul cloud e a prestazioni e scalabilità elevate che supporta miliardi di traduzioni ogni giorno.

Custom Translator supporta più di tre dozzine di lingue ed include il mapping diretto alle lingue disponibili per NMT. Per un elenco completo, vedere [Lingue di Microsoft Translator](../language-support.md#customization).

Questa documentazione contiene i tipi di articolo seguenti:

* Nelle [**guide introduttive**](quickstart-build-deploy-custom-model.md) vengono fornite istruzioni introduttive per l'esecuzione di richieste al servizio.  
* Le [**guide alle procedure**](how-to-create-project.md) contengono istruzioni per l'uso della funzionalità in modi più specifici o personalizzati.  
* I [**concetti**](workspace-and-project.md) forniscono spiegazioni approfondite delle funzionalità della funzionalità.  


## <a name="features"></a>Funzionalità

Il convertitore personalizzato fornisce funzionalità diverse per la creazione di un sistema di traduzione personalizzato e l'accesso successivo.

|Funzionalità  |Descrizione  |
|---------|---------|
|[Applicare la tecnologia di traduzione automatica neurale](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Migliorare la traduzione applicando la traduzione di Neural Machine (NMT) fornita dal convertitore personalizzato.       |
|[Creazione di sistemi in grado di riconoscere la terminologia aziendale](what-are-parallel-documents.md)     |  È possibile personalizzare e creare sistemi di traduzione con documenti paralleli, in grado di comprendere le terminologie usate in un'azienda e un settore specifico.       |
|[Uso di un dizionario per la creazione di modelli](what-is-dictionary.md)     |   Se non è disponibile alcun training set, è possibile eseguire il training di un modello usando solo i dati di un dizionario.       |
|[Collaborazione con altri utenti](how-to-manage-settings.md#share-your-workspace)     |   È possibile collaborare con il proprio team condividendo il proprio lavoro con diverse persone.     |
|[Accesso ai modelli di traduzione personalizzati](../reference/v3-0-translate.md?tabs=curl)     |  Le applicazioni o i programmi esistenti possono accedere ai modelli di traduzione personalizzati in qualsiasi momento tramite l'API Traduzione testuale di Microsoft Translator V3.       |

## <a name="get-better-translations"></a>Traduzioni migliori

Microsoft Translator ha rilasciato [Traduzione automatica neurale (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) nel 2016. NMT ha offerto progressi significativi a livello di qualità della traduzione rispetto alla tecnologia di [traduzione automatica statistica (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) standard di settore. Poiché NMT acquisisce meglio il contesto delle frasi complete prima di tradurle, offre traduzioni di qualità superiore, più vicine al linguaggio naturale e più scorrevoli. [Custom Translator](https://portal.customtranslator.azure.ai) offre NMT per i modelli personalizzati, per consentire di ottenere traduzioni di qualità migliore.

È possibile usare documenti tradotti in precedenza per creare un sistema di traduzione. Questi documenti includono terminologia e stile specifici del dominio, migliori rispetto a un sistema di traduzione standard. Gli utenti possono caricare documenti ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

Custom Translator accetta anche dati paralleli a livello di documento, per rendere più efficaci la raccolta e la preparazione dei dati. Se gli utenti hanno accesso alle versioni dello stesso contenuto in più lingue, ma in documenti separati, Custom Translator sarà in grado di associare automaticamente frasi tra documenti.

Se vengono forniti quantità e tipi di dati appropriati per il training, non è raro riscontrare miglioramenti compresi tra 5 e 10 punti sul [punteggio BLEU](what-is-bleu-score.md) con Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Produttività e convenienza

Con [Custom Translator](https://portal.customtranslator.azure.ai) il training e la distribuzione di un sistema personalizzato non richiedono alcuna competenza di programmazione.

Usando il portale sicuro di [Custom Translator](https://portal.customtranslator.azure.ai) gli utenti possono caricare dati di training, eseguire il training dei sistemi, testare i sistemi e distribuirli in un ambiente di produzione tramite un'interfaccia utente intuitiva. Il sistema sarà quindi disponibile per l'uso su larga scala entro poche ore. Il tempo effettivo dipende dalle dimensioni dei dati di training.

È anche possibile accedere a [Custom Translator](https://portal.customtranslator.azure.ai) a livello di codice tramite un'[API dedicata](https://custom-api.cognitive.microsofttranslator.com/swagger/), attualmente disponibile in anteprima. L'API consente agli utenti di gestire la creazione o l'aggiornamento del training tramite la propria app o WebService.

Il costo per l'uso di un modello personalizzato per la traduzione di contenuto è basato sul piano tariffario per l'API Traduzione testuale dell'utente. Per informazioni dettagliate sul piano tariffario, vedere la [pagina Web dei prezzi dell'API Traduzione testuale](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) di Servizi cognitivi.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzione sicura in qualsiasi momento, ovunque, in tutte le app e i servizi

È possibile accedere ai sistemi personalizzati e integrarli senza problemi in qualsiasi prodotto o flusso di lavoro aziendale e in qualsiasi dispositivo tramite la tecnologia REST standard dell'API Traduzione testuale di Microsoft Translator.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dettagli prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Per informazioni sulla creazione di un modello di traduzione in Custom Translator, vedere [Avvio rapido](quickstart-build-deploy-custom-model.md).
