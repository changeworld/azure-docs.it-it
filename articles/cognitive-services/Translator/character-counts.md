---
title: Conteggi dei caratteri - Traduzione
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come il Servizi cognitivi di Azure Translator conta i caratteri in modo da poter comprendere come inserire il contenuto.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373928"
---
# <a name="how-the-translator-counts-characters"></a>Modalità di conteggio dei caratteri da parte di Translator

Translator conta ogni punto di codice Unicode del testo di input come carattere. Ogni traduzione di un testo in una lingua viene conteggiata come una traduzione separata, anche se la richiesta è stata effettuata in una singola chiamata API per la traduzione in più lingue. Lunghezza della risposta non ha importanza.

Gli elementi importanti sono i seguenti:

* Testo passato a Translator nel corpo della richiesta
   * `Text` quando si usano i metodi relativi alla traduzione, alla traslitterazione e alla ricerca nel dizionario
   * `Text` e `Translation` quando si usa il metodo relativo agli esempi di dizionari
* Tutto il markup: HTML, tag XML e così via all'interno del campo di testo del corpo della richiesta. La notazione JSON usata per compilare la richiesta, ad esempio "Text:", non viene conteggiata.
* Una singola lettera
* Punteggiatura
* Uno spazio, una tabulazione, il markup e qualsiasi tipo di carattere che rappresenti uno spazio vuoto
* Ogni elemento di codice definito in Unicode
* Una traduzione ripetuta, anche se lo stesso testo è stato tradotto in precedenza

Per gli script basati su ideogrammi come kanji cinese e giapponese, il servizio Translator conta ancora il numero di punti di codice Unicode, un carattere per ideogramma. Eccezione: i surrogati Unicode vengono conteggiati come due caratteri.

Il numero di richieste, parole, byte o frasi non è rilevante nel conteggio dei caratteri.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Se il numero di chiamate Detect o BreakSentence effettuate supera di 100 volte il numero di altri metodi conteggiati, Microsoft si riserva il diritto di limitare l'uso dei metodi Detect e BreakSentence.

Ogni carattere inviato alla funzione translate viene conteggiato anche quando il contenuto non viene modificato o quando la lingua di origine e di destinazione è la stessa.

Altre informazioni sul conteggio dei caratteri sono disponibili nelle domande [frequenti su Translator.](https://www.microsoft.com/en-us/translator/faq.aspx)
