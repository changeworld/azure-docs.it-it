---
title: Dichiarare le risorse nei modelli
description: Viene descritto come dichiarare le risorse da distribuire in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745651"
---
# <a name="resource-declaration-in-arm-templates"></a>Dichiarazione di risorse nei modelli ARM

Per distribuire una risorsa tramite un modello di Azure Resource Manager (modello ARM), è necessario aggiungere una dichiarazione di risorsa. Usare la `resources` matrice per il modello JSON o la `resource` parola chiave per il bicipite.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Imposta il tipo di risorsa e la versione

Quando si aggiunge una risorsa al modello, iniziare impostando il tipo di risorsa e la versione dell'API. Questi valori determinano le altre proprietà disponibili per la risorsa.

L'esempio seguente illustra come impostare il tipo di risorsa e la versione dell'API per un account di archiviazione. Nell'esempio non viene visualizzata la dichiarazione di risorse completa.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Per bicipite si imposta un nome simbolico per la risorsa. Nell'esempio precedente, il nome simbolico è `sa` . È possibile usare qualsiasi valore per il nome simbolico, ma non può essere uguale a un'altra risorsa, parametro o variabile nel modello. Il nome simbolico non corrisponde al nome della risorsa. Usare il nome simbolico per fare facilmente riferimento alla risorsa in altre parti del modello.

## <a name="set-resource-name"></a>Imposta il nome della risorsa

Ogni risorsa ha un nome. Quando si imposta il nome della risorsa, prestare attenzione alle [regole e alle restrizioni per i nomi delle risorse](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Impostare il percorso

Molte risorse richiedono un percorso. È possibile determinare se la risorsa necessita di un percorso tramite IntelliSense o il riferimento a un [modello](/azure/templates/). Nell'esempio seguente viene aggiunto un parametro location usato per l'account di archiviazione.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Per altre informazioni, vedere [impostare la posizione delle risorse nel modello ARM](resource-location.md).

## <a name="set-tags"></a>Imposta tag

È possibile applicare tag a una risorsa durante la distribuzione. I tag consentono di organizzare in modo logico le risorse distribuite. Per esempi dei diversi modi in cui è possibile specificare i tag, vedere [tag del modello ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Imposta proprietà specifiche delle risorse

Le proprietà precedenti sono generiche per la maggior parte dei tipi di risorse. Dopo aver impostato tali valori, è necessario impostare le proprietà specifiche per il tipo di risorsa che si sta distribuendo.

Usare IntelliSense o il riferimento a un [modello](/azure/templates/) per determinare quali proprietà sono disponibili e quali sono necessarie. Nell'esempio seguente vengono impostate le proprietà rimanenti per un account di archiviazione.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per distribuire una risorsa in modo condizionale, vedere [distribuzione condizionale nei modelli ARM](conditional-resource-deployment.md).
* Per impostare le dipendenze delle risorse, vedere [definire l'ordine per la distribuzione delle risorse nei modelli ARM](define-resource-dependency.md).
