---
title: Creare e distribuire una specifica di modello
description: Informazioni su come creare una specifica di modello da un modello di Resource Manager e quindi distribuire la specifica di modello in un gruppo di risorse nella sottoscrizione.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518958"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Avvio rapido: Creare e distribuire una specifica di modello (anteprima)

Questo argomento di avvio rapido illustra come creare un pacchetto di un modello di Azure Resource Manager in una [specifica di modello](template-specs.md) e quindi distribuire tale specifica. La specifica di modello contiene un modello di Resource Manager che distribuisce un account di archiviazione.

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La funzionalità Specifiche di modello è attualmente in anteprima. Per usarla con Azure PowerShell, è necessario installare la [versione 5.0.0 o successiva](/powershell/azure/install-az-ps). Per usarla con l'interfaccia della riga di comando di Azure, usare la [versione 2.14.2 o successiva](/cli/azure/install-azure-cli).

## <a name="create-template-spec"></a>Creare la specifica di modello

La specifica di modello è un tipo di risorsa denominato **Microsoft.Resources/templateSpecs**. Per creare la specifica di modello, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o un modello di Resource Manager. In tutte le opzioni è necessario un modello di Resource Manager incluso nel pacchetto della specifica di modello.

Con PowerShell e l'interfaccia della riga di comando, il modello di Resource Manager viene passato come parametro al comando. Con il modello di Resource Manager, il modello stesso da includere nel pacchetto della specifica di modello è incorporato nella definizione della specifica di modello.

Queste opzioni sono mostrate di seguito.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella parte superiore della schermata, in **Cerca risorse, servizi e documentazione**, immettere **specifiche di modello** e quindi selezionare **Specifiche di modello**.
1. Selezionare **Crea specifica di modello**.
1. Selezionare o immettere i valori seguenti:

    - **Nome**: immettere un nome per la specifica di modello.  Ad esempio: **storageSpec**
    - **Sottoscrizione**: selezionare una sottoscrizione di Azure usata per creare la specifica di modello.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere un nome per il nuovo gruppo di risorse.  Ad esempio: **templateSpecRG**.
    - **Località**: selezionare una località per il gruppo di risorse. Ad esempio: **Stati Uniti occidentali 2**.
    - **Versione**: immettere la versione della specifica di modello. Ad esempio: **1.0** o **v1.0**.

1. Selezionare **Avanti: Modifica modello**.
1. Sostituire il contenuto del modello con il codice JSON seguente:

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    È il modello che verrà incluso nel pacchetto della specifica di modello.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Quando si crea una specifica di modello con PowerShell, è possibile passare un modello locale. Copiare il modello seguente e salvarlo in locale in un file denominato **azuredeploy.json**. In questo argomento di avvio rapido si presuppone di aver salvato il modello nel percorso **c:\Templates\azuredeploy.json**, ma è possibile usare un percorso qualsiasi.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Creare un nuovo gruppo di risorse per contenere la specifica di modello.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Quindi, creare la specifica di modello in quel gruppo di risorse. Assegnare alla nuova specifica di modello il nome **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Quando si crea una specifica di modello con l'interfaccia della riga di comando, è possibile passare un modello locale. Copiare il modello seguente e salvarlo in locale in un file denominato **azuredeploy.json**. In questo argomento di avvio rapido si presuppone di aver salvato il modello nel percorso **c:\Templates\azuredeploy.json**, ma è possibile usare un percorso qualsiasi.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Creare un nuovo gruppo di risorse per contenere la specifica di modello.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Quindi, creare la specifica di modello in quel gruppo di risorse. Assegnare alla nuova specifica di modello il nome **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Modello ARM](#tab/azure-resource-manager)

1. Quando si usa un modello di Resource Manager per creare la specifica di modello, il modello viene incorporato nella definizione della risorsa. Copiare il modello seguente e salvarlo in locale con il nome **azuredeploy.json**. In questo argomento di avvio rapido si presuppone di aver salvato il modello nel percorso **c:\Templates\azuredeploy.json**, ma è possibile usare un percorso qualsiasi.

    > [!NOTE]
    > Nel modello incorporato tutte le [espressioni del modello](template-expressions.md) devono essere precedute da un carattere di escape costituito da una seconda parentesi quadra aperta. Usare `"[[` invece di `"[`. Le matrici JSON usano ancora un'unica parentesi quadra aperta.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Usare l'interfaccia della riga di comando di Azure oppure PowerShell per creare un nuovo gruppo di risorse.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Distribuire il modello con l'interfaccia della riga di comando di Azure o con PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

È ora possibile distribuire la specifica di modello. La distribuzione della specifica di modello è analoga alla distribuzione del modello che contiene, con la differenza che occorre passare l'ID risorsa della specifica di modello in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Usare gli stessi comandi di distribuzione e, se necessario, passare i valori dei parametri della specifica di modello.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel portale di Azure aprire il gruppo di risorse creato nell'ultima procedura.  Ad esempio, **templateSpecRG**.
1. Selezionare la specifica di modello creata. Ad esempio, **storageSpec**.
1. Selezionare **Distribuisci**.
1. Selezionare o immettere i valori seguenti:

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure in cui creare la risorsa.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere **storageRG**.
    - **Tipo di account di archiviazione**: selezionare **Standard_GRS**.

    Viene creato un nuovo gruppo di risorse, in cui viene distribuito il modello all'interno della specifica di modello.

1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare un gruppo di risorse per contenere il nuovo account di archiviazione.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Ottenere l'ID risorsa della specifica di modello.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Distribuire la specifica di modello.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Specificare i parametri usando la stessa procedura adottata per un modello di Azure Resource Manager. Ridistribuire la specifica di modello con un parametro per il tipo di account di archiviazione.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Creare un gruppo di risorse per contenere il nuovo account di archiviazione.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Ottenere l'ID risorsa della specifica di modello.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > È stato rilevato un problema noto relativo al recupero di un ID specifica di modello e all'assegnazione di tale ID a una variabile in Windows PowerShell.

1. Distribuire la specifica di modello.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Specificare i parametri usando la stessa procedura adottata per un modello di Azure Resource Manager. Ridistribuire la specifica di modello con un parametro per il tipo di account di archiviazione.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Modello ARM](#tab/azure-resource-manager)

1. Copiare il modello seguente e salvarlo in locale in un file denominato **storage.json**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Usare l'interfaccia della riga di comando di Azure oppure PowerShell per creare un nuovo gruppo di risorse per l'account di archiviazione.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Distribuire il modello con l'interfaccia della riga di comando di Azure o con PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Concedere l'accesso

Se si vuole consentire ad altri utenti dell'organizzazione di distribuire la specifica di modello, è necessario concedere loro l'accesso in lettura. È possibile assegnare il ruolo Lettore a un gruppo di Azure AD per il gruppo di risorse che contiene le specifiche di modello da condividere. Per altre informazioni, vedere [Esercitazione: Concedere a un gruppo l'accesso alle risorse di Azure con Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse distribuite in questo argomento di avvio rapido, eliminare entrambi i gruppi di risorse creati.

1. Nel portale di Azure selezionare Gruppo di risorse nel menu a sinistra.

1. Immettere il nome del gruppo di risorse (templateSpecRG e storageRG) nel campo Filtra per nome.

1. Selezionare il nome del gruppo di risorse.

1. Selezionare Elimina gruppo di risorse nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare una specifica di modello che include modelli collegati, vedere [Creare una specifica di modello di un modello collegato](template-specs-create-linked.md).
