---
title: 'Esercitazione: aggiungere tag alle risorse in un file Azure Resource Manager bicipite'
description: Aggiungere i tag alle risorse distribuite nei file Bicipit. I tag consentono di organizzare le risorse in modo logico.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632604"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Esercitazione: aggiungere tag nei file di Azure Resource Manager bicipite

In questa esercitazione si apprenderà come aggiungere i tag alle risorse nei file bicipite. I [tag](../management/tag-resources.md) consentono di organizzare le risorse in modo logico. I valori dei tag vengono visualizzati nei report sui costi. Per completare l'esercitazione, sono necessari **8 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui modelli di avvio rapido](bicep-tutorial-quickstart-template.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Il file del bicipite precedente ha distribuito un account di archiviazione, un piano di servizio app e un'app Web.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Dopo la distribuzione di queste risorse, può essere necessario tenere traccia dei costi e individuare le risorse appartenenti a una categoria. Per risolvere questi problemi, è possibile aggiungere tag.

## <a name="add-tags"></a>Aggiungi tag

L'aggiunta di un tag alle risorse consente di aggiungere valori per identificarne più facilmente l'uso. È ad esempio possibile aggiungere tag che elencano l'ambiente e il progetto, nonché aggiungere tag che identificano un centro di costo oppure il team a cui appartiene la risorsa. Aggiungere tutti i valori appropriati per l'organizzazione.

Nell'esempio seguente vengono illustrate le modifiche apportate al file bicipite. Copiare l'intero file e sostituire il file bicipite con il relativo contenuto.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

È il momento di distribuire il file bicipite ed esaminare i risultati.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Selezionare una delle risorse, ad esempio la risorsa dell'account di archiviazione. Si noterà che ora include tag.

   ![Visualizzare i tag](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati aggiunti tag alle risorse. Nell'esercitazione successiva si apprenderà come usare i file di parametri per semplificare il passaggio dei valori alla distribuzione.

> [!div class="nextstepaction"]
> [Usare il file di parametri](bicep-tutorial-use-parameter-file.md)
