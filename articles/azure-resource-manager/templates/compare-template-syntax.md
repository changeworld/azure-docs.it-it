---
title: Convertire Azure Resource Manager modelli tra JSON e bicipite
description: Confronta Azure Resource Manager modelli sviluppati con JSON e bicipite.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745106"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Confronto tra JSON e bicipite per i modelli

Questo articolo mette a confronto la sintassi bicipite con la sintassi JSON per i modelli di Azure Resource Manager (modelli ARM). Nella maggior parte dei casi, il bicipite offre una sintassi meno dettagliata dell'equivalente in JSON.

## <a name="syntax-equivalents"></a>Equivalenti di sintassi

Se si ha familiarità con l'uso di JSON per sviluppare modelli ARM, usare la tabella seguente per informazioni sulla sintassi equivalente per bicipite.

| Scenario | Modello ARM | Bicep |
| -------- | ------------ | ----- |
| Creare un'espressione | `"[func()]"` | `func()` |
| Ottieni valore parametro | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Ottieni valore variabile | `[variables('exampleVar'))]` | `exampleVar` |
| Concatenare le stringhe | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Imposta proprietà risorsa | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| Restituisce l'operatore AND logico | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Ottenere l'ID risorsa della risorsa nel modello | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Ottenere la proprietà dalla risorsa nel modello | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Impostare un valore in modo condizionale | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Separare una soluzione in più file | Usare i modelli collegati | Usare i moduli |
| Impostare l'ambito di destinazione della distribuzione | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Imposta dipendenza | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | Si basano sul rilevamento automatico delle dipendenze o sull'impostazione manuale di una dipendenza con `dependsOn: [ stg ]` |

Per dichiarare il tipo e la versione di una risorsa, usare il comando seguente in bicipite:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

Anziché la sintassi equivalente in JSON:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Consigli

* Quando possibile, evitare di usare le funzioni di [riferimento](template-functions-resource.md#reference) e [resourceId](template-functions-resource.md#resourceid) nel file bicipite. Quando si fa riferimento a una risorsa nella stessa distribuzione bicipite, usare invece l'identificatore di risorsa. Ad esempio, se è stata distribuita una risorsa nel file bicipite con `stg` come identificatore di risorsa, usare la sintassi like `stg.id` o `stg.properties.primaryEndpoints.blob` per ottenere i valori delle proprietà. Usando l'identificatore di risorsa si crea una dipendenza implicita tra le risorse. Non è necessario impostare in modo esplicito la dipendenza con la proprietà dependsOn.
* Usare l'involucro coerente per gli identificatori. Se non si è certi del tipo di maiuscole e minuscole da usare, provare la convenzione Camel. Ad esempio: `param myCamelCasedParameter string`.
* Aggiungere una descrizione a un parametro solo quando la descrizione fornisce informazioni essenziali agli utenti. È possibile utilizzare `//` i commenti per alcune informazioni.

## <a name="decompile-json-to-bicep"></a>Decompilare JSON in bicipite

L'interfaccia della riga di comando bicipite fornisce un comando per decompilare qualsiasi modello ARM esistente in un file bicipite. Per decompilare un file JSON, usare: `bicep decompile "path/to/file.json"`

Questo comando fornisce un punto di partenza per la creazione di bicipiti, ma il comando non funziona per tutti i modelli. Il comando potrebbe non riuscire o potrebbe essere necessario risolvere i problemi dopo la decompilazione. Attualmente, il comando presenta le limitazioni seguenti:

* Non è possibile decompilare i modelli che usano i cicli di copia.
* I modelli annidati possono essere decompilati solo se usano l'ambito di valutazione dell'espressione "Inner".

È possibile esportare il modello per un gruppo di risorse e passarlo direttamente al comando bicipit decompile. Nell'esempio seguente viene illustrato come decompilare un modello esportato.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

[Esportare il modello](export-template-portal.md) tramite il portale. Usare `bicep decompile <filename>` nel file scaricato.

---

## <a name="build-json-from-bicep"></a>Crea JSON da bicipite

L'interfaccia della riga di comando bicipite offre anche un comando per convertire il bicipite in JSON. Per compilare un file JSON, usare: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Visualizzazione affiancata

Il [parco bicipite](https://aka.ms/bicepdemo) consente di visualizzare side-by-Side i file JSON e bicipite equivalenti. È possibile selezionare un modello di esempio per visualizzare entrambe le versioni. In alternativa, selezionare `Decompile` per caricare il proprio modello JSON e visualizzare il file bicipite equivalente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul progetto bicipite, vedere [progetto bicipite](https://github.com/Azure/bicep).
