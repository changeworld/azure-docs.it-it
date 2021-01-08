---
title: Creare più istanze di risorse
description: Informazioni su come creare un modello di Azure Resource Manager (modello di ARM) per creare più istanze di risorse di Azure.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673954"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Esercitazione: Creare più istanze di risorse con modelli di Azure Resource Manager

Informazioni su come eseguire un'iterazione del modello di Azure Resource Manager (modello di ARM) per creare più istanze di una risorsa di Azure. In questa esercitazione si modifica un modello per creare tre istanze di account di archiviazione.

![Diagramma creazione di più istanze con Azure Resource Manager](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Per un modulo Microsoft Learn che illustra la copia delle risorse, vedere [Gestire distribuzioni cloud complesse usando le funzionalità avanzate dei modelli di ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Avvio rapido: Creare modelli di ARM con Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

[Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) è un repository di modelli di Azure Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard). Il modello definisce una risorsa account di archiviazione di Azure.

1. In Visual Studio Code selezionare **File** > **Apri file**.
1. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file.
1. Nel modello è definita una risorsa `Microsoft.Storage/storageAccounts`. Confrontare il modello con le [informazioni di riferimento sui modelli](/azure/templates/Microsoft.Storage/storageAccounts). Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
1. Selezionare **File** > **Salva con nome** per salvare il file con il nome _azuredeploy.json_ nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Il modello esistente crea un account di archiviazione. Personalizzare il modello per creare tre account di archiviazione.

Da Visual Studio Code, apportare le quattro modifiche seguenti:

![Creazione di più istanze con Azure Resource Manager](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Aggiungere un elemento `copy` alla definizione di risorsa dell'account di archiviazione. Nell'elemento `copy` si specifica il numero di iterazioni e una variabile per il ciclo. Il valore del conteggio deve essere un numero intero positivo e non può essere maggiore di 800.
2. La funzione `copyIndex()` restituisce l'iterazione attuale nel ciclo. L'indice viene usato come prefisso del nome. `copyIndex()` è in base zero. Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione `copyIndex()`. Ad esempio: `copyIndex(1)`.
3. Eliminare l'elemento `variables`, in quanto non è più usato.
4. Eliminare l'elemento `outputs`. Non è più necessario.

Il modello completato è simile a:

```json
{
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
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Per altre informazioni sulla creazione di più istanze, vedere [Iterazione delle risorse nei modelli di Azure Resource Manager](./copy-resources.md)

## <a name="deploy-the-template"></a>Distribuire il modello

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com)

1. Scegliere l'ambiente preferito selezionando **PowerShell** o **Bash** (per l'interfaccia della riga di comando) nell'angolo in alto a sinistra. Quando si cambia interfaccia, è necessario riavviare la shell.

    ![Caricare file in Cloud Shell nel portale di Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selezionare **Carica/Scarica file** e quindi **Carica**. Vedere l'immagine sopra riportata. Selezionare il file salvato nella sezione precedente. Dopo aver caricato il file, è possibile usare i comandi `ls` e `cat` per verificare che il file sia stato caricato.

1. In Cloud Shell eseguire i comandi riportati sotto. Selezionare la scheda per visualizzare il codice di PowerShell o il codice dell'interfaccia della riga di comando.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Dopo aver completato la distribuzione di un modello, è possibile visualizzare i tre account di archiviazione creati nel gruppo di risorse specificato. Confrontare i nomi degli account di archiviazione con la definizione del nome nel modello.

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale tre risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare più istanze dell'account di archiviazione. Nell'esercitazione successiva verrà sviluppato un modello con più risorse e più tipi di risorse. alcune con risorse dipendenti.

> [!div class="nextstepaction"]
> [Creare le risorse dipendenti](./template-tutorial-create-templates-with-dependent-resources.md)
