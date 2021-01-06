---
title: Configurare l'ambiente di sviluppo per gli script di distribuzione nei modelli | Microsoft Docs
description: Configurare l'ambiente di sviluppo per gli script di distribuzione in modelli di Azure Resource Manager (modelli ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: 13dc072e31f0d27768de8d9a62ea942d55460713
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936397"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Configurare l'ambiente di sviluppo per gli script di distribuzione nei modelli ARM

Informazioni su come creare un ambiente di sviluppo per lo sviluppo e il test di script di distribuzione con un'immagine dello script di distribuzione. È possibile creare un' [istanza di contenitore di Azure](../../container-instances/container-instances-overview.md) o usare [Docker](https://docs.docker.com/get-docker/). Entrambi sono trattati in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Se non si dispone di uno script di distribuzione, è possibile creare un file di _hello.ps1_ con il contenuto seguente:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Usare l'istanza di contenitore di Azure

Per modificare gli script nel computer, è necessario creare un account di archiviazione e montare l'account di archiviazione nell'istanza del contenitore. Per poter caricare lo script nell'account di archiviazione ed eseguire lo script nell'istanza del contenitore.

> [!NOTE]
> L'account di archiviazione creato per testare lo script non è lo stesso account di archiviazione usato dal servizio script di distribuzione per eseguire lo script. Il servizio script di distribuzione crea un nome univoco come una condivisione file a ogni esecuzione.

### <a name="create-an-azure-container-instance"></a>Creare un'istanza di contenitore di Azure

Il modello di Azure Resource Manager seguente (modello ARM) crea un'istanza del contenitore e una condivisione file, quindi monta la condivisione file nell'immagine del contenitore.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

Il valore predefinito per il percorso di montaggio è `deploymentScript` . Si tratta del percorso nell'istanza del contenitore in cui è montata nella condivisione file.

L'immagine del contenitore predefinita specificata nel modello è `mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3` . Vedere un elenco di [versioni di Azure PowerShell supportate](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Vedere un elenco delle [versioni supportate dell'interfaccia](https://mcr.microsoft.com/v2/azure-cli/tags/list)della riga di comando di Azure.

  >[!IMPORTANT]
  > Lo script di distribuzione usa le immagini CLI disponibili di Microsoft Container Registry. La certificazione di un'immagine dell'interfaccia della riga di comando per uno script di distribuzione richiede circa un mese. Non usare le versioni dell'interfaccia della riga di comando rilasciate negli ultimi 30 giorni. Per trovare le date di rilascio delle immagini, vedere [Note sulla versione dell'interfaccia della riga di comando di Azure](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Se viene utilizzata una versione non supportata, nel messaggio di errore vengono elencate le versioni supportate.

Il modello sospende l'istanza del contenitore 1800 secondi. Sono disponibili 30 minuti prima che l'istanza del contenitore entra nello stato finale e la sessione termina.

Per distribuire il modello:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Carica script di distribuzione

Caricare lo script di distribuzione nell'account di archiviazione. Di seguito è riportato un esempio di PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

È anche possibile caricare il file usando il portale di Azure e l'interfaccia della riga di comando di Azure.

### <a name="test-the-deployment-script"></a>Testare lo script di distribuzione

1. Dal portale di Azure aprire il gruppo di risorse in cui è stata distribuita l'istanza del contenitore e l'account di archiviazione.
1. Aprire il gruppo di contenitori. Il nome del gruppo di contenitori predefinito è il nome del progetto con l'aggiunta di **CG** . Si noterà che lo stato dell'istanza del contenitore è in **esecuzione** .
1. Selezionare **contenitori** dal menu a sinistra. Verrà visualizzata un'istanza di contenitore. Il nome dell'istanza del contenitore è il nome del progetto con il **contenitore** accodato.

    ![istanza del contenitore Connect script di distribuzione](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selezionare **Connetti**, quindi selezionare **Connetti**. Se non è possibile connettersi all'istanza del contenitore, riavviare il gruppo di contenitori e riprovare.
1. Nel riquadro della console eseguire i comandi seguenti:

    ```console
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    L'output è **Hello John Dole**.

    ![test dell'istanza del contenitore dello script di distribuzione](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

1. Se si usa AZ CLI container Image, eseguire questo codice:

   ```console
   cd /mnt/azscripts/azscriptinput
   ls
   ./userscript.sh
   ```

## <a name="use-docker"></a>Usare Docker

È possibile usare un'immagine del contenitore Docker preconfigurata come ambiente di sviluppo dello script di distribuzione. Per installare Docker, vedere [Get Docker](https://docs.docker.com/get-docker/).
È anche necessario configurare la condivisione di file per montare la directory che contiene gli script di distribuzione nel contenitore docker.

1. Eseguire il pull dell'immagine del contenitore dello script di distribuzione nel computer locale:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Nell'esempio viene usata la versione di PowerShell 4.3.0.

    Per eseguire il pull di un'immagine dell'interfaccia della riga di comando da un Registro Microsoft Container (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Questo esempio usa la versione 2.0.80. Lo script di distribuzione usa le immagini predefinite dei contenitori CLI trovate [qui](https://hub.docker.com/_/microsoft-azure-cli).

1. Eseguire l'immagine di Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Sostituire **&lt;lettera unità host>** e **&lt;nome directory host>** con una cartella esistente nell'unità condivisa. Esegue il mapping della cartella nella cartella _/data_ del contenitore. Ad esempio, per eseguire il mapping di _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** indica di mantenere attiva l'immagine del contenitore.

    Esempio di interfaccia della riga di comando:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Lo screenshot seguente illustra come eseguire uno script di PowerShell, dato che è presente un file di _helloworld.ps1_ nell'unità condivisa.

    ![Script di distribuzione del modello di Resource Manager docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Una volta testato correttamente lo script, è possibile utilizzarlo come script di distribuzione nei modelli.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: usare gli script di distribuzione nei modelli ARM](./template-tutorial-deployment-script.md)
