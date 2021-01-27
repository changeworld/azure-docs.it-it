---
title: Variabili nei modelli
description: Viene descritto come definire le variabili in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874435"
---
# <a name="variables-in-arm-template"></a>Variabili nel modello ARM

Questo articolo descrive come definire e usare le variabili nel modello di Azure Resource Manager (modello ARM). Per semplificare il modello, è possibile usare le variabili. Anziché ripetere le espressioni complesse nell'intero modello, si definisce una variabile che contiene l'espressione complessa. Quindi, si fa riferimento a tale variabile in base alle esigenze nell'intero modello.

Gestione risorse risolve le variabili prima di avviare le operazioni di distribuzione. Indipendentemente dalla posizione in cui viene usata la variabile nel modello, Resource Manager la sostituisce con il valore risolto.

## <a name="define-variable"></a>Definisci variabile

Quando si definisce una variabile, fornire un valore o un'espressione di modello che viene risolta in un [tipo di dati](template-syntax.md#data-types). È possibile usare il valore di un parametro o di un'altra variabile quando si costruisce la variabile.

È possibile usare le [funzioni modello](template-functions.md) nella dichiarazione di variabile, ma non è possibile usare la funzione [Reference](template-functions-resource.md#reference) o una delle funzioni [elenco](template-functions-resource.md#list) . Queste funzioni ottengono lo stato di runtime di una risorsa e non possono essere eseguite prima della distribuzione quando le variabili vengono risolte.

L'esempio seguente illustra la definizione di una variabile: Crea un valore stringa per il nome di un account di archiviazione. USA diverse funzioni di modello per ottenere un valore di parametro e lo concatena a una stringa univoca.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Usare la variabile

Nel modello si fa riferimento al valore per il parametro usando la funzione [variables](template-functions-deployment.md#variables) . Nell'esempio seguente viene illustrato come utilizzare la variabile per una proprietà della risorsa.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Modello di esempio

Il modello seguente non distribuisce alcuna risorsa. Mostra solo alcuni modi per dichiarare le variabili.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variabili di configurazione

È possibile definire variabili che contengono valori correlati per la configurazione di un ambiente. La variabile viene definita come oggetto con i valori. Nell'esempio seguente viene illustrato un oggetto che contiene i valori per due ambienti, **test** e **Prod**. Si passa uno di questi valori durante la distribuzione.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per le variabili, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per consigli sulla creazione di variabili, vedere procedure consigliate [-variabili](template-best-practices.md#variables).
* Per un modello di esempio che assegna regole di sicurezza a un gruppo di sicurezza di rete, vedere [regole di sicurezza di rete](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e file di [parametri](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
