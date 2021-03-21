---
title: Output nei modelli
description: Viene descritto come definire i valori di output in un modello di Azure Resource Manager (modello ARM) e un file bicipite.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123412"
---
# <a name="outputs-in-arm-templates"></a>Output nei modelli ARM

Questo articolo descrive come definire i valori di output nel modello di Azure Resource Manager (modello ARM) e nel file bicipite. Gli output vengono usati quando è necessario restituire valori dalle risorse distribuite.

Il formato di ogni valore di output deve essere risolto in uno dei [tipi di dati](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definire i valori di output

Nell'esempio seguente viene illustrato come restituire una proprietà da una risorsa distribuita.

# <a name="json"></a>[JSON](#tab/json)

Per JSON, aggiungere la `outputs` sezione al modello. Il valore di output ottiene il nome di dominio completo per un indirizzo IP pubblico.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Per bicipite, usare la `output` parola chiave.

Nell'esempio seguente `publicIP` è l'identificatore di un indirizzo IP pubblico distribuito nel file bicipite. Il valore di output ottiene il nome di dominio completo per l'indirizzo IP pubblico.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Se è necessario restituire una proprietà con un trattino nel nome, usare le parentesi quadre intorno al nome anziché alla notazione del punto. Ad esempio, usare  `['property-name']` anziché `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Output condizionale

È possibile restituire un valore in modo condizionale. In genere si usa un output condizionale quando si [distribuisce](conditional-resource-deployment.md) una risorsa in modo condizionale. Nell'esempio seguente viene illustrato come restituire in modo condizionale l'ID risorsa per un indirizzo IP pubblico a seconda che sia stato distribuito un nuovo:

# <a name="json"></a>[JSON](#tab/json)

In JSON aggiungere l' `condition` elemento per definire se l'output viene restituito.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Per specificare un output condizionale in bicipite, usare l' `?` operatore. Nell'esempio seguente viene restituito un URL dell'endpoint o una stringa vuota a seconda di una condizione.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Per un esempio semplice di output condizionale, vedere [modello di output condizionale](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Numero dinamico di output

In alcuni scenari non si conosce il numero di istanze di un valore che è necessario restituire quando si crea il modello. È possibile restituire un numero variabile di valori usando l'output iterativo.

# <a name="json"></a>[JSON](#tab/json)

In JSON aggiungere l' `copy` elemento per eseguire l'iterazione di un output.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

L'output iterativo non è attualmente disponibile per bicipite.

---

Per altre informazioni, vedere [iterazione di output nei modelli ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modelli collegati

Nei modelli JSON è possibile distribuire modelli correlati usando [modelli collegati](linked-templates.md). Per recuperare il valore di output da un modello collegato, usare la funzione [Reference](template-functions-resource.md#reference) nel modello padre. La sintassi nel modello padre è la seguente:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Nell'esempio seguente viene illustrato come impostare l'indirizzo IP su un servizio di bilanciamento del carico recuperando un valore da un modello collegato.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Se il nome della proprietà presenta un trattino, racchiudere il nome tra parentesi quadre anziché la notazione del punto.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Non è possibile usare la funzione `reference` nella sezione outputs di un [modello annidato](linked-templates.md#nested-template). Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

Il [modello di indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) crea un indirizzo IP pubblico e restituisce l'ID della risorsa. Il [modello](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) del servizio di bilanciamento del carico si collega al modello precedente. Usa l'ID risorsa nell'output quando si crea il servizio di bilanciamento del carico.

## <a name="modules"></a>Moduli

Nei file Bicipit è possibile distribuire i modelli correlati usando i moduli. Per recuperare un valore di output da un modulo, usare la sintassi seguente:

```bicep
<module-name>.outputs.<property-name>
```

Nell'esempio seguente viene illustrato come impostare l'indirizzo IP su un servizio di bilanciamento del carico recuperando un valore da un modulo. Il nome del modulo è `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Modello di esempio

Il modello seguente non distribuisce alcuna risorsa. Mostra alcuni modi per restituire output di tipi diversi.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Il bicipite non supporta attualmente i cicli.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Ottenere i valori di output

Quando la distribuzione ha esito positivo, i valori di output vengono restituiti automaticamente nei risultati della distribuzione.

Per ottenere i valori di output dalla cronologia di distribuzione, è possibile usare lo script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per gli output, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
