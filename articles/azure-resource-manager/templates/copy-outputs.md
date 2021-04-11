---
title: Definire più istanze di un valore di output
description: Usare l'operazione di copia in un modello di Azure Resource Manager (modello ARM) per eseguire un'iterazione più volte quando si restituisce un valore da una distribuzione.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385735"
---
# <a name="output-iteration-in-arm-templates"></a>Iterazione di output nei modelli ARM

Questo articolo illustra come creare più di un valore per un output nel modello di Azure Resource Manager (modello ARM). Aggiungendo il ciclo di copia alla sezione Outputs del modello, è possibile restituire dinamicamente un numero di elementi durante la distribuzione.

È anche possibile usare il ciclo di copia con [risorse](copy-resources.md), [Proprietà in una risorsa](copy-properties.md)e [variabili](copy-variables.md).

## <a name="syntax"></a>Sintassi

# <a name="json"></a>[JSON](#tab/json)

Aggiungere l' `copy` elemento alla sezione output del modello per restituire un numero di elementi. Il formato generale dell'elemento Copy è il seguente:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

La `count` proprietà specifica il numero di iterazioni desiderate per il valore di output.

La `input` proprietà specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruita dal valore della proprietà `input`. Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

# <a name="bicep"></a>[Bicep](#tab/bicep)

È possibile usare i cicli per restituire un numero di elementi durante la distribuzione:

- Iterazione su una matrice:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Iterazione sugli elementi di una matrice

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Utilizzo dell'indice del ciclo

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Può essere zero se si distribuisce il modello con una versione recente dell'interfaccia della riga di comando di Azure, PowerShell o l'API REST. In particolare, è necessario usare:

- Azure PowerShell **2,6** o versione successiva
- INTERFACCIA della riga di comando di Azure **2.0.74** o versione successiva
- API REST versione **2019-05-10** o successiva
- Le [distribuzioni collegate](linked-templates.md) devono usare l'API versione **2019-05-10** o successiva per il tipo di risorsa di distribuzione

Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

## <a name="outputs-iteration"></a>Iterazione degli output

Nell'esempio seguente viene creato un numero variabile di account di archiviazione e viene restituito un endpoint per ogni account di archiviazione:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

Il modello precedente restituisce una matrice con i valori seguenti:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Nell'esempio seguente vengono restituite tre proprietà dai nuovi account di archiviazione.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

Nell'esempio precedente viene restituita una matrice con i valori seguenti:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire un'esercitazione, vedere [esercitazione: creare più istanze di risorse con i modelli ARM](template-tutorial-create-multiple-instances.md).
- Per altri usi del ciclo di copia, vedere:
  - [Iterazione delle risorse nei modelli ARM](copy-resources.md)
  - [Iterazione delle proprietà nei modelli ARM](copy-properties.md)
  - [Iterazione delle variabili nei modelli ARM](copy-variables.md)
- Per informazioni sulle sezioni di un modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
- Per informazioni su come distribuire il modello, vedere [distribuire le risorse con i modelli ARM e Azure PowerShell](deploy-powershell.md).
