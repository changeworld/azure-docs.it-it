---
title: Modelli di esempio
description: Elenca le origini per i modelli di esempio.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530536"
---
# <a name="sample-models"></a>Modelli di esempio

Questo articolo elenca alcune risorse per i dati di esempio utilizzabili per testare il servizio Rendering remoto di Azure.

## <a name="built-in-sample-model"></a>Modello di esempio predefinito

È disponibile un modello di esempio predefinito che può essere sempre caricato usando l'URL **builtin://Engine**

![Modello di esempio](./media/sample-model.png "Modello di esempio")

Statistiche modello:

| Nome | valore |
|-----------|:-----------|
| [Dimensioni server necessarie](../reference/vm-sizes.md) | standard |
| Numero di triangoli | 18,7 milioni |
| Numero di parti mobili | 2073 |
| Numero di materiali | 94 |

## <a name="third-party-data"></a>Dati di terze parti

Il gruppo Khronos gestisce un set di modelli di esempio glTF per i test. Rendering remoto di Azure supporta il formato glTF in formato testo (*con estensione glTF*) e in formato binario (*con estensione glb*). È consigliabile usare i modelli PBR per ottenere risultati visivi ottimali:

* [Modelli di esempio glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
* [Avvio rapido: Convertire un modello per il rendering](../quickstarts/convert-model.md)
