---
title: 'Esercitazione: usare modelli di avvio rapido per lo sviluppo di Azure Resource Manager bicipite'
description: Informazioni su come usare i modelli di avvio rapido di Azure per completare lo sviluppo bicipite.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632434"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Esercitazione: usare i modelli di avvio rapido di Azure per lo sviluppo di Azure Resource Manager bicipite

[Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) è un repository di modelli JSON forniti dalla community. È possibile usare i modelli di esempio nello sviluppo bicipite. In questa esercitazione viene illustrata una definizione di risorsa del sito Web, viene decompilata in bicipite e aggiunta al file bicipite. Per completare l'esercitazione, sono necessari circa **12 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui modelli esportati](bicep-tutorial-export-template.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Questo file bicipite funziona per la distribuzione di account di archiviazione e piani di servizio app, ma potrebbe essere necessario aggiungervi un sito Web. È possibile usare i modelli predefiniti per individuare rapidamente il codice JSON necessario per la distribuzione di una risorsa. Prima che i modelli di avvio rapido di Azure offrano esempi di bicipite, è possibile usare gli strumenti di conversione per convertire modelli JSON in file bicipite.

## <a name="find-template"></a>Trovare il modello

Attualmente, i modelli di avvio rapido di Azure forniscono solo modelli JSON. Sono disponibili strumenti che è possibile usare per decompilare modelli JSON nei modelli bicipite.

1. Aprire i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
1. In **Cerca** immettere _distribuisci app Web Linux_.
1. Selezionare il riquadro con il titolo **Deploy a basic Linux web app** (Distribuire un'app Web Linux di base). Se non si riesce a trovarlo, ecco il [collegamento diretto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selezionare **Sfoglia su GitHub**.
1. Selezionare _azuredeploy.json_. Questo è il modello che è possibile usare.
1. Selezionare **RAW**, quindi creare una copia dell'URL.
1. Passare a **https://bicepdemo.z22.web.core.windows.net/** , selezionare **decompile**, quindi specificare l'URL del modello non elaborato.
1. Esaminare il modello bicipite. In particolare, cercare la risorsa `Microsoft.Web/sites`.

    ![Avvio rapido per i modelli di Resource Manager: sito Web](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Esistono due importanti funzionalità di bicipite da tenere presente in questa nuova risorsa se si è lavorato sui modelli JSON.

    Nei modelli JSON ARM è necessario specificare manualmente le dipendenze delle risorse con la proprietà _dependsOn_ . La risorsa del sito Web dipende dalla risorsa del piano di servizio app. Con bicipite, se si fa riferimento a qualsiasi proprietà della risorsa usando il nome simbolico, la proprietà dependsOn viene aggiunta automaticamente.

    È possibile fare facilmente riferimento all'ID risorsa dal nome simbolico del piano di servizio app (appServicePlanName.id) che verrà convertito nella funzione resourceId (...) nel modello JSON compilato.

## <a name="revise-existing-bicep-file"></a>Modificare il file bicipite esistente

Unire il modello di avvio rapido decompilato con il file bicipite esistente. Come per l'esercitazione precedente, aggiornare il nome simbolico della risorsa e il nome della risorsa in modo che corrisponda alla convenzione di denominazione.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Il nome dell'app Web deve essere univoco in Azure. Per evitare nomi duplicati, la variabile `webAppPortalName` è stata aggiornata da `var webAppPortalName_var = '${webAppName}-webapp'` a `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Usare l'interfaccia della riga di comando di Azure o Azure PowerShell per distribuire un modello bicipite.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che sia stata impostata la variabile **bicepFile** sul percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come usare un modello di avvio rapido per lo sviluppo bicipite. Nell'esercitazione successiva si aggiungeranno tag alle risorse.

> [!div class="nextstepaction"]
> [Aggiungere i tag](bicep-tutorial-add-tags.md)
