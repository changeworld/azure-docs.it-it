---
title: Generatore di immagini-creare un'immagine di desktop virtuale Windows
description: Creare un'immagine di macchina virtuale di Azure di desktop virtuale Windows usando Generatore immagini di Azure in PowerShell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602019"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Creare un'immagine di desktop virtuale Windows usando il generatore di immagini VM di Azure e PowerShell

Questo articolo illustra come creare un'immagine di desktop virtuale Windows con queste personalizzazioni:

* Installazione di [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Esecuzione di uno [script di ottimizzazione del desktop virtuale Windows](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) dal repository della community.
* Installare [Microsoft teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd).
* [Riavvia](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* Esegui [Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer)

Verrà illustrato come automatizzare questa operazione usando il generatore di immagini di macchine virtuali di Azure e distribuire l'immagine in una [raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), in cui è possibile eseguire la replica in altre aree, controllare la scala e condividere l'immagine all'interno e all'esterno delle organizzazioni.


Per semplificare la distribuzione di una configurazione di Image Builder, questo esempio usa un modello di Azure Resource Manager con il modello di generatore di immagini annidato all'interno di. Ciò offre altri vantaggi, ad esempio variabili e input di parametri. È anche possibile passare parametri dalla riga di comando.

Questo articolo è destinato a essere un esercizio di copia e incolla.

> [!NOTE]
> Gli script per installare le app si trovano in [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Sono solo per illustrazioni e test e non per i carichi di lavoro di produzione. 

## <a name="tips-for-building-windows-images"></a>Suggerimenti per la creazione di immagini Windows 

- Dimensioni macchina virtuale: le dimensioni predefinite della macchina virtuale sono `Standard_D1_v2` , che non è adatto per Windows. Usare un `Standard_D2_v2` o un valore maggiore.
- Questo esempio usa gli [script verbi di PowerShell](../linux/image-builder-json.md). È necessario usare queste impostazioni. in questo modo, la compilazione smette di rispondere.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Ad esempio:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Commentare il codice: il log di compilazione AIB (Customization. log) è estremamente dettagliato. Se si commentano gli script con ' write-host ', questi verranno inviati ai log e la risoluzione dei problemi verrà semplificata.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Codici di uscita: AIB prevede che tutti gli script restituiscano un codice di uscita 0. qualsiasi codice di uscita diverso da zero comporterà la mancata personalizzazione di AIB e l'arresto della compilazione. Se sono presenti script complessi, aggiungere strumentazione ed emettere codici di uscita, questi verranno visualizzati nel file customization. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test: testare e testare il codice prima in una macchina virtuale autonoma, assicurarsi che non siano presenti richieste utente, che si stia usando il privilegio appropriato e così via.

- Rete- `Set-NetAdapterAdvancedProperty` . Questa impostazione viene impostata nello script di ottimizzazione, ma non riesce a eseguire la compilazione di AIB perché disconnette la rete. si tratta di un commento. È in fase di analisi.

## <a name="prerequisites"></a>Prerequisiti

È necessario che siano installati i CmdLet di Azure PowerShell più recenti, vedere [qui](https://docs.microsoft.com/powershell/azure/overview) per i dettagli dell'installazione.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Configurare l'ambiente e le variabili

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Autorizzazioni, identità utente e ruolo 


 Creare un'identità utente.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Assegnare le autorizzazioni all'identità per distribuire le immagini. Tramite questo comando verrà scaricato e aggiornato il modello con i parametri specificati in precedenza.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Se viene visualizzato questo errore: "New-AzRoleDefinition: limite definizione ruolo superato. Non è possibile creare più definizioni di ruolo .' vedere questo articolo per risolvere: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Creare la Raccolta immagini condivise 

Se non si dispone già di una raccolta di immagini condivise, è necessario crearne una.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Configurare il modello di immagine

Per questo esempio, è disponibile un modello pronto per il download e l'aggiornamento del modello con i parametri specificati in precedenza, per installare FsLogix, le ottimizzazioni del sistema operativo, Microsoft teams ed eseguire Windows Update alla fine.

Se si apre il modello, è possibile vedere nella proprietà Source l'immagine in uso, in questo esempio viene usata un'immagine Win 10 multisessione. 

### <a name="windows-10-images"></a>Immagini di Windows 10
Due tipi di chiave che è necessario tenere presente: Multisession e Single-Session.

Le immagini multisessione sono destinate all'utilizzo in pool. Di seguito è riportato un esempio dei dettagli dell'immagine in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Le immagini a sessione singola sono destinate all'uso individuale. Di seguito è riportato un esempio dei dettagli dell'immagine in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

È anche possibile modificare le immagini WIN10 disponibili:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Scaricare il modello e configurare

A questo punto, è necessario scaricare il modello e configurarlo per l'uso.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

È possibile visualizzare il [modello](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json). tutto il codice è visualizzabile.


## <a name="submit-the-template"></a>Inviare il modello

Il modello deve essere inviato al servizio. verranno scaricati tutti gli elementi dipendenti (ad esempio script), verranno convalidati, verificate le autorizzazioni e archiviati nel gruppo di risorse di gestione temporanea, con prefisso *it_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Compilare l'immagine
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Il comando non attende il completamento della compilazione dell'immagine da parte del servizio Generatore immagini. è possibile eseguire una query sullo stato riportato di seguito.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Creare una macchina virtuale
A questo punto, la compilazione è stata completata. è possibile creare una VM dall'immagine, usando gli esempi disponibili [qui](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Eseguire la pulizia

Eliminare prima il modello di gruppo di risorse, non solo eliminare l'intero gruppo di risorse. in caso contrario, il gruppo di risorse di gestione temporanea (*it_*) usato da AIB non verrà pulito.

Rimuovere il modello di immagine.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Eliminare l'assegnazione di ruolo.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Eliminare il gruppo di risorse.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

È possibile provare altri esempi [su GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
