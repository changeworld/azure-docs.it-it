---
title: Impedisci conversione contenuto-Translator
titleSuffix: Azure Cognitive Services
description: Impedisci la conversione del contenuto con il traduttore. Il convertitore consente di contrassegnare il contenuto in modo che non venga tradotto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563433"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Come impedire la conversione del contenuto con il traduttore

Il convertitore consente di contrassegnare il contenuto in modo che non venga tradotto. Ad esempio, potrebbe essere necessario contrassegnare del codice, il nome di un marchio o una parola/frase la cui localizzazione non avrebbe senso.

## <a name="methods-for-preventing-translation"></a>Metodi per impedire la traduzione

1. Aggiungere tag al contenuto con `notranslate`. Si tratta di un modello che funziona solo quando il textType di input è impostato come HTML

   Esempio:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Aggiungere tag al contenuto con `translate="no"`. Funziona solo quando il textType di input è impostato come HTML

   Esempio:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Usare il [dizionario dinamico](dynamic-dictionary.md) per definire una traduzione specifica.

4. Non passare la stringa al convertitore per la conversione.

5. Traduttore personalizzato: usare un [dizionario in un convertitore personalizzato](custom-translator/what-is-dictionary.md) per prescrivere la traduzione di una frase con probabilità del 100%.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Usare l'operazione translate per tradurre il testo](reference/v3-0-translate.md)
