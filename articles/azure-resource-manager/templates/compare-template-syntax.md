---
title: Confrontare la sintassi per i modelli di Azure Resource Manager in JSON e bicipite
description: Confronta Azure Resource Manager modelli sviluppati con JSON e bicipite e Mostra come eseguire la conversione tra le lingue.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461795"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Confronto tra JSON e bicipite per i modelli

Questo articolo mette a confronto la sintassi bicipite con la sintassi JSON per i modelli di Azure Resource Manager (modelli ARM). Nella maggior parte dei casi, il bicipite offre una sintassi meno dettagliata dell'equivalente in JSON.

Se si ha familiarità con l'uso di JSON per sviluppare modelli ARM, usare gli esempi seguenti per apprendere la sintassi equivalente per bicipite.

## <a name="expressions"></a>Espressioni

Per creare un'espressione:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parametri

Per dichiarare un parametro con un valore predefinito:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Per ottenere un valore di parametro:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variabili

Per dichiarare una variabile:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Per ottenere un valore della variabile:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Stringhe

Per concatenare le stringhe:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Operatori logici

Per restituire l'operatore **and** logico:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Per impostare un valore in modo condizionale:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Ambito della distribuzione

Per impostare l'ambito di destinazione della distribuzione:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Risorse

Per dichiarare una risorsa:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Per distribuire una risorsa in modo condizionale:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Per impostare la proprietà della risorsa:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Per ottenere l'ID risorsa della risorsa nel modello:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Loop

Per eseguire l'iterazione degli elementi in una matrice o in un conteggio:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Dipendenze delle risorse

Per impostare la dipendenza tra le risorse:

Per bicipite, utilizzare il rilevamento automatico delle dipendenze o impostare manualmente la dipendenza.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Risorse di riferimento

Per ottenere una proprietà da una risorsa nel modello:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Per ottenere una proprietà da una risorsa esistente che non viene distribuita nel modello:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Output

Per restituire una proprietà da una risorsa nel modello:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Riutilizzo del codice

Per separare una soluzione in più file:

* Per bicipite usare i [moduli](bicep-tutorial-add-modules.md).
* Per JSON usare i [modelli collegati](linked-templates.md).

## <a name="recommendations"></a>Consigli

* Quando possibile, evitare di usare le funzioni di [riferimento](template-functions-resource.md#reference) e [resourceId](template-functions-resource.md#resourceid) nel file bicipite. Quando si fa riferimento a una risorsa nella stessa distribuzione bicipite, usare invece l'identificatore di risorsa. Ad esempio, se è stata distribuita una risorsa nel file bicipite con `stg` come identificatore di risorsa, usare la sintassi like `stg.id` o `stg.properties.primaryEndpoints.blob` per ottenere i valori delle proprietà. Usando l'identificatore di risorsa si crea una dipendenza implicita tra le risorse. Non è necessario impostare in modo esplicito la dipendenza con la proprietà dependsOn.
* Se la risorsa non viene distribuita nel file bicipite, è comunque possibile ottenere un riferimento simbolico alla risorsa usando la parola chiave **esistente** .
* Usare l'involucro coerente per gli identificatori. Se non si è certi del tipo di maiuscole e minuscole da usare, provare la convenzione Camel. Ad esempio: `param myCamelCasedParameter string`.
* Aggiungere una descrizione a un parametro solo quando la descrizione fornisce informazioni essenziali agli utenti. È possibile utilizzare `//` i commenti per alcune informazioni.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul bicipite, vedere l' [esercitazione sul bicipite](./bicep-tutorial-create-first-bicep.md).
* Per informazioni sulla conversione dei modelli tra le lingue, vedere [conversione di modelli ARM tra JSON e bicipite](bicep-decompile.md).
