---
title: Creare e distribuire una specifica di modello
description: Informazioni su come creare una specifica di modello da un modello di Resource Manager e quindi distribuire la specifica di modello in un gruppo di risorse nella sottoscrizione.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 1d3e7c6ee6d19d4d2cd3828d5abf95ccb5457e76
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511349"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Avvio rapido: Creare e distribuire una specifica di modello (anteprima)

Questo argomento di avvio rapido illustra come creare un pacchetto di un modello di Azure Resource Manager in una [specifica di modello](template-specs.md) e quindi distribuire tale specifica. La specifica di modello contiene un modello di Resource Manager che distribuisce un account di archiviazione.

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La funzionalità Specifiche di modello è attualmente in anteprima. Per usarla con Azure PowerShell, è necessario installare la [versione 5.0.0 o successiva](/powershell/azure/install-az-ps). Per usarla con l'interfaccia della riga di comando di Azure, usare la [versione 2.14.2 o successiva](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Creare il modello

Per creare una specifica di modello si userà un modello locale. Copiare il modello seguente e salvarlo in locale in un file denominato **azuredeploy.json**. In questo argomento di avvio rapido si presuppone di aver salvato il modello nel percorso **c:\Templates\azuredeploy.json**, ma è possibile usare un percorso qualsiasi.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Creare la specifica di modello

La specifica di modello è un tipo di risorsa denominato `Microsoft.Resources/templateSpecs`. Per creare una specifica di modello è possibile usare PowerShell, l'interfaccia della riga di comando di Azure, il portale o un modello di Resource Manager.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare un nuovo gruppo di risorse per contenere la specifica di modello.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Creare la specifica di modello in quel gruppo di risorse. Assegnare alla nuova specifica di modello il nome **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Creare un nuovo gruppo di risorse per contenere la specifica di modello.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Creare la specifica di modello in quel gruppo di risorse. Assegnare alla nuova specifica di modello il nome **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **specifiche di modello**. Selezionare **Specifiche di modello** dalle opzioni disponibili.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="cercare specifiche di modello":::

1. Selezionare **Importa modello**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="importa modello":::

1. Selezionare l'icona della cartella.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="apri cartella":::

1. Passare al modello locale salvato e selezionarlo. Selezionare **Open** (Apri).
1. Selezionare **Importa**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="selezionare l'opzione Importa":::

1. Specificare i valori seguenti:

    - **Nome**: immettere un nome per la specifica di modello.  Ad esempio: **storageSpec**
    - **Sottoscrizione**: selezionare una sottoscrizione di Azure usata per creare la specifica di modello.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere un nome per il nuovo gruppo di risorse.  Ad esempio: **templateSpecRG**.
    - **Località**: selezionare una località per il gruppo di risorse. Ad esempio: **Stati Uniti occidentali 2**.
    - **Versione**: immettere la versione della specifica di modello. Usare **1.0**.

1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

# <a name="arm-template"></a>[Modello ARM](#tab/azure-resource-manager)

> [!NOTE]
> Invece di usare un modello di Resource Manager, per creare la specifica di modello è consigliabile usare PowerShell o l'interfaccia della riga di comando. Questi strumenti convertono automaticamente i modelli collegati in artefatti connessi al modello principale. Quando si usa un modello di Resource Manager per creare la specifica di modello, è necessario aggiungere manualmente i modelli collegati come artefatti, operazione che può essere complicata.

1. Quando si usa un modello di Resource Manager per creare la specifica di modello, il modello viene incorporato nella definizione della risorsa. È necessario apportare alcune modifiche al modello locale. Copiare il modello seguente e salvarlo in locale con il nome **azuredeploy.json**.

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

Per distribuire una specifica di modello, usare gli stessi comandi di distribuzione usati per distribuire un modello. Passare l'ID risorsa della specifica di modello da distribuire.

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
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
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

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare la specifica di modello creata.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="selezionare la specifica di modello":::

1. Selezionare **Distribuisci**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="distribuire la specifica di modello":::

1. Specificare i valori seguenti:

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure in cui creare la risorsa.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere **storageRG**.
    - **Tipo di account di archiviazione**: selezionare **Standard_GRS**.

1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

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

## <a name="update-template"></a>Aggiornare il modello

Si supponga di aver identificato una modifica da apportare al modello nella specifica di modello. Il modello seguente è analogo al modello precedente, con la differenza che aggiunge un prefisso al nome dell'account di archiviazione. Copiare il modello seguente e aggiornare il file azuredeploy.json.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Aggiornare la versione della specifica di modello

Invece di creare una nuova specifica di modello per il modello modificato, aggiungere una nuova versione denominata `2.0` alla specifica di modello esistente. Gli utenti possono scegliere quale delle due versioni distribuire.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Creare una nuova versione per la specifica di modello.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Per distribuire la nuova versione, ottenere l'ID della risorsa per la versione `2.0`.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Distribuire tale versione. Fornire un prefisso per il nome account di archiviazione.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Creare una nuova versione per la specifica di modello.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Per distribuire la nuova versione, ottenere l'ID della risorsa per la versione `2.0`.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Distribuire tale versione. Fornire un prefisso per il nome account di archiviazione.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella specifica di modello selezionare **Crea nuova versione**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="Crea nuova versione":::

1. Assegnare alla nuova versione il nome `2.0` e, se si vuole, aggiungere note. Selezionare **Modifica modello**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="Assegnare un nome alla nuova versione":::

1. Sostituire il contenuto del modello con il modello aggiornato. Selezionare **Verifica e salva**.
1. Selezionare **Save changes** (Salva modifiche).

1. Per distribuire la nuova versione, selezionare **Versioni**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="Elenco delle versioni":::

1. Selezionare i tre puntini in corrispondenza della versione che si vuole distribuire e quindi selezionare **Distribuisci**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="Selezionare la versione da distribuire":::

1. Compilare i campi come è stato fatto per distribuire la versione precedente.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

# <a name="arm-template"></a>[Modello ARM](#tab/azure-resource-manager)

1. Anche in questo caso è necessario apportare alcune modifiche al modello locale per adattarlo alle specifiche di modello. Copiare il modello seguente e salvarlo in locale con il nome azuredeploy.json.

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
              "name": "2.0",
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
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
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

1. Per aggiungere la nuova versione alla specifica di modello, distribuire il modello con l'interfaccia della riga di comando di Azure o con PowerShell.

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
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
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

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse distribuite in questo argomento di avvio rapido, eliminare entrambi i gruppi di risorse creati.

1. Nel portale di Azure selezionare Gruppo di risorse nel menu a sinistra.

1. Immettere il nome del gruppo di risorse (templateSpecRG e storageRG) nel campo Filtra per nome.

1. Selezionare il nome del gruppo di risorse.

1. Selezionare Elimina gruppo di risorse nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare una specifica di modello che include modelli collegati, vedere [Creare una specifica di modello di un modello collegato](template-specs-create-linked.md).
