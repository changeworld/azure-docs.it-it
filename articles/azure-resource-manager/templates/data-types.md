---
title: Tipi di dati nei modelli
description: Vengono descritti i tipi di dati disponibili nei modelli Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125144"
---
# <a name="data-types-in-arm-templates"></a>Tipi di dati nei modelli ARM

Questo articolo descrive i tipi di dati supportati nei modelli di Azure Resource Manager (modelli ARM). Vengono illustrati i tipi di dati JSON e bicipite.

## <a name="supported-types"></a>Tipi supportati

All'interno di un modello ARM, è possibile usare questi tipi di dati:

* array
* bool
* INT
* object
* secureObject-indicato dal modificatore in bicipite
* secureString-indicato dal modificatore in bicipite
* string

## <a name="arrays"></a>Matrici

Le matrici iniziano con una parentesi quadra aperta ( `[` ) e terminano con una parentesi quadra chiusa ( `]` ).

In JSON, una matrice può essere dichiarata in una sola riga o più righe. Ogni elemento è separato da una virgola.

In bicipite una matrice deve essere dichiarata in più righe. Non usare le virgole tra i valori.

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

Quando si specificano valori booleani, utilizzare `true` o `false` . Non racchiudere il valore tra virgolette.

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

Quando si specificano valori integer, non usare le virgolette.

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

Per i numeri interi passati come parametri inline, l'intervallo di valori può essere limitato dall'SDK o dallo strumento da riga di comando usato per la distribuzione. Ad esempio, quando si usa PowerShell per distribuire un modello, i tipi integer possono variare da-2147483648 a 2147483647. Per evitare questa limitazione, specificare valori integer di grandi dimensioni in un [file di parametri](parameter-files.md). I tipi di risorsa applicano i propri limiti per le proprietà Integer.

## <a name="objects"></a>Oggetti

Gli oggetti iniziano con una parentesi graffa sinistra ( `{` ) e terminano con una parentesi graffa destra ( `}` ). Ogni proprietà in un oggetto è costituita da chiave e valore. La chiave e il valore sono separati da due punti ( `:` ).

In JSON la chiave è racchiusa tra virgolette doppie. Ogni proprietà è separata da una virgola.

In bicipite la chiave non è racchiusa tra virgolette. Non usare le virgole tra le proprietà.

# <a name="json"></a>[JSON](#tab/json)

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

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Stringhe

In JSON le stringhe sono contrassegnate con virgolette doppie. In bicipite le stringhe sono contrassegnate con virgolette singole.

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

## <a name="secure-strings-and-objects"></a>Oggetti e stringhe protette

La stringa protetta usa lo stesso formato di stringa e l'oggetto protetto usa lo stesso formato dell'oggetto. Quando si imposta un parametro su una stringa sicura o un oggetto protetto, il valore del parametro non viene salvato nella cronologia di distribuzione e non viene registrato. Tuttavia, se si imposta il valore sicuro su una proprietà che non prevede un valore sicuro, il valore non è protetto. Se, ad esempio, si imposta una stringa sicura su un tag, tale valore viene archiviato come testo normale. Usare le stringhe sicure per le password e i segreti.

Con bicipite si aggiunge il `@secure()` modificatore a una stringa o a un oggetto.

Nell'esempio seguente vengono illustrati due parametri di sicurezza:

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

Per informazioni sulla sintassi del modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
