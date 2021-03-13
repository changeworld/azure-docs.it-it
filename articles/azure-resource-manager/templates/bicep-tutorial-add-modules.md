---
title: Esercitazione-aggiungere moduli al file di Azure Resource Manager bicipite
description: Usare i moduli per incapsulare i dettagli complessi della dichiarazione di risorse non elaborate.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633139"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Esercitazione: aggiungere moduli al file bicipite Azure Resource Manager

Nell' [esercitazione precedente](bicep-tutorial-use-parameter-file.md)si è appreso come usare un file di parametri per distribuire il file bicipite. In questa esercitazione si apprenderà come usare i moduli bicipite per incapsulare i dettagli complessi della dichiarazione di risorse non elaborate. I moduli possono essere condivisi e riusati all'interno della soluzione.  Per completare l'esercitazione, sono necessari circa **12 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Si consiglia di completare l' [esercitazione sul file dei parametri](bicep-tutorial-use-parameter-file.md), ma non è obbligatorio.

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Il file bicipite funziona correttamente. Per le soluzioni di grandi dimensioni, tuttavia, si vuole suddividere il file bicipite in molti moduli correlati, in modo da poter condividere e riutilizzare questi moduli. Il file del bicipite corrente distribuisce un account di archiviazione, un piano di servizio app e un sito Web.  Separiamo l'account di archiviazione in un modulo.

## <a name="create-bicep-module"></a>Crea modulo bicipite

Ogni file bicipite può essere utilizzato come modulo, quindi non esiste una sintassi specifica per la definizione di un modulo. Creare un file _storage. bicipite_ con il contenuto seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Questo modulo contiene la risorsa dell'account di archiviazione e i parametri e le variabili correlati. I valori per il parametro _location_ e i parametri _resourceTags_ sono stati rimossi. Questi valori verranno passati dal file principale del bicipite.

## <a name="consume-bicep-module"></a>Utilizzare il modulo bicipite

Sostituire la definizione della risorsa dell'account di archiviazione nel _file azuredeploy. bicipite_ esistente con i contenuti bicipiti seguenti:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **Module**: parola chiave.
- **nome simbolico** (STG): identificatore per il modulo.
- **file di modulo**: il percorso del modulo in questo esempio viene specificato usando un percorso relativo (/Storage.Bicep). Tutti i percorsi nel bicipite devono essere specificati usando il separatore di directory barra (/) per garantire una compilazione coerente multipiattaforma. Barra rovesciata di Windows ( \) carattere non supportato.

Per recuperare l'endpoint di archiviazione, aggiornare l'output in _file azuredeploy. bicipit_ al bicipite seguente:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Il file azuredeploy. bicipite completato presenta il contenuto seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire il modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---
> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare i gruppi di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Vengono visualizzati i due nuovi gruppi di risorse distribuiti in questa esercitazione.
1. Selezionare uno dei gruppi di risorse e visualizzare le risorse distribuite. Si noti che le risorse corrispondono ai valori specificati nel file di parametri per tale ambiente.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**. Se questa serie è stata completata, è necessario eliminare tre gruppi di risorse, ovvero **myResourceGroup**, **myResourceGroupDev** e **myResourceGroupProd**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Questa introduzione alla distribuzione dei file bicipite in Azure è stata completata. Usare la sezione dei commenti per inviare eventuali commenti e suggerimenti. Grazie.

La serie di esercitazioni successiva illustra in modo più dettagliato la distribuzione dei modelli.

> [!div class="nextstepaction"]
> [Aggiungere moduli](./bicep-tutorial-add-modules.md)
