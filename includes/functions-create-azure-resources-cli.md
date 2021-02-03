---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500425"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Prima di distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Un [gruppo di risorse](../articles/azure-resource-manager/management/overview.md), che è un contenitore logico per le risorse correlate.
- Un [account di archiviazione](../articles/storage/common/storage-account-create.md), usato per gestire lo stato e altre informazioni sulle funzioni.
- Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Usare i comandi seguenti per creare questi elementi. Sono supportati sia l'interfaccia della riga di comando di Azure che PowerShell.

1. Se non è già stato fatto, accedere ad Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Il comando [az login](/cli/azure/reference-index#az_login) consente di accedere all'account Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) consente di accedere all'account Azure.

    ---

1. Creare un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Il comando [az group create](/cli/azure/group#az_group_create) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal comando `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Creare un account di archiviazione per utilizzo generico nel gruppo di risorse e nell'area:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) crea l'account di archiviazione. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crea l'account di archiviazione.

    ---

    Nell'esempio precedente sostituire `<STORAGE_NAME>` con un nome appropriato e univoco in Archiviazione di Azure. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un account per utilizzo generico, che è [supportato da Funzioni](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
