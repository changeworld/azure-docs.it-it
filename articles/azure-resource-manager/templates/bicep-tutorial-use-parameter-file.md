---
title: Esercitazione-usare il file di parametri per distribuire il file del bicipite Azure Resource Manager
description: Usare i file di parametri che contengono i valori da usare per distribuire il file bicipite.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 233bab26d659fef2da9a9f5a7080d4b3ecbce7d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748189"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Esercitazione: usare i file di parametri per distribuire il file bicipite Azure Resource Manager

Questa esercitazione illustra come usare i [file di parametri](parameter-files.md) per archiviare i valori passati durante la distribuzione. Nelle esercitazioni precedenti sono stati usati parametri inline con il comando di distribuzione. Questo approccio ha funzionato per il test del file bicipite, ma quando si automatizzano le distribuzioni può essere più semplice passare un set di valori per l'ambiente. I file di parametri semplificano la creazione di un pacchetto di valori di parametri per un ambiente specifico. Si usa lo stesso file di parametri JSON quando si distribuisce un modello JSON. In questa esercitazione si creeranno file di parametri per gli ambienti di sviluppo e di produzione. Per completare l'esercitazione, sono necessari circa **12 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui tag](bicep-tutorial-add-tags.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Il file del bicipite ha molti parametri che è possibile fornire durante la distribuzione. Al termine dell'esercitazione precedente, il file bicipite ha un aspetto simile al seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Questo file bicipite funziona correttamente, ma ora si vuole gestire facilmente i parametri passati per il file bicipite.

## <a name="add-parameter-files"></a>Aggiungere file di parametri

I file dei parametri sono file JSON con una struttura simile ai modelli JSON. Nel file è possibile specificare i valori dei parametri da passare durante la distribuzione.

Nel file dei parametri è possibile specificare i valori per i parametri nel file bicipite. Il nome di ogni parametro nel file di parametri deve corrispondere al nome di un parametro nel file bicipite. Il nome non fa distinzione tra maiuscole e minuscole, ma per visualizzare facilmente i valori corrispondenti si consiglia di trovare la corrispondenza tra le maiuscole e minuscole del file bicipite.

Non è necessario specificare un valore per ogni parametro. Se un parametro non specificato ha un valore predefinito, verrà usato durante la distribuzione. Se un parametro non ha un valore predefinito e non viene specificato nel file di parametri, viene chiesto di specificarlo durante la distribuzione.

Non è possibile specificare un nome di parametro nel file di parametri che non corrisponde al nome di un parametro nel file bicipite. Se si specificano parametri sconosciuti, si riceve un errore.

In Visual Studio Code creare un nuovo file con il contenuto seguente. Salvare il file con il nome _azuredeploy.parameters.dev.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Questo è il file di parametri per l'ambiente di sviluppo. Si noti che usa **Standard_LRS** come account di archiviazione, aggiunge il prefisso **dev** alle risorse e imposta il tag `Environment` su **Dev**.

Creare un nuovo file con il contenuto seguente. Salvare il file con il nome _azuredeploy.parameters.prod.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Questo è il file di parametri per l'ambiente di produzione. Si noti che usa **Standard_GRS** come account di archiviazione, aggiunge il prefisso **contoso** alle risorse e imposta il tag `Environment` su **Production**. In un ambiente di produzione reale si vuole anche usare un servizio app con uno SKU diverso da quello gratuito, ma per questa esercitazione si continuerà a usare lo SKU gratuito.

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Usare l'interfaccia della riga di comando di Azure o Azure PowerShell per distribuire il file bicipite.

In questa esercitazione vengono creati due nuovi gruppi di risorse. uno per l'ambiente di sviluppo e uno per l'ambiente di produzione.

Per le variabili del modello e dei parametri, sostituire `{path-to-the-bicep-file}` , `{path-to-azuredeploy.parameters.dev.json}` , `{path-to-azuredeploy.parameters.prod.json}` e le parentesi graffe `{}` con il file bicipite e i percorsi dei file di parametri.

Prima di tutto, verrà eseguita la distribuzione nell'ambiente di sviluppo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

A questo punto, verrà eseguita la distribuzione nell'ambiente di produzione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
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
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si userà un file di parametri per distribuire il file bicipite. Nell'esercitazione successiva si apprenderà come modularizzare i file bicipite.

> [!div class="nextstepaction"]
> [Aggiungere moduli](./bicep-tutorial-add-modules.md)
