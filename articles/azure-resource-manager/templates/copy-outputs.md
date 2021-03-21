---
title: Definire più istanze di un valore di output
description: Usare l'operazione di copia in un modello di Azure Resource Manager (modello ARM) per eseguire un'iterazione più volte quando si restituisce un valore da una distribuzione.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905945"
---
# <a name="output-iteration-in-arm-templates"></a>Iterazione di output nei modelli ARM

Questo articolo illustra come creare più di un valore per un output nel modello di Azure Resource Manager (modello ARM). Aggiungendo l' `copy` elemento alla sezione Outputs del modello, è possibile restituire dinamicamente un numero di elementi durante la distribuzione.

È anche possibile usare copia con [risorse](copy-resources.md), [Proprietà in una risorsa](copy-properties.md)e [variabili](copy-variables.md).

## <a name="syntax"></a>Sintassi

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

La `count` proprietà specifica il numero di iterazioni desiderate per il valore di output.

La `input` proprietà specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruita dal valore della proprietà `input`. Può essere una singola proprietà (ad esempio una stringa) o un oggetto con diverse proprietà.

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Può essere zero se si distribuisce il modello con una versione recente dell'interfaccia della riga di comando di Azure, PowerShell o l'API REST. In particolare, è necessario usare:

* Azure PowerShell **2,6** o versione successiva
* INTERFACCIA della riga di comando di Azure **2.0.74** o versione successiva
* API REST versione **2019-05-10** o successiva
* Le [distribuzioni collegate](linked-templates.md) devono usare l'API versione **2019-05-10** o successiva per il tipo di risorsa di distribuzione

Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

## <a name="outputs-iteration"></a>Iterazione degli output

Nell'esempio seguente viene creato un numero variabile di account di archiviazione e viene restituito un endpoint per ogni account di archiviazione:

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

Il modello precedente restituisce una matrice con i valori seguenti:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Nell'esempio seguente vengono restituite tre proprietà dai nuovi account di archiviazione.

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

* Per eseguire un'esercitazione, vedere [esercitazione: creare più istanze di risorse con i modelli ARM](template-tutorial-create-multiple-instances.md).
* Per altri usi dell'elemento Copy, vedere:
  * [Iterazione delle risorse nei modelli ARM](copy-resources.md)
  * [Iterazione delle proprietà nei modelli ARM](copy-properties.md)
  * [Iterazione delle variabili nei modelli ARM](copy-variables.md)
* Per informazioni sulle sezioni di un modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [distribuire le risorse con i modelli ARM e Azure PowerShell](deploy-powershell.md).
