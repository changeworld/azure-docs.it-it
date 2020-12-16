---
title: "Avvio rapido: Creare un account Azure Purview mediante Azure PowerShell o l'interfaccia della riga di comando di Azure (anteprima)"
description: Questo argomento di avvio rapido illustra come creare un account Azure Purview mediante Azure PowerShell o l'interfaccia della riga di comando di Azure.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 7db546ec9038403bd9e34f637e9d9de368277d52
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400303"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Avvio rapido: Creare un account Azure Purview mediante Azure PowerShell o l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

In questo argomento di avvio rapido si creerà un account Azure Purview mediante Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* L'account utente usato per accedere ad Azure deve essere un membro del ruolo Collaboratore o Proprietario oppure un amministratore della sottoscrizione di Azure.

* Un [tenant di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

* Per distribuire il modello, installare Azure PowerShell o l'interfaccia della riga di comando di Azure nel computer client: [Distribuzione dalla riga di comando](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="configure-your-subscription"></a>Configurare la sottoscrizione

Se necessario, seguire questa procedura per configurare la sottoscrizione in modo da consentire l'esecuzione di Azure Purview nella sottoscrizione:

   1. Nel portale di Azure cercare e selezionare **Sottoscrizioni**.

   1. Nell'elenco delle sottoscrizioni selezionare quella che si vuole usare. È necessaria l'autorizzazione di accesso amministrativo per la sottoscrizione.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot che illustra come selezionare una sottoscrizione nel portale di Azure.":::

   1. Selezionare **Provider di risorse** per la propria sottoscrizione. Nel riquadro **Provider di risorse** cercare e registrare tutti e tre i provider di risorse: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se non sono registrati, registrarli selezionando **Registra**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot che illustra come registrare il provider di risorse Microsoft.Purview nel portale di Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Creare un'istanza dell'account Azure Purview

1. Accedere con le credenziali di Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Se si dispone di più sottoscrizioni di Azure, selezionare quella da usare:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Creare un gruppo di risorse per l'account Purview. Se se ne ha già uno, è possibile ignorare questo passaggio:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Creare un file di modello di Purview, ad esempio `purviewtemplate.json`. È possibile aggiornare `name`, `location` e `capacity` (`4` o `16`):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Distribuire il modello di Purview

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Il comando di distribuzione restituisce risultati. Cercare `ProvisioningState` per verificare se la distribuzione è riuscita.
    
## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Purview.

Passare all'articolo successivo per informazioni su come consentire agli utenti di accedere all'account Azure Purview. 

> [!div class="nextstepaction"]
> [Aggiungere utenti all'account Azure Purview](catalog-permissions.md)