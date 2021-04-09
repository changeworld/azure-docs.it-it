---
title: 'Esercitazione: Distribuire un modello collegato'
description: Informazioni su come distribuire un modello collegato
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589282"
---
# <a name="tutorial-deploy-a-linked-template"></a>Esercitazione: Distribuire un modello collegato

Nelle [esercitazioni precedenti](./deployment-tutorial-local-template.md) si è appreso come distribuire un modello archiviato nel computer locale. Per distribuire soluzioni complesse, è possibile suddividere un modello in più modelli e distribuire questi modelli tramite un modello principale. In questa esercitazione si apprenderà come distribuire un modello principale che contiene il riferimento a un modello collegato. Quando il modello principale viene distribuito, viene attivata la distribuzione del modello collegato. Si apprenderà anche come archiviare e proteggere i modelli usando il token SAS. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'esercitazione precedente.

## <a name="review-template"></a>Rivedere il modello

Nelle esercitazioni precedenti si è distribuito un modello che crea un account di archiviazione, un piano di servizio app e un'app Web. Il modello usato era:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Creare un modello collegato

È possibile separare la risorsa dell'account di archiviazione da un modello collegato:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Il modello seguente è il modello principale. L'oggetto evidenziato `Microsoft.Resources/deployments` mostra come chiamare un modello collegato. Il modello collegato non può essere archiviato come file locale o file disponibile unicamente nella rete locale. È possibile specificare un valore URI del modello collegato che include HTTP o HTTPS oppure usare la proprietà _relativePath_ per distribuire un modello collegato remoto in un percorso relativo al modello padre. Un'opzione consiste nell'inserire il modello principale e il modello collegato in un account di archiviazione.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Archiviare il modello collegato

Sia il modello principale che il modello collegato vengono archiviati in GitHub:

Con lo script di PowerShell seguente viene creato un account di archiviazione, viene creato un contenitore e vengono copiati i due modelli da un repository GitHub al contenitore. Questi due modelli sono:

- Modello principale: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Modello collegato: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Selezionare **Prova** per aprire Cloud Shell, selezionare **Copia** per copiare lo script di PowerShell e fare clic con il pulsante destro del mouse sul riquadro della shell per incollare lo script:

> [!IMPORTANT]
> I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. Il nome deve essere univoco. Nel modello il nome dell'account di archiviazione è il nome del progetto con l'aggiunta di **store** e il nome del progetto deve avere una lunghezza compresa tra 3 e 11 caratteri. Quindi il nome del progetto deve soddisfare i requisiti del nome dell'account di archiviazione e deve avere meno di 11 caratteri.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire i modelli in un account di archiviazione, generare un token di firma di accesso condiviso e fornirlo al parametro _-QueryString_ . Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione. I BLOB contenenti i modelli sono accessibili solo al proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per un BLOB, il BLOB è accessibile a tutti gli utenti con tale token SAS. Se un altro utente intercetta l'URI e il token di firma di accesso condiviso, tale utente sarà in grado di accedere al modello. Usare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> Nel codice dell'interfaccia della riga di comando di Azure seguente l'argomento `-d` del parametro `date` non è valido in macOS. Gli utenti macOS dovranno quindi usare dovranno usare `-v+2H` per aggiungere due ore all'ora corrente nel terminale in macOS.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare le risorse distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come distribuire un modello collegato. Nell'esercitazione successiva si apprenderà come creare una pipeline DevOps per distribuire un modello.

> [!div class="nextstepaction"]
> [Creare una pipeline](./deployment-tutorial-pipeline.md)