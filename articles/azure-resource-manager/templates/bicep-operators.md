---
title: Operatori bicipite
description: Vengono descritti gli operatori bicipiti disponibili per le distribuzioni Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211332"
---
# <a name="bicep-operators"></a>Operatori bicipite

Questo articolo descrive gli operatori bicipite disponibili quando si crea un modello bicipite e si usa Azure Resource Manager per distribuire le risorse. Gli operatori vengono utilizzati per calcolare i valori, confrontare i valori o valutare le condizioni. Sono disponibili tre tipi di operatori bicipite: [confronto](#comparison), [logico](#logical)e [numerico](#numeric).

## <a name="comparison"></a>Confronto

Gli operatori di confronto confrontano i valori e restituiscono `true` o `false` .

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `>=` | [Maggiore o uguale a](bicep-operators-comparison.md#greater-than-or-equal-) | Restituisce se il primo valore è maggiore o uguale al secondo valore. |
| `>`  | [Maggiore di](bicep-operators-comparison.md#greater-than-) | Valuta se il primo valore è maggiore del secondo valore. |
| `<=` | [Minore o uguale a](bicep-operators-comparison.md#less-than-or-equal-) | Restituisce se il primo valore è minore o uguale al secondo valore. |
| `<`  | [Minore di](bicep-operators-comparison.md#less-than-) | Valuta se il primo valore è minore del secondo valore. |
| `==` | [È uguale a](bicep-operators-comparison.md#equals-) | Valuta se due valori sono uguali. |
| `!=` | [Diverso da](bicep-operators-comparison.md#not-equal-) | Valuta se due valori **non** sono uguali. |
| `=~` | [Uguaglianza senza distinzione tra maiuscole e minuscole](bicep-operators-comparison.md#equal-case-insensitive-) | Ignora la distinzione tra maiuscole e minuscole per determinare se due valori sono uguali. |
| `!~` | [Non uguale a senza distinzione tra maiuscole e minuscole](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignora la distinzione tra maiuscole e minuscole per determinare se due valori **non** sono uguali. |

## <a name="logical"></a>Logico

Gli operatori logici valutano i valori booleani, restituiscono valori non null o valutano un'espressione condizionale.

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `&&` | [And](bicep-operators-logical.md#and-) | Restituisce `true` se tutti i valori sono true. |
| `||`| [Or](bicep-operators-logical.md#or-) | Restituisce `true` se uno degli due valori è true. |
| `!` | [Non](bicep-operators-logical.md#not-) | Nega un valore booleano. |
| `??` | [COALESCE](bicep-operators-logical.md#coalesce-) | Restituisce il primo valore non null. |
| `?` `:` | [Espressione condizionale](bicep-operators-logical.md#conditional-expression--) | Valuta una condizione per true o false e restituisce un valore. |

## <a name="numeric"></a>Numeric

Gli operatori numerici utilizzano numeri interi per eseguire calcoli e restituire valori integer.

| Operatore | Nome | Descrizione |
| ---- | ---- | ---- |
| `*` | [Moltiplicare](bicep-operators-numeric.md#multiply-) | Moltiplica due numeri interi. |
| `/` | [Dividere](bicep-operators-numeric.md#divide-) | Divide un Integer in base a un intero. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Divide un intero in base a un intero e restituisce il resto. |
| `+` | [Aggiungere](bicep-operators-numeric.md#add-) | Aggiunge due valori integer. |
| `-` | [Sottrarre](bicep-operators-numeric.md#subtract--) | Sottrae un intero da un Integer. |
| `-` | [Meno](bicep-operators-numeric.md#minus--) | Moltiplica un valore integer per `-1` . |

> [!NOTE]
> Sottrazione e meno utilizzano lo stesso operatore. La funzionalità è diversa perché la sottrazione usa due operandi e meno usa un operando.

## <a name="next-steps"></a>Passaggi successivi

- Per creare un file bicipite, vedere [esercitazione: creare e distribuire il primo file bicipite Azure Resource Manager](bicep-tutorial-create-first-bicep.md).
- Per informazioni su come risolvere gli errori di tipo bicipite, vedere [qualsiasi funzione per bicipite](template-functions-any.md).
- Per confrontare la sintassi per bicipite e JSON, vedere [confronto tra JSON e bicipite per i modelli](compare-template-syntax.md).
- Per esempi di funzioni modello bicipite e ARM, vedere [funzioni modello ARM](template-functions.md).
