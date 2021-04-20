---
title: Spostare aree per le risorse in Microsoft.Resources
description: Illustra come spostare le risorse presenti nello spazio dei nomi Microsoft.Resources in nuove aree.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725873"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Spostare le risorse Microsoft.Resources in una nuova area

Potrebbe essere necessario spostare una risorsa esistente in una nuova area. Questo articolo illustra come spostare due tipi di risorse, templateSpecs e deploymentScripts, nello spazio dei nomi Microsoft.Resources.

## <a name="move-template-specs-to-new-region"></a>Spostare le specifiche del modello in una nuova area

Se si dispone di [una specifica di modello](../templates/template-specs.md) in un'area e si vuole spostarla in una nuova area, è possibile esportare la specifica del modello e ridistribuirla.

1. Usare il comando per esportare una specifica di modello esistente. Per i valori dei parametri, specificare i valori che corrispondono alla specifica di modello che si vuole esportare.

   Per Azure PowerShell usare:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Per l'interfaccia della riga di comando di Azure usare:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Usare la specifica del modello esportato per creare una nuova specifica di modello. Gli esempi seguenti `westus` illustrano per la nuova area, ma è possibile specificare l'area desiderata.

   Per Azure PowerShell usare:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Per l'interfaccia della riga di comando di Azure usare:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Spostare gli script di distribuzione in una nuova area

1. Selezionare il gruppo di risorse che contiene lo script di distribuzione che si vuole spostare in una nuova area.

1. [Esportare il modello](../templates/export-template-portal.md). Durante l'esportazione, selezionare lo script di distribuzione e tutte le altre risorse necessarie.

1. Nel modello esportato eliminare le proprietà seguenti:

   * TenantId
   * principalId
   * clientId

1. Il modello esportato ha un valore hardcoded per l'area dello script di distribuzione.

   ```json
   "location": "westus2",
   ```

   Modificare il modello per consentire un parametro per l'impostazione della posizione. Per altre informazioni, vedere Set [resource location in ARM template (Impostare la posizione delle risorse nel modello arm)](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Distribuire il modello esportato](../templates/deploy-powershell.md) e specificare una nuova area per lo script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sullo spostamento delle risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione, vedere Spostare le risorse [in un nuovo gruppo di risorse o in una nuova sottoscrizione.](move-resource-group-and-subscription.md)
* Per informazioni sullo spostamento delle risorse in una nuova area, vedere [Spostare le risorse tra aree.](move-resources-overview.md#move-resources-across-regions)
