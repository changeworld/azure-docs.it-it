---
title: Variabili nei modelli
description: Viene descritto come definire le variabili in un modello di Azure Resource Manager (ARM template) e nel file bicipite.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 3ab14c9acfcc2d6c9edd23fb3bc4d876cd5ac756
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123378"
---
# <a name="variables-in-arm-templates"></a>Variabili nei modelli ARM

Questo articolo descrive come definire e usare le variabili nel modello di Azure Resource Manager (ARM template) o nel file bicipite. Per semplificare il modello, è possibile usare le variabili. Anziché ripetere le espressioni complesse nell'intero modello, si definisce una variabile che contiene l'espressione complessa. Quindi, usare tale variabile in base alle esigenze nell'intero modello.

Gestione risorse risolve le variabili prima di avviare le operazioni di distribuzione. Indipendentemente dalla posizione in cui viene usata la variabile nel modello, Resource Manager la sostituisce con il valore risolto.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Definisci variabile

Quando si definisce una variabile, non si specifica un [tipo di dati](data-types.md) per la variabile. Specificare invece un valore o un'espressione di modello. Il tipo di variabile viene dedotto dal valore risolto. Nell'esempio seguente viene impostata una variabile su una stringa.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

È possibile usare il valore di un parametro o di un'altra variabile quando si costruisce la variabile.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

È possibile utilizzare le [funzioni modello](template-functions.md) per costruire il valore della variabile.

Nell'esempio seguente viene creato un valore stringa per un nome di account di archiviazione. USA diverse funzioni di modello per ottenere un valore di parametro e lo concatena a una stringa univoca.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

Nei modelli JSON non è possibile usare la funzione [Reference](template-functions-resource.md#reference) o una delle funzioni [elenco](template-functions-resource.md#list) nella dichiarazione di variabile. Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando le variabili vengono risolte.

Nei file Bicipit, le funzioni di riferimento e elenco sono valide quando si dichiara una variabile.

## <a name="use-variable"></a>Usare la variabile

Nell'esempio seguente viene illustrato come utilizzare la variabile per una proprietà della risorsa.

# <a name="json"></a>[JSON](#tab/json)

In un modello JSON si fa riferimento al valore per la variabile tramite la funzione [variables](template-functions-deployment.md#variables) .

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

In un file bicipite, si fa riferimento al valore per la variabile fornendo il nome della variabile.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Modello di esempio

Il modello seguente non distribuisce alcuna risorsa. Vengono illustrati alcuni modi per dichiarare variabili di tipi diversi.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Il bicipite non supporta attualmente i cicli.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Variabili di configurazione

È possibile definire variabili che contengono valori correlati per la configurazione di un ambiente. La variabile viene definita come oggetto con i valori. Nell'esempio seguente viene illustrato un oggetto che contiene i valori per due ambienti, **test** e **Prod**. Passare uno di questi valori durante la distribuzione.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le variabili, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per consigli sulla creazione di variabili, vedere procedure consigliate [-variabili](template-best-practices.md#variables).
* Per un modello di esempio che assegna regole di sicurezza a un gruppo di sicurezza di rete, vedere [regole di sicurezza di rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e file di [parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
