---
title: Operatori Bicep
description: Descrive gli operatori Bicep disponibili per le Azure Resource Manager distribuzione.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537823"
---
# <a name="bicep-operators"></a>Operatori Bicep

Questo articolo descrive gli operatori Bicep disponibili quando si crea un modello Bicep e si usa Azure Resource Manager distribuire le risorse. Gli operatori vengono usati per calcolare valori, confrontare valori o valutare condizioni. Esistono tre tipi di operatori Bicep:

- [confronto](#comparison)
- [logico](#logical)
- [numeric](#numeric)

Racchiudendo un'espressione tra `(` e è possibile eseguire `)` l'override della precedenza predefinita dell'operatore Bicep. Ad esempio, l'espressione x + y / z valuta prima la divisione e quindi l'addizione. Tuttavia, l'espressione (x + y) / z valuta l'addizione prima e la seconda divisione.

## <a name="comparison"></a>Confronto

Gli operatori di confronto confrontano i valori e restituiscono `true` o `false` .

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `>=` | [Maggiore o uguale a](bicep-operators-comparison.md#greater-than-or-equal-) | Valuta se il primo valore è maggiore o uguale al secondo valore. |
| `>`  | [Maggiore di](bicep-operators-comparison.md#greater-than-) | Valuta se il primo valore è maggiore del secondo. |
| `<=` | [Minore o uguale a](bicep-operators-comparison.md#less-than-or-equal-) | Valuta se il primo valore è minore o uguale al secondo. |
| `<`  | [Minore di](bicep-operators-comparison.md#less-than-) | Valuta se il primo valore è minore del secondo. |
| `==` | [È uguale a](bicep-operators-comparison.md#equals-) | Valuta se due valori sono uguali. |
| `!=` | [Diverso da](bicep-operators-comparison.md#not-equal-) | Valuta se due valori non **sono** uguali. |
| `=~` | [Senza distinzione tra maiuscole e minuscole](bicep-operators-comparison.md#equal-case-insensitive-) | Ignora la distinzione tra maiuscole e minuscole per determinare se due valori sono uguali. |
| `!~` | [Senza distinzione tra maiuscole e minuscole](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignora la distinzione tra maiuscole e minuscole per determinare se due **valori non sono** uguali. |

## <a name="logical"></a>Logico

Gli operatori logici valutano valori booleani, restituiscono valori non Null o valutano un'espressione condizionale.

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `&&` | [And](bicep-operators-logical.md#and-) | Restituisce `true` se tutti i valori sono true. |
| `||`| [Or](bicep-operators-logical.md#or-) | Restituisce `true` se uno dei due valori è true. |
| `!` | [Non](bicep-operators-logical.md#not-) | Nega un valore booleano. |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | Restituisce il primo valore non Null. |
| `?` `:` | [Espressione condizionale](bicep-operators-logical.md#conditional-expression--) | Valuta una condizione per true o false e restituisce un valore. |

## <a name="numeric"></a>Numeric

Gli operatori numerici usano numeri interi per eseguire calcoli e restituire valori integer.

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `*` | [Moltiplicare](bicep-operators-numeric.md#multiply-) | Moltiplica due numeri interi. |
| `/` | [Dividere](bicep-operators-numeric.md#divide-) | Divide un numero intero per un numero intero. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Divide un numero intero per un numero intero e restituisce il resto. |
| `+` | [Aggiungere](bicep-operators-numeric.md#add-) | Aggiunge due numeri interi. |
| `-` | [Sottrarre](bicep-operators-numeric.md#subtract--) | Sottrae un numero intero da un numero intero. |
| `-` | [Meno](bicep-operators-numeric.md#minus--) | Moltiplica un numero intero per `-1` . |

> [!NOTE]
> Le sottrazioni e le sottrazioni usano lo stesso operatore. La funzionalità è diversa perché la sottrazione usa due operandi e meno usa un operando.

## <a name="next-steps"></a>Passaggi successivi

- Per creare un file Bicep, vedere Esercitazione: Creare e distribuire prima Azure Resource Manager [file Bicep.](bicep-tutorial-create-first-bicep.md)
- Per informazioni su come risolvere gli errori di tipo Bicep, vedere [Qualsiasi funzione per Bicep.](template-functions-any.md)
- Per confrontare la sintassi per Bicep e JSON, vedere [Confronto tra JSON e Bicep per i modelli.](compare-template-syntax.md)
- Per esempi di funzioni del modello Bicep e ARM, vedere Funzioni [del modello di Arm.](template-functions.md)
