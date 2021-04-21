---
title: Creare una macchina virtuale Windows con Azure Image Builder (anteprima)
description: Creare una macchina virtuale Windows con il Image Builder Azure.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764568"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Anteprima: Creare una macchina virtuale Windows con Azure Image Builder

Questo articolo illustra come creare un'immagine Windows personalizzata usando il Image Builder per macchine virtuali di Azure. L'esempio in questo articolo usa [le personalizzatori](../linux/image-builder-json.md#properties-customize) per personalizzare l'immagine:
- PowerShell (ScriptUri): scaricare ed eseguire uno [script di PowerShell.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)
- Riavvio di Windows: riavvia la macchina virtuale.
- PowerShell (inline): eseguire un comando specifico. In questo esempio viene creata una directory nella macchina virtuale usando `mkdir c:\\buildActions` .
- File: copiare un file da GitHub nella macchina virtuale. Questo esempio copia [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) nella macchina `c:\buildArtifacts\index.html` virtuale.
- buildTimeoutInMinutes: aumentare il tempo di compilazione per consentire compilazioni con esecuzione più lunga, il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire compilazioni con esecuzione più lunga.
- vmProfile: specifica delle proprietà vmSize e Network
- osDiskSizeGB: è possibile aumentare le dimensioni dell'immagine
- identity: fornire un'identità che azure Image Builder usare durante la compilazione


È anche possibile specificare un oggetto `buildTimeoutInMinutes` . Il valore predefinito è 240 minuti ed è possibile aumentare il tempo di compilazione per consentire compilazioni con esecuzione più lunga.

Per configurare l'immagine si userà un modello di esempio con estensione json. Il file con estensione json in uso è il seguente: [helloImageTemplateWin.jssu](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrare le funzionalità

Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verificare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verificare la registrazione.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Se non visualizzano Registered, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Impostare variabili

Alcune informazioni verranno usate ripetutamente, pertanto verranno create alcune variabili in cui archiviarle.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Creare una variabile per l'ID di sottoscrizione. Per ottenere questo risultato, usare `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Questo gruppo di risorse viene usato per archiviare l'artefatto del modello di configurazione dell'immagine e l'immagine.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Creare un'identità assegnata dall'utente e impostare autorizzazioni per il gruppo di risorse
Image Builder userà [l'identità utente fornita](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) per inserire l'immagine nel gruppo di risorse. In questo esempio si creerà una definizione del ruolo di Azure con le azioni granulari per eseguire la distribuzione dell'immagine. La definizione del ruolo verrà quindi assegnata all'identità utente.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Creare un'identità gestita assegnata dall'utente e concedere le autorizzazioni 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Scaricare l'esempio di modello di configurazione dell'immagine

È stato creato un modello di configurazione dell'immagine con parametri da provare. Scaricare il file JSON di esempio e configurarlo con le variabili impostate in precedenza.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

È possibile modificare questo esempio nel terminale usando un editor di testo come `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Per l'immagine di origine è necessario [specificare sempre una versione](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), non è possibile usare `latest` .
> Se si aggiunge o si modifica il gruppo di risorse in cui viene distribuita l'immagine, è necessario impostare le autorizzazioni [per](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) il gruppo di risorse.
 
## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio Image Builder macchina virtuale

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Al termine, verrà restituito un messaggio di esito positivo alla console e verrà creato `Image Builder Configuration Template` un oggetto in `$imageResourceGroup` . È possibile visualizzare questa risorsa nel gruppo di risorse nel portale di Azure, se si abilita "Mostra tipi nascosti".

In background, Image Builder anche un gruppo di risorse di staging nella sottoscrizione. Questo gruppo di risorse viene usato per la compilazione dell'immagine. Sarà nel formato seguente: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Non è necessario eliminare direttamente il gruppo di risorse di staging. Prima di tutto eliminare l'artefatto del modello di immagine, il gruppo di risorse di staging verrà eliminato.

Se il servizio segnala un errore durante l'invio del modello di configurazione dell'immagine:
-  Esaminare questi passaggi [per la risoluzione dei](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) problemi. 
- È necessario eliminare il modello usando il frammento di codice seguente prima di riprovare a eseguire l'invio.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Avviare la compilazione dell'immagine
Avviare il processo di compilazione dell'immagine [usando az resource invoke-action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Attendere il completamento della compilazione. Questa operazione può richiedere circa 15 minuti.

Se si verificano errori, esaminare questi passaggi per [la risoluzione dei](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) problemi.


## <a name="create-the-vm"></a>Creare la macchina virtuale

Creare la macchina virtuale usando l'immagine creata. Sostituire *\<password>* con la propria password per nella macchina `aibuser` virtuale.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione

Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della VM. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:

```console
dir c:\
```

Dovrebbero essere disponibili le due directory seguenti create durante la personalizzazione dell'immagine:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Eseguire la pulizia

Al termine, eliminare le risorse.

### <a name="delete-the-image-builder-template"></a>Eliminare il modello di Image Builder

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Eliminare l'assegnazione di ruolo, la definizione del ruolo e l'identità utente.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Eliminare il gruppo di risorse immagine

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui componenti del file json usato in questo articolo, vedere Image builder template reference (Informazioni di riferimento [sui modelli di Image Builder).](../linux/image-builder-json.md)
