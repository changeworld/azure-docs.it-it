---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467245"
---
Esaminare i valori di `"confidence"` per ogni risultato di chiave-valore nel nodo `"pageResults"`. È inoltre consigliabile esaminare i punteggi di attendibilità nel nodo `"readResults"`, che corrispondono all'operazione di lettura del testo. L'attendibilità dei risultati della lettura non influisce su quella dei risultati dell'estrazione di coppie chiave-valore, quindi è necessario controllare entrambi.
* Se i punteggi di attendibilità dell'operazione di lettura sono bassi, provare a migliorare la qualità dei documenti di input. Vedere [Requisiti di input](../overview.md#input-requirements).
* Se i punteggi di attendibilità per l'operazione di estrazione di coppie chiave-valore sono bassi, assicurarsi che i documenti da analizzare siano dello stesso tipo di quelli usati nel set di training. Se i documenti del set di training hanno aspetti diversi, è consigliabile dividerli in cartelle diverse ed eseguire il training di un modello per ogni variante.

I punteggi di attendibilità obiettivo dipendono dal caso d'uso, ma in genere è consigliabile avere come obiettivo un punteggio di almeno l'80%. Per i casi più sensibili, come la lettura di record medici o gli estratti conto, è consigliabile un punteggio del 100%.