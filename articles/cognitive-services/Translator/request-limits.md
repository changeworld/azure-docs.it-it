---
title: Limiti delle richieste - Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo elenca i limiti delle richieste per Translator. Sono previsti addebiti in base al numero di caratteri, non alla frequenza delle richieste, con un limite di 5000 caratteri per ogni richiesta. I limiti dei caratteri sono basati su sottoscrizione, con F0 limitato a 2 milioni di caratteri all'ora.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727943"
---
# <a name="request-limits-for-translator"></a>Limiti delle richieste per Translator

Questo articolo illustra i limiti di limitazione per la traduzione, la traslitterazione, il rilevamento della lunghezza delle frasi, il rilevamento della lingua e le traduzioni alternative.

## <a name="character-and-array-limits-per-request"></a>Limiti di caratteri e matrici per richiesta

Ogni richiesta di traduzione è limitata a 10.000 caratteri in tutte le lingue di destinazione in cui si traduce. Ad esempio, l'invio di una richiesta di traduzione di 3.000 caratteri per la conversione in tre lingue diverse comporta una dimensione della richiesta di 3000x3 = 9.000 caratteri, che soddisfano il limite di richieste. Sono previsti addebiti in base ai caratteri e non al numero di richieste. È consigliabile inviare richieste più brevi.

Nella tabella seguente sono elencati i limiti degli elementi della matrice e dei caratteri per ogni operazione di Translator.

| Operazione | Dimensioni massime dell'elemento array |    Numero massimo di elementi della matrice |    Dimensioni massime richiesta (caratteri) |
|:----|:----|:----|:----|
| Traduci | 10,000| 100| 10,000 |
| Transliterate | 5\.000| 10| 5\.000 |
| Detect | 50.000 |100 |50.000 |
| BreakSentence | 50.000| 100 |50.000 |
| Ricerca nel dizionario| 100 |10| 1\.000 |
| Esempi di dizionari | 100 per il testo e 100 per la traduzione (totale 200)| 10|2.000 |

## <a name="character-limits-per-hour"></a>Limiti di caratteri all'ora

Il limite di caratteri all'ora è basato sul livello di sottoscrizione di Translator.

La quota oraria deve essere utilizzata in modo uniforme durante l'ora. Ad esempio, al limite del livello F0 di 2 milioni di caratteri all'ora, i caratteri devono essere utilizzati non più velocemente di circa 33.300 caratteri al minuto (2 milioni di caratteri divisi per 60 minuti).

Se si raggiungono o superano questi limiti o si invia una parte troppo grande della quota in un breve periodo di tempo, è probabile che si riceva una risposta fuori quota. Non sono previsti limiti per le richieste simultanee.

| Livello | Limite di caratteri |
|------|-----------------|
| F0 | 2 milioni di caratteri all'ora |
| S1 | 40 milioni di caratteri all'ora |
| S2/C2 | 40 milioni di caratteri all'ora |
| S3/C3 | 120 milioni di caratteri all'ora |
| S4/C4 | 200 milioni di caratteri all'ora |

I limiti [per le sottoscrizioni multi-servizio](./reference/v3-0-reference.md#authentication) sono gli stessi del livello S1.

Questi limiti sono limitati ai modelli di traduzione standard di Microsoft. I modelli di traduzione personalizzati che traduttore personalizzato possono usare solo 1.800 caratteri al secondo, per modello.

## <a name="latency"></a>Latenza

Translator ha una latenza massima di 15 secondi usando i modelli standard e 120 secondi quando si usano modelli personalizzati. In genere, le risposte per il testo entro *100 caratteri* vengono restituite in 150 millisecondi a 300 millisecondi. I modelli di traduzione personalizzati hanno caratteristiche di latenza simili alla frequenza delle richieste sostenuta e possono avere una latenza maggiore quando la frequenza delle richieste è intermittente. I tempi di risposta variano in base alle dimensioni della coppia di richiesta e lingua. Se non si riceve una [](./reference/v3-0-reference.md#errors) traduzione o una risposta di errore entro tale intervallo di tempo, controllare il codice, la connessione di rete e riprovare.

## <a name="sentence-length-limits"></a>Limiti di lunghezza delle frasi

Quando si usa la funzione [BreakSentence](./reference/v3-0-break-sentence.md), lunghezza delle frasi è limitata a 275 caratteri. Sono previste eccezioni per queste lingue:

| Linguaggio | Codice | Limite di caratteri |
|----------|------|-----------------|
| Cinese | zh | 166 |
| Tedesco | de | 800 |
| Italiano | it | 800 |
| Giapponese | ja | 166 |
| Portoghese | pt | 800 |
| Spagnolo | es | 800 |
| Thai | th | 180 |

> [!NOTE]
> Questo limite non si applica alle traduzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilità a livello di area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Informazioni di riferimento su Traduttore v3](./reference/v3-0-reference.md)