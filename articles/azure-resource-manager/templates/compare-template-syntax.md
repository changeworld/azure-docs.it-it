---
title: Confrontare la sintassi per i modelli di Azure Resource Manager in JSON e bicipite
description: Confronta Azure Resource Manager modelli sviluppati con JSON e bicipite e Mostra come eseguire la conversione tra le lingue.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418046"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Confronto tra JSON e bicipite per i modelli

Questo articolo mette a confronto la sintassi bicipite con la sintassi JSON per i modelli di Azure Resource Manager (modelli ARM). Nella maggior parte dei casi, il bicipite offre una sintassi meno dettagliata dell'equivalente in JSON.

## <a name="syntax-equivalents"></a>Equivalenti di sintassi

Se si ha familiarità con l'uso di JSON per sviluppare modelli ARM, usare la tabella seguente per informazioni sulla sintassi equivalente per bicipite.

| Scenario | Bicep | JSON |
| -------- | ------------ | ----- |
| Creare un'espressione | `func()` | `"[func()]"` |
| Ottieni valore parametro | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Ottieni valore variabile | `exampleVar` | `[variables('exampleVar'))]` |
| Concatenare le stringhe | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Imposta proprietà risorsa | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Restituisce l'operatore AND logico | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Ottenere l'ID risorsa della risorsa nel modello | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Ottenere la proprietà dalla risorsa nel modello | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Impostare un valore in modo condizionale | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Separare una soluzione in più file | Usare i moduli | Usare i modelli collegati |
| Impostare l'ambito di destinazione della distribuzione | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Imposta dipendenza | Si basano sul rilevamento automatico delle dipendenze o sull'impostazione manuale di una dipendenza con `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Dichiarazione di risorsa | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Consigli

* Quando possibile, evitare di usare le funzioni di [riferimento](template-functions-resource.md#reference) e [resourceId](template-functions-resource.md#resourceid) nel file bicipite. Quando si fa riferimento a una risorsa nella stessa distribuzione bicipite, usare invece l'identificatore di risorsa. Ad esempio, se è stata distribuita una risorsa nel file bicipite con `stg` come identificatore di risorsa, usare la sintassi like `stg.id` o `stg.properties.primaryEndpoints.blob` per ottenere i valori delle proprietà. Usando l'identificatore di risorsa si crea una dipendenza implicita tra le risorse. Non è necessario impostare in modo esplicito la dipendenza con la proprietà dependsOn.
* Usare l'involucro coerente per gli identificatori. Se non si è certi del tipo di maiuscole e minuscole da usare, provare la convenzione Camel. Ad esempio: `param myCamelCasedParameter string`.
* Aggiungere una descrizione a un parametro solo quando la descrizione fornisce informazioni essenziali agli utenti. È possibile utilizzare `//` i commenti per alcune informazioni.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul bicipite, vedere l' [esercitazione sul bicipite](./bicep-tutorial-create-first-bicep.md).
* Per informazioni sulla conversione dei modelli tra le lingue, vedere [conversione di modelli ARM tra JSON e bicipite](bicep-decompile.md).
