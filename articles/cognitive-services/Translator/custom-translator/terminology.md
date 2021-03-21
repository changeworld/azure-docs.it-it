---
title: Terminologia-convertitore personalizzato
titleSuffix: Azure Cognitive Services
description: Elenco dei termini utilizzati negli articoli del traduttore personalizzato.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657823"
---
# <a name="custom-translator-terminology"></a>Terminologia per i traduttori personalizzati

La tabella seguente presenta un elenco di termini che è possibile trovare quando si lavora con il [traduttore personalizzato](https://portal.customtranslator.azure.ai).

| Termine o locuzione|Definizione|
|------------------|-----------|
| Lingua di origine | La lingua di origine è la lingua iniziale che si desidera convertire in un'altra lingua ("destinazione").|
| Lingua di destinazione| La lingua di destinazione è quella che si vuole che restituisca il sistema di traduzione automatica dopo che ha ricevuto la lingua di origine. |
| File monolingue | Un file monolingue ha una sola lingua non associata a un altro file di una lingua diversa. |
| File paralleli | Un file parallelo è una combinazione dei due file con testo corrispondente. Uno dei file contiene la lingua di origine e l'altro contiene la lingua di destinazione.|
| Allineamento di frasi| Un set di dati paralleli deve includere frasi allineate che rappresentano lo stesso testo in due lingue. Ad esempio, in un file parallelo di origine la prima frase deve corrispondere teoricamente alla prima frase nel file parallelo di destinazione.|
| Testo allineato | Uno dei passaggi più importanti della convalida dei file è l'allineamento delle frasi nei documenti paralleli. I concetti vengono espressi in modo diverso in diverse lingue. Le varie lingue presentano inoltre un diverso ordine delle parole. Questo procedura esegue l'allineamento delle frasi con lo stesso contenuto in modo che possano essere utilizzate per il training. Un basso livello di allineamento delle frasi indica che potrebbe essersi verificato un problema con uno o con entrambi i file. |
| Separazione/Annullamento della separazione delle parole | La separazione delle parole è la funzione di contrassegnare i limiti tra le parole. Molti sistemi di scrittura usano uno spazio per indicare tale limite. L'annullamento della separazione delle parole indica la rimozione di qualsiasi marcatore visibile che sia stato inserito tra le parole in un passaggio precedente. |
| Delimitatori   | I delimitatori sono modi per suddividere una frase in segmenti o elementi che delimitano il margine tra due frasi. Ad esempio, in inglese le parole sono delimitate da spazi, le frasi dai due punti e dal punto e virgola e i periodi dal punto. |
| File di training | Un file di training viene usato per insegnare al sistema di traduzione automatica a eseguire il mapping da una lingua (origine) a un'altra lingua (destinazione). Maggiore è il numero di dati forniti, migliori saranno le prestazioni del sistema. |
| File di ottimizzazione | Questi file vengono spesso derivati in modo casuale dal set di training (se non si seleziona un set di ottimizzazione). Le frasi vengono selezionate e usate per ottimizzare il sistema e verificare che funzioni correttamente. Se si vuole creare un modello di traduzione per utilizzo generico e creare i propri file di ottimizzazione, assicurarsi che siano un set casuale di frasi tra domini |
| File di test| Si tratta spesso di file derivati, selezionati in modo casuale dal set di training (se non si seleziona alcun set di test). Lo scopo di queste frasi è quello di valutare l'accuratezza del modello di traduzione. Poiché queste frasi sono quelle che si desidera assicurarsi che il sistema si converta in modo accurato, è possibile creare un set di testing e caricarlo nel convertitore. In questo modo si assicurerà che queste frasi vengano usate nella valutazione del sistema (la generazione di un punteggio BLEU).   |
| File combinato   | È un tipo di file in cui le frasi di origine e quelle tradotte sono incluse nello stesso file. Formati di file supportati (. TMX,. XLIFF,. xlf,. ICI,. xlsx). |
| File di archivio | Un file che contiene altri file. I formati di file supportati sono quelli con estensione zip, gz e tgz.  |
| Punteggio BLEU   | [BLEU](what-is-bleu-score.md) è il metodo standard del settore per la valutazione della "precisione" o accuratezza del modello di traduzione. Anche se esistono altri metodi di valutazione, Microsoft Translator si basa sul metodo BLEU per segnalare ai proprietari di progetto il livello di accuratezza.
