---
title: Distribuire le risorse con l'interfaccia della riga di comando e il modello di Azure
description: Usare Azure Resource Manager e l'interfaccia della riga di comando di Azure per distribuire le risorse in Azure. Le risorse sono definite in un modello Resource Manager o in un file Bicep.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: f616a40f2683268f0cc26314fcc88ecca23bdbcf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782064"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure con i modelli di Azure Resource Manager (modelli ARM) o i file Bicep per distribuire le risorse in Azure. Se non si ha familiarità con i concetti di distribuzione [](overview.md) e gestione delle soluzioni di Azure, vedere Panoramica della distribuzione di modelli o [Panoramica di Bicep.](bicep-overview.md)

I comandi di distribuzione sono stati modificati nell'interfaccia della riga di comando di Azure versione 2.2.0. Gli esempi in questo articolo richiedono l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Per distribuire i file Bicep, è necessaria l'interfaccia della riga di comando [di Azure versione 2.20.0 o successiva.](/cli/azure/install-azure-cli)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se l'interfaccia della riga di comando di Azure non è installata, è possibile usare Azure Cloud Shell. Per altre informazioni, vedere [Distribuire modelli arm da Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Ambito della distribuzione

È possibile impostare la distribuzione come destinazione per un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. A seconda dell'ambito della distribuzione, vengono usati comandi diversi.

* Per eseguire la distribuzione in **un gruppo di risorse,** [usare az deployment group create:](/cli/azure/deployment/group#az_deployment_group_create)

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Per eseguire la distribuzione in **una sottoscrizione,** usare [az deployment sub create:](/cli/azure/deployment/sub#az_deployment_sub_create)

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

* Per eseguire la distribuzione in **un gruppo di** gestione, usare [az deployment mg create:](/cli/azure/deployment/mg#az_deployment_mg_create)

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Per altre informazioni sulle distribuzioni a livello di gruppo di gestione, vedere [Creare risorse a livello di gruppo di gestione](deploy-to-management-group.md).

* Per eseguire la distribuzione in **un tenant,** usare [az deployment tenant create:](/cli/azure/deployment/tenant#az_deployment_tenant_create)

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Per altre informazioni sulle distribuzioni a livello di tenant, vedere [Creare risorse a livello di tenant](deploy-to-tenant.md).

Per ogni ambito, l'utente che distribuisce il modello o il file Bicep deve avere le autorizzazioni necessarie per creare le risorse.

## <a name="deploy-local-template-or-bicep-file"></a>Distribuire il modello locale o il file Bicep

È possibile distribuire un modello dal computer locale o da un modello archiviato esternamente. Questa sezione descrive la distribuzione di un modello locale.

Se si esegue la distribuzione in un gruppo di risorse che non esiste, creare il gruppo di risorse. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Il nome non può terminare in un punto.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Per distribuire un modello locale o un file Bicep, usare il `--template-file` parametro nel comando di distribuzione. L'esempio seguente illustra anche come impostare un valore di parametro.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio che include il risultato:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuire un modello remoto

> [!NOTE]
> Attualmente, l'interfaccia della riga di comando di Azure non supporta la distribuzione di file Bicep remoti. Usare [l'interfaccia della](./bicep-install.md#development-environment) riga di comando di Bicep per compilare il file Bicep in un modello JSON e quindi caricare il file JSON nel percorso remoto.

Invece di archiviare i modelli arm nel computer locale, è preferibile archiviarli in una posizione esterna. ad esempio in un repository di controllo del codice sorgente come GitHub. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Se si esegue la distribuzione in un gruppo di risorse che non esiste, creare il gruppo di risorse. Il nome del gruppo di risorse può contenere solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Può contenere fino a 90 caratteri. Il nome non può terminare in un punto.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Per distribuire un modello esterno, usare il parametro `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

L'esempio precedente richiede l'utilizzo di un URI accessibile pubblicamente per il modello, che funziona per la maggior parte degli scenari. Il proprio modello non deve infatti includere dati sensibili. Se è necessario specificare dati riservati, ad esempio una password di amministratore, passare il valore come parametro protetto. Tuttavia, se si vuole gestire l'accesso al modello, è consigliabile usare [le specifiche del modello](#deploy-template-spec).

Per distribuire modelli collegati remoti con percorso relativo archiviati in un account di archiviazione, usare `query-string` per specificare il token di firma di accesso condiviso:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Per altre informazioni, vedere [Usare il percorso relativo per i modelli collegati.](./linked-templates.md#linked-template)

## <a name="deployment-name"></a>Nome distribuzione

Quando si distribuisce un modello arm, è possibile assegnare un nome alla distribuzione. Questo nome consente di recuperare la distribuzione dalla cronologia di distribuzione. Se non si specifica un nome per la distribuzione, viene usato il nome del file modello. Ad esempio, se si distribuisce un modello denominato e non si specifica un nome di `azuredeploy.json` distribuzione, la distribuzione viene denominata `azuredeploy` .

Ogni volta che si esegue una distribuzione, viene aggiunta una voce alla cronologia di distribuzione del gruppo di risorse con il nome della distribuzione. Se si esegue un'altra distribuzione e si assegna lo stesso nome, la voce precedente viene sostituita con la distribuzione corrente. Se si desidera mantenere voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

Per creare un nome univoco, è possibile assegnare un numero casuale.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

In caso contrario, aggiungere un valore di data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Se si eseguono distribuzioni simultanee nello stesso gruppo di risorse con lo stesso nome di distribuzione, viene completata solo l'ultima distribuzione. Tutte le distribuzioni con lo stesso nome che non sono state completate vengono sostituite dall'ultima distribuzione. Ad esempio, se si esegue una distribuzione denominata che distribuisce un account di archiviazione denominato e allo stesso tempo si esegue un'altra distribuzione denominata che distribuisce un account di archiviazione denominato , si distribuisce un solo `newStorage` `storage1` account di `newStorage` `storage2` archiviazione. L'account di archiviazione risultante è denominato `storage2` .

Tuttavia, se si esegue una distribuzione denominata che distribuisce un account di archiviazione denominato e subito dopo il completamento viene eseguita un'altra distribuzione denominata che distribuisce un account di archiviazione denominato , si dispone di due account `newStorage` `storage1` di `newStorage` `storage2` archiviazione. Uno è denominato `storage1` e l'altro è denominato `storage2` . Tuttavia, nella cronologia di distribuzione è presente una sola voce.

Quando si specifica un nome univoco per ogni distribuzione, è possibile eseguirli simultaneamente senza conflitti. Se si esegue una distribuzione denominata che distribuisce un account di archiviazione denominato e allo stesso tempo si esegue un'altra distribuzione denominata che distribuisce un account di archiviazione denominato , nella cronologia di distribuzione sono presenti due account di archiviazione e due `newStorage1` `storage1` `newStorage2` `storage2` voci.

Per evitare conflitti con le distribuzioni simultanee e garantire voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

## <a name="deploy-template-spec"></a>Distribuire la specifica di modello

> [!NOTE]
> Attualmente, l'interfaccia della riga di comando di Azure non supporta la creazione di specifiche di modello fornendo file Bicep. È tuttavia possibile creare un file Bicep con la [risorsa Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) per distribuire una specifica di modello. Di seguito è riportato un [esempio.](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)

Anziché distribuire un modello locale o remoto, è possibile creare una [specifica di modello](template-specs.md). La specifica del modello è una risorsa nella sottoscrizione di Azure che contiene un modello di Resource Editor. Semplifica la condivisione sicura del modello con gli utenti dell'organizzazione. Si usa il controllo degli accessi in base al ruolo di Azure per concedere l'accesso alla specifica del modello. Questa funzionalità è attualmente in anteprima.

Gli esempi seguenti illustrano come creare e distribuire una specifica di modello.

Creare prima di tutto la specifica del modello specificando il modello di Arm.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Ottenere quindi l'ID per la specifica del modello e distribuirlo.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Per altre informazioni, vedere [Azure Resource Manager specifiche del modello (anteprima)](template-specs.md).

## <a name="preview-changes"></a>Anteprima modifiche

Prima di distribuire il modello, è possibile visualizzare in anteprima le modifiche apportate dal modello all'ambiente. Usare [l'operazione di what-if](template-deploy-what-if.md) per verificare che il modello apportare le modifiche previste. Verifica anche la presenza di errori nel modello.

## <a name="parameters"></a>Parametri

Per passare i valori dei parametri, è possibile usare i parametri inline o un file di parametri.

### <a name="inline-parameters"></a>Parametri inline

Per passare i parametri inline, specificare i valori in `parameters`. Ad esempio, per passare una stringa e una matrice a un modello in una shell Bash, usare:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se si usa l'interfaccia della riga di comando di Azure con il prompt dei comandi di Windows (CMD) o PowerShell, passare la matrice nel formato: `exampleArray="['value1','value2']"` .

È anche possibile ottenere i contenuti del file e fornire il contenuto come un parametro inline.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Ottenere un valore di parametro da un file è utile quando è necessario fornire i valori di configurazione. Ad esempio, è possibile fornire i valori [cloud-init per una macchina virtuale Linux](../../virtual-machines/linux/using-cloud-init.md).

Il _arrayContent.jssul_ formato è:

```json
[
    "value1",
    "value2"
]
```

Per passare un oggetto, ad esempio per impostare i tag, usare JSON. Ad esempio, il modello potrebbe includere un parametro simile al seguente:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

In questo caso, è possibile passare una stringa JSON per impostare il parametro come illustrato nello script Bash seguente:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Usare le virgolette doppie intorno al codice JSON che si vuole passare all'oggetto .

### <a name="parameter-files"></a>File dei parametri

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Il file dei parametri deve essere un file locale. I file dei parametri esterni non sono supportati con l'interfaccia della riga di comando di Azure. Sia il modello arm che il file Bicep usano file di parametri JSON.

Per altre informazioni sul file dei parametri, vedere [Creare il file di parametri di Resource Manager](parameter-files.md).

Per passare un file di parametri locale, usare per specificare un file locale `@` denominato _storage.parameters.jsin_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Gestire il formato JSON esteso

Per distribuire un modello con stringhe o commenti su più righe usando l'interfaccia della riga di comando di Azure con la versione 2.3.0 o precedente, è necessario usare `--handle-extended-json-format` l'opzione .  Ad esempio:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire il rollback a una distribuzione corretta quando viene restituito un errore, vedere [Rollback alla distribuzione corretta in caso di errore](rollback-on-error.md).
* Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli arm.](template-syntax.md)
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
