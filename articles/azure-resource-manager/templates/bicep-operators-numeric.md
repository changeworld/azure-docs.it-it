---
title: Operatori numerici bicipite
description: Descrive gli operatori numerici bicipiti che calcolano i valori.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211316"
---
# <a name="bicep-numeric-operators"></a>Operatori numerici bicipite

Gli operatori numerici utilizzano numeri interi per eseguire calcoli e restituire valori integer. Per eseguire gli esempi, usare l'interfaccia della riga di comando di Azure o Azure PowerShell per [distribuire un file bicipite](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operatore | Nome |
| ---- | ---- |
| `*` | [Moltiplicare](#multiply-) |
| `/` | [Dividere](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Aggiungere](#add-) |
| `-` | [Sottrarre](#subtract--) |
| `-` | [Meno](#minus--) |

> [!NOTE]
> Sottrazione e meno utilizzano lo stesso operatore. La funzionalità è diversa perché la sottrazione usa due operandi e meno usa un operando.

## <a name="multiply-"></a>Moltiplicare

`operand1 * operand2`

Moltiplica due numeri interi.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1`  | numero intero | Numero da moltiplicare. |
| `operand2`  | numero intero  | Moltiplicatore del numero. |

### <a name="return-value"></a>Valore restituito

La moltiplicazione restituisce il prodotto come intero.

### <a name="example"></a>Esempio

Due numeri interi vengono moltiplicati e restituiscono il prodotto.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `product` | numero intero | 10 |

## <a name="divide-"></a>Dividere

`operand1 / operand2`

Divide un Integer in base a un intero.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | numero intero | Intero diviso. |
| `operand2` | numero intero | Integer usato per la divisione. Non può essere zero. |

### <a name="return-value"></a>Valore restituito

La divisione restituisce il quoziente sotto forma di numero intero.

### <a name="example"></a>Esempio

Due integer sono divisi e restituiscono il quoziente.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `quotient` | numero intero | 5 |

## <a name="modulo-"></a>Modulo

`operand1 % operand2`

Divide un intero in base a un intero e restituisce il resto.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1`  | numero intero  | Intero diviso. |
| `operand2`  | numero intero  | Integer usato per la divisione. Non può essere 0. |

### <a name="return-value"></a>Valore restituito

Il resto viene restituito come numero intero. Se la divisione non produce un resto, viene restituito 0.

### <a name="example"></a>Esempio

Due coppie di numeri interi vengono divise e restituiscono i rimanenti.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | integer | 0 |

## <a name="add-"></a>Aggiungi +

`operand1 + operand2`

Aggiunge due valori integer.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | numero intero | Numero da aggiungere. |
| `operand2` | numero intero | Numero aggiunto a un numero. |

### <a name="return-value"></a>Valore restituito

L'addizione restituisce la somma come valore integer.

### <a name="example"></a>Esempio

Vengono aggiunti due Integer e viene restituita la somma.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `sum` | numero intero | 12 |

## <a name="subtract--"></a>Sottrarre

`operand1 - operand2`

Sottrae un intero da un Integer.

### <a name="operands"></a>Operandi

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `operand1` | numero intero | Numero maggiore sottratto da. |
| `operand2` | numero intero | Numero sottratto dal numero maggiore. |

### <a name="return-value"></a>Valore restituito

La sottrazione restituisce la differenza come valore integer.

### <a name="example"></a>Esempio

Viene sottratto un numero intero e viene restituita la differenza.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `difference` | numero intero | 6 |

## <a name="minus--"></a>Meno

`-integerValue`

Moltiplica un valore integer per `-1` .

### <a name="operand"></a>Operando

| Operando | Tipo | Descrizione |
| ---- | ---- | ---- |
| `integerValue` | numero intero | Integer moltiplicato per `-1` . |

### <a name="return-value"></a>Valore restituito

Un numero intero viene moltiplicato per `-1` . Un numero intero positivo restituisce un numero intero negativo e un numero intero negativo restituisce un numero intero positivo. I valori possono essere racchiusi tra parentesi.

### <a name="example"></a>Esempio

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Output dell'esempio:

| Nome | Type | valore |
| ---- | ---- | ---- |
| `startedPositive` | numero intero | -10 |
| `startedNegative` | numero intero | 20 |

## <a name="next-steps"></a>Passaggi successivi

- Per creare un file bicipite, vedere [esercitazione: creare e distribuire il primo file bicipite Azure Resource Manager](bicep-tutorial-create-first-bicep.md).
- Per informazioni su come risolvere gli errori di tipo bicipite, vedere [qualsiasi funzione per bicipite](template-functions-any.md).
- Per confrontare la sintassi per bicipite e JSON, vedere [confronto tra JSON e bicipite per i modelli](compare-template-syntax.md).
- Per esempi di funzioni modello bicipite e ARM, vedere [funzioni modello ARM](template-functions.md).
