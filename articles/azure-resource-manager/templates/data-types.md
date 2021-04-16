---
title: Tipi di dati nei modelli
description: Descrive i tipi di dati disponibili nei modelli Azure Resource Manager dati.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538073"
---
# <a name="data-types-in-arm-templates"></a>Tipi di dati nei modelli arm

Questo articolo descrive i tipi di dati supportati nei modelli di Azure Resource Manager (modelli arm). Vengono illustrati sia i tipi di dati JSON che i tipi di dati Bicep.

## <a name="supported-types"></a>Tipi supportati

All'interno di un modello arm è possibile usare questi tipi di dati:

* array
* bool
* INT
* object
* secureObject : indicato dal modificatore in Bicep
* secureString : indicato dal modificatore in Bicep
* string

## <a name="arrays"></a>Matrici

Le matrici iniziano con una parentesi quadra aperta ( `[` ) e terminano con una parentesi quadra chiusa ( `]` ).

In JSON una matrice può essere dichiarata in una o più righe. Ogni elemento è separato da una virgola.

In Bicep una matrice deve essere dichiarata in più righe. Non usare virgole tra i valori.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Gli elementi di una matrice possono essere dello stesso tipo o di tipi diversi.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>valori booleani

Quando si specificano valori booleani, usare `true` o `false` . Non racchiudere il valore tra virgolette.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Integer

Quando si specificano valori interi, non usare le virgolette.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Per i numeri interi passati come parametri inline, l'intervallo di valori può essere limitato dall'SDK o dallo strumento da riga di comando utilizzato per la distribuzione. Ad esempio, quando si usa PowerShell per distribuire un modello, i tipi integer possono variare da -2147483648 a 2147483647. Per evitare questa limitazione, specificare valori interi di grandi dimensioni in un [file di parametri](parameter-files.md). I tipi di risorse applicano i propri limiti per le proprietà integer.

## <a name="objects"></a>Oggetti

Gli oggetti iniziano con una parentesi graffa sinistra ( ) e terminano con una parentesi `{` graffa destra ( `}` ). Ogni proprietà in un oggetto è costituita da chiave e valore. La chiave e il valore sono separati da due punti ( `:` ).

# <a name="json"></a>[JSON](#tab/json)

In JSON la chiave è racchiusa tra virgolette doppie. Ogni proprietà è separata da una virgola.

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

In Bicep la chiave non è racchiusa tra virgolette. Non usare virgole tra le proprietà.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Le funzioni di accesso alle proprietà vengono usate per accedere alle proprietà di un oggetto . Vengono costruiti usando `.` l'operatore . Ad esempio:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

Data la dichiarazione precedente, l'espressione x.y.z restituisce la stringa letterale 'Hello'. Analogamente, l'espressione x.q restituisce il valore letterale integer 42.

Le funzioni di accesso alle proprietà possono essere usate con qualsiasi oggetto. Sono inclusi parametri e variabili di tipi di oggetto e valori letterali oggetto. L'uso di una funzione di accesso alla proprietà in un'espressione di tipo non oggetto è un errore.

---

## <a name="strings"></a>Stringhe

In JSON le stringhe sono contrassegnate con virgolette doppie. In Bicep le stringhe sono contrassegnate con virgolette singole.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Proteggere stringhe e oggetti

La stringa sicura usa lo stesso formato della stringa e l'oggetto protetto usa lo stesso formato dell'oggetto . Quando si imposta un parametro su una stringa sicura o un oggetto protetto, il valore del parametro non viene salvato nella cronologia di distribuzione e non viene registrato. Tuttavia, se si imposta tale valore sicuro su una proprietà che non prevede un valore sicuro, il valore non è protetto. Ad esempio, se si imposta una stringa sicura su un tag, tale valore viene archiviato come testo normale. Usare stringhe sicure per password e segreti.

Con Bicep si aggiunge il `@secure()` modificatore a una stringa o a un oggetto.

L'esempio seguente mostra due parametri sicuri:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla sintassi del modello, vedere [Comprendere la struttura e la sintassi dei modelli di Arm.](template-syntax.md)
