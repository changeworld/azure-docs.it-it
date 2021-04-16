---
title: Trasferire gli artefatti
description: Trasferire raccolte di immagini o altri elementi da un registro contenitori a un altro registro creando una pipeline di trasferimento usando gli account di archiviazione di Azure
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: e921880eb0b8ae5a38e69c9c0045f6a26d84084d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497983"
---
# <a name="transfer-artifacts-to-another-registry"></a>Trasferire gli artefatti in un altro registro

Questo articolo illustra come trasferire raccolte di immagini o altri elementi del Registro di sistema da un registro Azure Container a un altro registro. I registri di origine e di destinazione possono essere in sottoscrizioni uguali o diverse, tenant di Active Directory, cloud di Azure o cloud fisicamente disconnessi. 

Per trasferire gli elementi, creare una *pipeline di trasferimento* che replica gli elementi tra due registri usando l'archiviazione [BLOB:](../storage/blobs/storage-blobs-introduction.md)

* Gli artefatti di un registro di origine vengono esportati in un BLOB in un account di archiviazione di origine 
* Il BLOB viene copiato dall'account di archiviazione di origine a un account di archiviazione di destinazione
* Il BLOB nell'account di archiviazione di destinazione viene importato come artefatti nel Registro di sistema di destinazione. È possibile configurare la pipeline di importazione per l'attivazione ogni volta che il BLOB dell'artefatto viene aggiornato nell'archiviazione di destinazione.

Il trasferimento è ideale per copiare il contenuto tra due registri di Contenitori di Azure in cloud fisicamente disconnessi, mediati dagli account di archiviazione in ogni cloud. Se invece si vogliono copiare immagini dai registri contenitori nei cloud connessi, inclusi Docker Hub e altri fornitori di [cloud,](container-registry-import-images.md) è consigliabile importare immagini.

In questo articolo si usano Azure Resource Manager di modelli per creare ed eseguire la pipeline di trasferimento. L'interfaccia della riga di comando di Azure viene usata per effettuare il provisioning delle risorse associate, ad esempio i segreti di archiviazione. È consigliabile utilizzare l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

Questa funzionalità è disponibile per il livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio del registro e sui limiti, vedere [livelli di Registro Azure Container](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* **Registri contenitori:** è necessario un registro di origine esistente con elementi da trasferire e un registro di destinazione. Il trasferimento ACR è progettato per lo spostamento tra cloud fisicamente disconnessi. Per i test, i registri di origine e di destinazione possono essere nella stessa sottoscrizione di Azure, in un tenant di Active Directory o in un cloud diverso. 

   Se è necessario creare un registro, vedere Avvio rapido: Creare un registro [contenitori privato usando l'interfaccia](container-registry-get-started-azure-cli.md)della riga di comando di Azure. 
* **Account di archiviazione:** creare account di archiviazione di origine e di destinazione in una sottoscrizione e in un percorso a scelta. A scopo di test, è possibile usare la stessa sottoscrizione o la stessa sottoscrizione dei registri di origine e di destinazione. Per gli scenari tra cloud, in genere si crea un account di archiviazione separato in ogni cloud. 

  Se necessario, creare gli account di archiviazione con l'interfaccia della riga [di comando](../storage/common/storage-account-create.md?tabs=azure-cli) di Azure o altri strumenti. 

  Creare un contenitore BLOB per il trasferimento degli artefatti in ogni account. Ad esempio, creare un contenitore denominato *transfer*. Due o più pipeline di trasferimento possono condividere lo stesso account di archiviazione, ma devono usare ambiti contenitore di archiviazione diversi.
* **Insiemi di credenziali delle chiavi:** gli insiemi di credenziali delle chiavi sono necessari per archiviare i segreti dei token di firma di accesso condiviso usati per accedere agli account di archiviazione di origine e di destinazione. Creare gli insiemi di credenziali delle chiavi di origine e di destinazione nella stessa sottoscrizione o sottoscrizione di Azure dei registri di origine e di destinazione. A scopo dimostrativo, i modelli e i comandi usati in questo articolo presuppongono anche che gli insiemi di credenziali delle chiavi di origine e di destinazione si trovino rispettivamente negli stessi gruppi di risorse dei registri di origine e di destinazione. Questo uso di gruppi di risorse comuni non è necessario, ma semplifica i modelli e i comandi usati in questo articolo.

   Se necessario, creare insiemi di credenziali delle chiavi con l'interfaccia della [riga di comando](../key-vault/secrets/quick-create-cli.md) di Azure o altri strumenti.

* **Variabili di** ambiente: ad esempio i comandi di questo articolo, impostare le variabili di ambiente seguenti per gli ambienti di origine e di destinazione. Tutti gli esempi sono formattati per la shell Bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Panoramica dello scenario

Creare le tre risorse della pipeline seguenti per il trasferimento di immagini tra registri. Tutti vengono creati usando operazioni PUT. Queste risorse operano sui registri *di origine e* *di* destinazione e sugli account di archiviazione. 

L'autenticazione di archiviazione usa token di firma di accesso condiviso, gestiti come segreti negli insiemi di credenziali delle chiavi. Le pipeline usano le identità gestite per leggere i segreti negli insiemi di credenziali.

* **[ExportPipeline:](#create-exportpipeline-with-resource-manager)** risorsa di lunga durata che contiene informazioni di alto livello sul registro *di* origine e sull'account di archiviazione. Queste informazioni includono l'URI del contenitore BLOB di archiviazione di origine e l'insieme di credenziali delle chiavi che gestisce il token di firma di accesso condiviso di origine. 
* **[ImportPipeline:](#create-importpipeline-with-resource-manager)** risorsa di lunga durata che contiene informazioni di alto livello sul registro *di* destinazione e sull'account di archiviazione. Queste informazioni includono l'URI del contenitore BLOB di archiviazione di destinazione e l'insieme di credenziali delle chiavi che gestisce il token di firma di accesso condiviso di destinazione. Un trigger di importazione è abilitato per impostazione predefinita, quindi la pipeline viene eseguita automaticamente quando un BLOB di artefatto viene inserito nel contenitore di archiviazione di destinazione. 
* **[PipelineRun:](#create-pipelinerun-for-export-with-resource-manager)** risorsa usata per richiamare una risorsa ExportPipeline o ImportPipeline.  
  * Eseguire exportPipeline manualmente creando una risorsa PipelineRun e specificando gli elementi da esportare.  
  * Se è abilitato un trigger di importazione, ImportPipeline viene eseguito automaticamente. Può anche essere eseguito manualmente usando pipelineRun. 
  * Attualmente è possibile **trasferire un massimo di 50 artefatti** con ogni PipelineRun.

### <a name="things-to-know"></a>Informazioni importanti
* ExportPipeline e ImportPipeline si trovano in genere in tenant di Active Directory diversi associati ai cloud di origine e di destinazione. Questo scenario richiede identità gestite separate e insiemi di credenziali delle chiavi per le risorse di esportazione e importazione. A scopo di test, queste risorse possono essere inserite nello stesso cloud, condividendo le identità.
* Per impostazione predefinita, i modelli ExportPipeline e ImportPipeline consentono a un'identità gestita assegnata dal sistema di accedere ai segreti dell'insieme di credenziali delle chiavi. I modelli ExportPipeline e ImportPipeline supportano anche un'identità assegnata dall'utente specificata. 

## <a name="create-and-store-sas-keys"></a>Creare e archiviare chiavi di firma di accesso condiviso

Il trasferimento usa token di firma di accesso condiviso per accedere agli account di archiviazione negli ambienti di origine e di destinazione. Generare e archiviare i token come descritto nelle sezioni seguenti.  

### <a name="generate-sas-token-for-export"></a>Generare un token di firma di accesso condiviso per l'esportazione

Eseguire il [comando az storage container generate-sas][az-storage-container-generate-sas] per generare un token di firma di accesso condiviso per il contenitore nell'account di archiviazione di origine, usato per l'esportazione degli artefatti.

*Autorizzazioni di token consigliate:* Lettura, Scrittura, Elenco, Aggiungi. 

Nell'esempio seguente l'output del comando viene assegnato alla EXPORT_SAS di ambiente, preceduta dal carattere '?'. Aggiornare il `--expiry` valore per l'ambiente:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Archiviare il token di firma di accesso condiviso per l'esportazione

Archiviare il token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di Azure di origine [usando az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generare un token di firma di accesso condiviso per l'importazione

Eseguire il [comando az storage container generate-sas][az-storage-container-generate-sas] per generare un token di firma di accesso condiviso per il contenitore nell'account di archiviazione di destinazione, usato per l'importazione degli artefatti.

*Autorizzazioni token consigliate:* Lettura, Eliminazione, Elenco

Nell'esempio seguente l'output del comando viene assegnato alla IMPORT_SAS di ambiente, preceduta dal carattere '?'. Aggiornare il `--expiry` valore per l'ambiente:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Archiviare il token di firma di accesso condiviso per l'importazione

Archiviare il token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di Azure di destinazione [usando az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Creare ExportPipeline con Resource Manager

Creare una risorsa ExportPipeline per il registro contenitori di origine usando Azure Resource Manager distribuzione del modello.

Copiare ExportPipeline Resource Manager [file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di origine      |
|exportPipelineName     |  Nome scelto per la pipeline di esportazione       |
|targetUri     |  URI del contenitore di archiviazione nell'ambiente di origine (destinazione della pipeline di esportazione).<br/>Esempio: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nome dell'insieme di credenziali delle chiavi di origine  |
|sasTokenSecretName  | Nome del segreto del token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di origine <br/>Esempio: acrexportsas

### <a name="export-options"></a>Opzioni di esportazione

La `options` proprietà per le pipeline di esportazione supporta valori booleani facoltativi. Sono consigliati i valori seguenti:

|Parametro  |Valore  |
|---------|---------|
|opzioni | OverwriteBlobs - Sovrascrivere i BLOB di destinazione esistenti<br/>ContinueOnErrors: continua l'esportazione degli artefatti rimanenti nel registro di origine se l'esportazione di un elemento non riesce.

### <a name="create-the-resource"></a>Creare la risorsa

Eseguire [az deployment group create][az-deployment-group-create] per creare una risorsa denominata *exportPipeline,* come illustrato negli esempi seguenti. Per impostazione predefinita, con la prima opzione, il modello di esempio abilita un'identità assegnata dal sistema nella risorsa ExportPipeline. 

Con la seconda opzione è possibile fornire alla risorsa un'identità assegnata dall'utente. La creazione dell'identità assegnata dall'utente non viene visualizzata.

Con entrambe le opzioni, il modello configura l'identità per accedere al token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di esportazione. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opzione 1: Creare una risorsa e abilitare l'identità assegnata dal sistema

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opzione 2: Creare una risorsa e fornire l'identità assegnata dall'utente

In questo comando specificare l'ID risorsa dell'identità assegnata dall'utente come parametro aggiuntivo.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Nell'output del comando prendere nota dell'ID risorsa ( `id` ) della pipeline. È possibile archiviare questo valore in una variabile di ambiente per un uso successivo eseguendo [az deployment group show][az-deployment-group-show]. Ad esempio:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Creare ImportPipeline con Resource Manager 

Creare una risorsa ImportPipeline nel registro contenitori di destinazione usando Azure Resource Manager distribuzione del modello. Per impostazione predefinita, la pipeline è abilitata per l'importazione automatica quando l'account di archiviazione nell'ambiente di destinazione include un BLOB di artefatti.

Copiare ImportPipeline Resource Manager [file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di destinazione      |
|importPipelineName     |  Nome scelto per la pipeline di importazione       |
|Sourceuri     |  URI del contenitore di archiviazione nell'ambiente di destinazione (origine per la pipeline di importazione).<br/>Esempio: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nome dell'insieme di credenziali delle chiavi di destinazione |
|sasTokenSecretName     |  Nome del segreto del token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di destinazione<br/>Esempio: acr importsas |

### <a name="import-options"></a>Opzioni di importazione

La `options` proprietà per la pipeline di importazione supporta valori booleani facoltativi. Sono consigliati i valori seguenti:

|Parametro  |Valore  |
|---------|---------|
|opzioni | OverwriteTags : sovrascrive i tag di destinazione esistenti<br/>DeleteSourceBlobOnSuccess: eliminare il BLOB di archiviazione di origine dopo l'importazione nel Registro di sistema di destinazione<br/>ContinueOnErrors: continua l'importazione degli artefatti rimanenti nel registro di destinazione se l'importazione di un artefatto non riesce.

### <a name="create-the-resource"></a>Creare la risorsa

Eseguire [az deployment group create][az-deployment-group-create] per creare una risorsa denominata *importPipeline,* come illustrato negli esempi seguenti. Per impostazione predefinita, con la prima opzione, il modello di esempio abilita un'identità assegnata dal sistema nella risorsa ImportPipeline. 

Con la seconda opzione è possibile fornire alla risorsa un'identità assegnata dall'utente. La creazione dell'identità assegnata dall'utente non viene visualizzata.

Con entrambe le opzioni, il modello configura l'identità per accedere al token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di importazione. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opzione 1: Creare una risorsa e abilitare l'identità assegnata dal sistema

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opzione 2: Creare una risorsa e fornire l'identità assegnata dall'utente

In questo comando specificare l'ID risorsa dell'identità assegnata dall'utente come parametro aggiuntivo.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Se si prevede di eseguire l'importazione manualmente, prendere nota dell'ID risorsa ( `id` ) della pipeline. È possibile archiviare questo valore in una variabile di ambiente per un uso successivo eseguendo il [comando az deployment group show.][az-deployment-group-show] Ad esempio:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Creare PipelineRun per l'esportazione con Resource Manager 

Creare una risorsa PipelineRun per il registro contenitori di origine usando la Azure Resource Manager modello. Questa risorsa esegue la risorsa ExportPipeline creata in precedenza ed esporta gli artefatti specificati dal registro contenitori come BLOB nell'account di archiviazione di origine.

Copiare PipelineEsegui Resource Manager [file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di origine      |
|pipelineRunName     |  Nome scelto per l'esecuzione       |
|pipelineResourceId     |  ID risorsa della pipeline di esportazione.<br/>Esempio: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|Targetname     |  Nome scelto per il BLOB degli artefatti esportato nell'account di archiviazione di origine, ad esempio *myblob*
|Artefatti | Matrice di elementi di origine da trasferire, come tag o digest del manifesto<br/>Esempio: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Se si ridistribuisce una risorsa PipelineRun con proprietà identiche, è necessario usare anche la [proprietà forceUpdateTag.](#redeploy-pipelinerun-resource)

Eseguire [az deployment group create][az-deployment-group-create] per creare la risorsa PipelineRun. Nell'esempio seguente viene impostata la distribuzione *exportPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Per un uso successivo, archiviare l'ID risorsa dell'esecuzione della pipeline in una variabile di ambiente:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

L'esportazione degli elementi può richiedere alcuni minuti. Al termine della distribuzione, verificare l'esportazione dell'artefatto elencando il BLOB esportato nel contenitore *di* trasferimento dell'account di archiviazione di origine. Ad esempio, eseguire il [comando az storage blob list:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Blob di trasferimento (facoltativo) 

Usare lo strumento AzCopy o altri metodi per [trasferire i](../storage/common/storage-use-azcopy-v10.md#transfer-data) dati BLOB dall'account di archiviazione di origine all'account di archiviazione di destinazione.

Ad esempio, il comando seguente copia myblob dal contenitore di trasferimento nell'account di origine al contenitore [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) *di* trasferimento nell'account di  destinazione. Se il BLOB esiste nell'account di destinazione, viene sovrascritto. L'autenticazione usa token di firma di accesso condiviso con le autorizzazioni appropriate per i contenitori di origine e di destinazione. I passaggi per creare i token non vengono visualizzati.

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Attivare la risorsa ImportPipeline

Se è stato abilitato il parametro di ImportPipeline (valore predefinito), la pipeline viene attivata dopo la copia del `sourceTriggerStatus` BLOB nell'account di archiviazione di destinazione. L'importazione degli elementi può richiedere alcuni minuti. Al termine dell'importazione, verificare l'importazione dell'artefatto elencando i repository nel registro contenitori di destinazione. Ad esempio, eseguire [az acr repository list:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

Se il parametro della pipeline di importazione non è stato abilitato, eseguire manualmente la `sourceTriggerStatus` risorsa ImportPipeline, come illustrato nella sezione seguente. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Creare PipelineRun per l'importazione con Resource Manager (facoltativo) 
 
È anche possibile usare una risorsa PipelineRun per attivare importPipeline per l'importazione di artefatti nel registro contenitori di destinazione.

Copiare PipelineEsegui Resource Manager [file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di destinazione      |
|pipelineRunName     |  Nome scelto per l'esecuzione       |
|pipelineResourceId     |  ID risorsa della pipeline di importazione.<br/>Esempio: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Nome del BLOB esistente per gli artefatti esportati nell'account di archiviazione, ad esempio *myblob*

Se si ridistribuisce una risorsa PipelineRun con proprietà identiche, è necessario usare anche la [proprietà forceUpdateTag.](#redeploy-pipelinerun-resource)

Eseguire [az deployment group create][az-deployment-group-create] per eseguire la risorsa.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Per un uso successivo, archiviare l'ID risorsa dell'esecuzione della pipeline in una variabile di ambiente:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Al termine della distribuzione, verificare l'importazione dell'artefatto elencando i repository nel registro contenitori di destinazione. Ad esempio, eseguire [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Ridistribuire la risorsa PipelineRun

Se si ridistribuisce una risorsa PipelineRun con *proprietà identiche,* è necessario sfruttare la **proprietà forceUpdateTag.** Questa proprietà indica che la risorsa PipelineRun deve essere ricreata anche se la configurazione non è stata modificata. Assicurarsi che forceUpdateTag sia diverso ogni volta che si ridistribuisce la risorsa PipelineRun. L'esempio seguente ricrea un oggetto PipelineRun per l'esportazione. Il valore datetime corrente viene usato per impostare forceUpdateTag, assicurando così che questa proprietà sia sempre univoca.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Eliminare le risorse della pipeline

I comandi di esempio seguenti usano [az resource delete][az-resource-delete] per eliminare le risorse della pipeline create in questo articolo. Gli ID risorsa sono stati archiviati in precedenza nelle variabili di ambiente.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Distribuzione modelli errori o errori**
  * Se un'esecuzione della pipeline ha esito negativo, `pipelineRunErrorMessage` esaminare la proprietà della risorsa di esecuzione.
  * Per gli errori comuni di distribuzione dei modelli, vedere [Risolvere i problemi relativi alle distribuzioni di modelli di Arm](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemi relativi all'esportazione o all'importazione di BLOB di archiviazione**
  * Il token di firma di accesso condiviso potrebbe essere scaduto o avere autorizzazioni insufficienti per l'esportazione o l'esecuzione dell'importazione specificata
  * Il BLOB di archiviazione esistente nell'account di archiviazione di origine potrebbe non essere sovrascritto durante più esecuzioni di esportazione. Verificare che l'opzione OverwriteBlob sia impostata nell'esecuzione dell'esportazione e che il token di firma di accesso condiviso disponga di autorizzazioni sufficienti.
  * Il BLOB di archiviazione nell'account di archiviazione di destinazione potrebbe non essere eliminato dopo l'esecuzione dell'importazione. Verificare che l'opzione DeleteBlobOnSuccess sia impostata nell'esecuzione dell'importazione e che il token di firma di accesso condiviso disponga di autorizzazioni sufficienti.
  * BLOB di archiviazione non creato o eliminato. Verificare che il contenitore specificato nell'esecuzione di esportazione o importazione esista o che il BLOB di archiviazione specificato esista per l'esecuzione manuale dell'importazione. 
* **Problemi di AzCopy**
  * Vedere [Risolvere i problemi di AzCopy](../storage/common/storage-use-azcopy-configure.md).  
* **Problemi di trasferimento degli artefatti**
  * Non tutti gli artefatti, o nessuno, vengono trasferiti. Verificare l'ortografia degli elementi nell'esecuzione dell'esportazione e il nome del BLOB nelle esecuzioni di esportazione e importazione. Verificare di trasferire un massimo di 50 artefatti.
  * L'esecuzione della pipeline potrebbe non essere stata completata. Un'esecuzione di esportazione o importazione può richiedere del tempo. 
  * Per altri problemi relativi alla pipeline, specificare [l'ID](../azure-resource-manager/templates/deployment-history.md) di correlazione della distribuzione dell'esecuzione dell'esportazione o dell'importazione nel team Registro Azure Container lavoro.
* **Problemi durante il pull dell'immagine in un ambiente fisicamente isolato**
  * Se vengono visualizzati errori relativi a livelli esterni o tentativi di risolvere mcr.microsoft.com durante il tentativo di eseguire il pull di un'immagine in un ambiente fisicamente isolato, è probabile che il manifesto dell'immagine abbia livelli non distribuibili. A causa della natura di un ambiente fisicamente isolato, queste immagini spesso non riescono a eseguire il pull. È possibile verificare che questo sia il caso controllando nel manifesto dell'immagine eventuali riferimenti a registri esterni. In questo caso, è necessario eseguire il push dei livelli non distribuibili nel cloud pubblico ACR prima di distribuire un'esecuzione della pipeline di esportazione per tale immagine. Per indicazioni su come eseguire questa operazione, vedere eseguire Ricerca per categorie push di livelli [non distribuibili in un registro?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Passaggi successivi

Per importare immagini di contenitori singoli in un registro Azure Container da un registro pubblico o da un altro registro privato, vedere le informazioni di riferimento sul [comando az acr import.][az-acr-import]

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
