---
title: Operatori di confronto bicipite
description: Descrive gli operatori di confronto bicipite che confrontano i valori.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211323"
---
# <a name="bicep-comparison-operators"></a>Operatori di confronto bicipite

Gli operatori di confronto confrontano i valori e restituiscono `true` o `false` . Per eseguire gli esempi, usare l'interfaccia della riga di comando di Azure o Azure PowerShell per [distribuire un file bicipite](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatore | Nome |
| ---- | ---- |
| `>=` | [Maggiore o uguale a](#greater-than-or-equal-) |
| `>`  | [Maggiore di](#greater-than-) |
| `<=` | [Minore o uguale a](#less-than-or-equal-) |
| `<`  | [Minore di](#less-than-) |
| `==` | [È uguale a](#equals-) |
| `!=` | [Diverso da](#not-equal-) |
| `=~` | [Uguaglianza senza distinzione tra maiuscole e minuscole](#equal-case-insensitive-) |
| `!~` | [Non uguale a senza distinzione tra maiuscole e minuscole](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Maggiore o uguale a >=

`operand1 >= operand2`

Restituisce se il primo valore è maggiore o uguale al secondo valore.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | Integer, stringa | Primo valore nel confronto. |
| `operand2` | Integer, stringa | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se il primo valore è maggiore o uguale al secondo valore, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Viene confrontata una coppia di numeri interi e stringhe.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Maggiore di >

`operand1 > operand2`

Valuta se il primo valore è maggiore del secondo valore.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | Integer, stringa | Primo valore nel confronto. |
| `operand2` | Integer, stringa | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se il primo valore è maggiore del secondo valore, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Viene confrontata una coppia di numeri interi e stringhe.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Output dell'esempio:

**E** in **curva** rende la prima stringa maggiore.

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Minore o uguale a <=

`operand1 <= operand2`

Restituisce se il primo valore è minore o uguale al secondo valore.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | Integer, stringa | Primo valore nel confronto. |
| `operand2` | Integer, stringa | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se il primo valore è minore o uguale al secondo valore, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Viene confrontata una coppia di numeri interi e stringhe.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Minore di <

`operand1 < operand2`

Valuta se il primo valore è minore del secondo valore.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | Integer, stringa | Primo valore nel confronto. |
| `operand2` | Integer, stringa | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se il primo valore è minore del secondo valore, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Viene confrontata una coppia di numeri interi e stringhe.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Output dell'esempio:

La stringa è `true` perché le lettere minuscole sono minori delle lettere maiuscole.

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Uguale a = =

`operand1 == operand2`

Valuta se i valori sono uguali.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | String, Integer, Boolean, array, Object | Primo valore nel confronto. |
| `operand2` | String, Integer, Boolean, array, Object | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se gli operandi sono uguali, `true` viene restituito. Se gli operandi sono diversi, `false` viene restituito.

### <a name="example"></a>Esempio

Vengono confrontate coppie di numeri interi, stringhe e valori Boolean.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Diverso da! =

`operand1 != operand2`

Valuta se due valori **non** sono uguali.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | String, Integer, Boolean, array, Object | Primo valore nel confronto. |
| `operand2` | String, Integer, Boolean, array, Object | Secondo valore nel confronto. |

### <a name="return-value"></a>Valore restituito

Se gli operandi **non** sono uguali, `true` viene restituito. Se gli operandi sono uguali, `false` viene restituito.

### <a name="example"></a>Esempio

Vengono confrontate coppie di numeri interi, stringhe e valori Boolean.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Uguaglianza senza distinzione tra maiuscole e minuscole = ~

`operand1 =~ operand2`

Ignora la distinzione tra maiuscole e minuscole per determinare se i due valori sono uguali.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1`  | string | Prima stringa nel confronto. |
| `operand2`  | string | Seconda stringa nel confronto. |

### <a name="return-value"></a>Valore restituito

Se le stringhe sono uguali, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Confronta le stringhe che usano lettere maiuscole e minuscole.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>Non uguale a senza distinzione tra maiuscole e minuscole! ~

`operand1 !~ operand2`

Ignora la distinzione tra maiuscole e minuscole per determinare se i due valori **non** sono uguali.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | string | Prima stringa nel confronto. |
| `operand2` | string | Seconda stringa nel confronto. |

### <a name="return-value"></a>Valore restituito

Se le stringhe **non** sono uguali, `true` viene restituito. In caso contrario, viene restituito `false`.

### <a name="example"></a>Esempio

Confronta le stringhe che usano lettere maiuscole e minuscole.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>Passaggi successivi

- Per creare un file bicipite, vedere [esercitazione: creare e distribuire il primo file bicipite Azure Resource Manager](bicep-tutorial-create-first-bicep.md).
- Per informazioni su come risolvere gli errori di tipo bicipite, vedere [qualsiasi funzione per bicipite](template-functions-any.md).
- Per confrontare la sintassi per bicipite e JSON, vedere [confronto tra JSON e bicipite per i modelli](compare-template-syntax.md).
- Per esempi di funzioni modello bicipite e ARM, vedere [funzioni modello ARM](template-functions.md).
