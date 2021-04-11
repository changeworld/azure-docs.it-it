---
title: Operatori logici bicipite
description: Descrive gli operatori logici bicipiti che valutano le condizioni.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211339"
---
# <a name="bicep-logical-operators"></a>Operatori logici bicipite

Gli operatori logici valutano i valori booleani, restituiscono valori non null o valutano un'espressione condizionale. Per eseguire gli esempi, usare l'interfaccia della riga di comando di Azure o Azure PowerShell per [distribuire un file bicipite](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatore | Nome |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Or](#or-) |
| `!` | [Non](#not-) |
| `??` | [COALESCE](#coalesce-) |
| `?` `:` | [Espressione condizionale](#conditional-expression--) |

## <a name="and-"></a>E &&

`operand1 && operand2`

Determina se entrambi i valori sono true.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | boolean | Primo valore da verificare se true. |
| `operand2` | boolean | Secondo valore da controllare se true. |
| Altri operandi | boolean | È possibile includere più operandi. |

### <a name="return-value"></a>Valore restituito

`True` Quando entrambi i valori sono true; in caso contrario, `false` viene restituito.

### <a name="example"></a>Esempio

Valuta un set di valori di parametro e un set di espressioni.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>O | |

`operand1 || operand2`

Determina se uno o più valori sono true.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | boolean | Primo valore da verificare se true. |
| `operand2` | boolean | Secondo valore da controllare se true. |
| Altri operandi | boolean | È possibile includere più operandi. |

### <a name="return-value"></a>Valore restituito

`True` Quando uno di questi valori è true, viene restituito, in caso contrario `false` .

### <a name="example"></a>Esempio

Valuta un set di valori di parametro e un set di espressioni.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Non!

`!boolValue`

Nega un valore booleano.

### <a name="operand"></a>Operando

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `boolValue` | boolean | Valore booleano negato. |

### <a name="return-value"></a>Valore restituito

Nega il valore iniziale e restituisce un valore booleano. Se il valore iniziale è `true` , `false` viene restituito.

### <a name="example"></a>Esempio

L' `not` operatore Annulla un valore. I valori possono essere racchiusi tra parentesi.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>COALESCE?

`operand1 ?? operand2`

Restituisce il primo valore non null dagli operandi.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | stringa, numero intero, valore booleano, oggetto, matrice | Valore da verificare `null` . |
| `operand2` | stringa, numero intero, valore booleano, oggetto, matrice | Valore da verificare `null` . |
| Altri operandi | stringa, numero intero, valore booleano, oggetto, matrice | Valore da verificare `null` . |

### <a name="return-value"></a>Valore restituito

Restituisce il primo valore non null. Le stringhe vuote, le matrici vuote e gli oggetti vuoti non sono `null` e \<empty> viene restituito un valore.

### <a name="example"></a>Esempio

Le istruzioni output restituiscono i valori non null. Il tipo di output deve corrispondere al tipo nel confronto oppure viene generato un errore.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | INT | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>Espressione condizionale? :

`condition ? true-value : false-value`

Valuta una condizione e restituisce un valore se la condizione è true o false.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `condition` | boolean | Condizione da valutare come true o false. |
| `true-value` | stringa, numero intero, valore booleano, oggetto, matrice | Valore quando condition è true. |
| `false-value` | stringa, numero intero, valore booleano, oggetto, matrice | Valore quando condition è false. |

### <a name="example"></a>Esempio

In questo esempio viene valutato l'iniziale di un parametro e viene restituito un valore se la condizione è true o false.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `outValue` | string | valore true |

## <a name="next-steps"></a>Passaggi successivi

- Per creare un file bicipite, vedere [esercitazione: creare e distribuire il primo file bicipite Azure Resource Manager](bicep-tutorial-create-first-bicep.md).
- Per informazioni su come risolvere gli errori di tipo bicipite, vedere [qualsiasi funzione per bicipite](template-functions-any.md).
- Per confrontare la sintassi per bicipite e JSON, vedere [confronto tra JSON e bicipite per i modelli](compare-template-syntax.md).
- Per esempi di funzioni modello bicipite e ARM, vedere [funzioni modello ARM](template-functions.md).
