---
title: Sintassi e espressioni del modello
description: Descrive la sintassi JSON dichiarativa per i modelli di Azure Resource Manager (modelli ARM).
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797044"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>Sintassi ed espressioni nei modelli ARM

La sintassi di base del modello di Azure Resource Manager (modello ARM) è JavaScript Object Notation (JSON). Tuttavia, è possibile usare le espressioni per estendere i valori JSON disponibili all'interno del modello.  Le espressioni iniziano e terminano con parentesi quadre: `[` e `]` rispettivamente. Il valore dell'espressione viene valutato quando viene distribuito il modello. Un'espressione può restituire una stringa, un intero, un valore booleano, una matrice o un oggetto.

Un'espressione modello non può superare i 24.576 caratteri.

## <a name="use-functions"></a>Usare le funzioni

Azure Resource Manager fornisce [funzioni](template-functions.md) che è possibile utilizzare in un modello. Nell'esempio seguente viene illustrata un'espressione che utilizza una funzione nel valore predefinito di un parametro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

All'interno dell'espressione, la sintassi `resourceGroup()` chiama una delle funzioni che Gestione risorse fornisce per l'utilizzo all'interno di un modello. In questo caso, si tratta della funzione [resourceGroup](template-functions-resource.md#resourcegroup) . Proprio come in JavaScript, le chiamate di funzione sono formattate come `functionName(arg1,arg2,arg3)`. La sintassi `.location` Recupera una proprietà dall'oggetto restituito dalla funzione.

Le funzioni del modello e i relativi parametri non hanno la distinzione tra maiuscole e minuscole. Ad esempio, Gestione risorse risolve `variables('var1')` e `VARIABLES('VAR1')` come lo stesso. Quando viene valutato, a meno che la funzione non modifichi espressamente la distinzione tra maiuscole e minuscole, ad esempio `toUpper` o `toLower` , la funzione conserva il caso. Alcuni tipi di risorse possono presentare requisiti del case distinti rispetto alla valutazione delle funzioni.

Per passare un valore di stringa come parametro a una funzione, usare le virgolette singole.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

La maggior parte delle funzioni funziona allo stesso modo se distribuite in un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Le funzioni seguenti presentano restrizioni basate sull'ambito:

* [resourceGroup](template-functions-resource.md#resourcegroup) : può essere usato solo nelle distribuzioni in un gruppo di risorse.
* [resourceId](template-functions-resource.md#resourceid) : può essere usato in qualsiasi ambito, ma i parametri validi cambiano a seconda dell'ambito.
* [Subscription](template-functions-resource.md#subscription) -può essere usato solo nelle distribuzioni in un gruppo di risorse o in una sottoscrizione.

## <a name="escape-characters"></a>Caratteri di escape

Per avere una stringa letterale che inizia con una parentesi quadra aperta `[` e termina con una parentesi quadra chiusa `]` , ma non deve essere interpretata come espressione, aggiungere una parentesi aggiuntiva per avviare la stringa con `[[` . Ad esempio, la variabile:

```json
"demoVar1": "[[test value]"
```

Viene risolto in `[test value]` .

Tuttavia, se la stringa letterale non termina con una parentesi, non usare caratteri di escape per la prima parentesi. Ad esempio, la variabile:

```json
"demoVar2": "[test] value"
```

Viene risolto in `[test] value` .

Per eseguire l'escape delle virgolette doppie in un'espressione, ad esempio l'aggiunta di un oggetto JSON nel modello, usare la barra rovesciata.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Quando si passano i valori dei parametri, l'uso di caratteri di escape dipende dalla posizione in cui viene specificato il valore del parametro. Se si imposta un valore predefinito nel modello, è necessario disporre della parentesi sinistra aggiuntiva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Se si usa il valore predefinito, il modello restituisce `[test value]` .

Tuttavia, se si passa un valore di parametro tramite la riga di comando, i caratteri vengono interpretati letteralmente. Distribuzione del modello precedente con:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Restituisce `[[test value]`. Usare invece:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

La stessa formattazione si applica quando si passano valori in da un file di parametri. I caratteri vengono interpretati letteralmente. Quando viene usato con il modello precedente, il file di parametri seguente restituisce `[test value]` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Valori Null

Per impostare una proprietà su Null, è possibile usare `null` o `[json('null')]`. La [funzione JSON](template-functions-object.md#json) restituisce un oggetto vuoto quando si specifica `null` come parametro. In entrambi i casi, Gestione risorse modelli lo considerano come se la proprietà non fosse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Passaggi successivi

* Per l'elenco completo delle funzioni del modello, vedere [funzioni del modello ARM](template-functions.md).
* Per altre informazioni sui file modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
