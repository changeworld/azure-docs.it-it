---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 7cf3d86eeea9d1b0f5fcbb757d3597e21cbcc369
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901015"
---
Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG, PDF (testo o digitalizzato) o TIFF. I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Le dimensioni del file devono essere minori di 50 MB.
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 10000 x 10000 pixel.
* Le dimensioni dei PDF devono essere al massimo di 17 x 17 pollici, corrispondenti a formati della carta Legal o A3 e inferiori.
* Per PDF e TIFF, vengono elaborate solo le prime 200 pagine (con una sottoscrizione di livello gratuito, vengono elaborate solo le prime due pagine).
* Le dimensioni totali del training set non devono superare le 500 pagine.
* Se i file PDF sono bloccati da password, è necessario rimuovere il blocco prima di inviarli.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* Per l'apprendimento non supervisionato (senza dati etichettati), i dati devono contenere chiavi e valori.
* Per l'apprendimento non supervisionato (senza dati etichettati), le chiavi devono essere visualizzate sopra o a sinistra dei valori; non possono essere visualizzate sotto o a destra.
