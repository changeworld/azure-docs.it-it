---
title: Distribuzione condizionale con modelli
description: Viene descritto come distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741175"
---
# <a name="conditional-deployment-in-arm-templates"></a>Distribuzione condizionale nei modelli di ARM

In alcuni casi è necessario distribuire facoltativamente una risorsa in un modello di Azure Resource Manager (modello ARM) o in un file bicipite. Per i modelli JSON, usare l' `condition` elemento per specificare se la risorsa viene distribuita. Per bicipite, usare la `if` parola chiave per specificare se la risorsa viene distribuita. Il valore della condizione viene risolto in true o false. Quando il valore è true, la risorsa viene creata. Quando il valore è false, la risorsa non viene creata. Il valore può essere applicato solo all'intera risorsa.

> [!NOTE]
> La distribuzione condizionale non si sovrappone alle [risorse figlio](child-resource-name-type.md). Se si desidera distribuire una risorsa e le relative risorse figlio in modo condizionale, è necessario applicare la stessa condizione a ogni tipo di risorsa.

## <a name="deploy-condition"></a>Condizione di distribuzione

È possibile passare un valore di parametro che indica se una risorsa viene distribuita. Nell'esempio seguente viene distribuita in modo condizionale una zona DNS.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Per un esempio più complesso, vedere [server logico SQL di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Risorsa nuova o esistente

È possibile usare la distribuzione condizionale per creare una nuova risorsa o utilizzarne una esistente. L'esempio seguente illustra come distribuire un nuovo account di archiviazione o usare un account di archiviazione esistente.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
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
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

Quando il parametro `newOrExisting` è impostato su **New**, la condizione restituisce true. L'account di archiviazione viene distribuito. Tuttavia, quando `newOrExisting` è impostato su **existing**, la condizione restituisce false e l'account di archiviazione non viene distribuito.

Per un modello di esempio completo che usa l'elemento `condition`, vedere [Macchina virtuale con una Rete virtuale nuova o esistente, archiviazione e indirizzo IP pubblico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Funzioni di runtime

Se si usa una funzione di [riferimento](template-functions-resource.md#reference) o [elenco](template-functions-resource.md#list) con una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non viene distribuita. Viene ricevuto un errore se la funzione fa riferimento a una risorsa che non esiste.

Usare la funzione [if](template-functions-logical.md#if) per assicurarsi che la funzione venga valutata solo per le condizioni quando la risorsa viene distribuita. Vedere la [funzione If](template-functions-logical.md#if) per un modello di esempio che usa `if` e `reference` con una risorsa distribuita in modo condizionale.

Si imposta una [risorsa come dipendente](define-resource-dependency.md) da una risorsa condizionale esattamente come per qualsiasi altra risorsa. Quando una risorsa condizionale non viene distribuita, Azure Resource Manager la rimuove automaticamente dalle dipendenze richieste.

## <a name="complete-mode"></a>Modalità completa

Se si distribuisce un modello con la [modalità completa](deployment-modes.md) e una risorsa non viene distribuita perché `condition` restituisce false, il risultato dipende dalla versione dell'API REST usata per distribuire il modello. Se si usa una versione precedente alla 2019-05-10, la risorsa **non viene eliminata**. Con 2019-05-10 o versioni successive, la risorsa **viene eliminata**. Le versioni più recenti di Azure PowerShell e dell'interfaccia della riga di comando di Azure eliminano la risorsa quando condition è false.

## <a name="next-steps"></a>Passaggi successivi

* Per un modulo Microsoft Learn che copre la distribuzione condizionale, vedere [gestire le distribuzioni cloud complesse usando le funzionalità avanzate del modello ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Per consigli sulla creazione di modelli, vedere procedure consigliate per il [modello ARM](template-best-practices.md).
* Per creare più istanze di una risorsa, vedere [iterazione delle risorse nei modelli ARM](copy-resources.md).
