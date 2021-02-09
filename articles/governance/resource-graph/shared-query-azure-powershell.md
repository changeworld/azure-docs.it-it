---
title: 'Guida introduttiva: Creare una query condivisa con Azure PowerShell'
description: Questa guida di avvio rapido descrive la procedura per creare una query condivisa di Resource Graph con Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981545"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Guida introduttiva: Creare una query condivisa di Resource Graph con Azure PowerShell

Questo articolo descrive come creare una query condivisa di Azure Resource Graph usando il modulo [Az.ResourceGraph](/powershell/module/az.resourcegraph) di PowerShell.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Durante la fase di anteprima, è necessario installare il modulo **Az.ResourceGraph** di PowerShell separatamente usando il cmdlet `Install-Module`.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Creare una query condivisa di Resource Graph

Con il modulo di PowerShell **AZ. ResourceGraph** aggiunto all'ambiente desiderato, è possibile creare una query di Resource Graph condivisa. La query condivisa è un oggetto Azure Resource Manager a cui è possibile concedere autorizzazioni o che è possibile eseguire in Azure Resource Graph Explorer. Nella query viene riepilogato il conteggio di tutte le risorse raggruppate in base alla _località_.

1. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) per archiviare la query condivisa di Azure Resource Graph. Questo gruppo di risorse è denominato `resource-graph-queries` e la località è `westus2`.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Creare la query condivisa di Azure Resource Graph usando il modulo di PowerShell **AZ. ResourceGraph** e il cmdlet [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) :

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Elencare le query condivise nel nuovo gruppo di risorse. Il cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) restituisce una matrice di valori.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Per ottenere solo un singolo risultato della query condivisa, usare `Get-AzResourceGraphQuery` con il relativo parametro `Name`.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere la query condivisa di Resource Graph e il gruppo di risorse dall'ambiente di Azure, è possibile usare i comandi seguenti:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata una query condivisa di Resource Graph con Azure PowerShell. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)