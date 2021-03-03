---
title: 'Esercitazione: aggiungere output al file di Azure Resource Manager bicipite'
description: Aggiungere gli output al file del bicipite per semplificare la sintassi.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 279417f22ded89db21abddad7a91a4cce520c6bd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748205"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Esercitazione: aggiungere output al file di Azure Resource Manager bicipite

In questa esercitazione si apprenderà come restituire un valore dalla distribuzione. Gli output vengono usati quando è necessario ottenere un valore da una risorsa distribuita. Per completare l'esercitazione, sono necessari **7 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sulle variabili](bicep-tutorial-add-variables.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Distribuisce un account di archiviazione, ma non restituisce informazioni relative all'account di archiviazione. Può essere necessario acquisire le proprietà da una nuova risorsa in modo che siano disponibili in seguito per riferimento.

## <a name="add-outputs"></a>Aggiungere gli output

È possibile usare gli output per restituire i valori dalla distribuzione. Può, ad esempio, essere utile ottenere gli endpoint per il nuovo account di archiviazione.

Nell'esempio seguente viene evidenziata la modifica apportata al file bicipite per aggiungere un valore di output. Copiare l'intero file e sostituire il file bicipite con il relativo contenuto.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Ecco alcune importanti aspetti da considerare sul valore di output aggiunto.

Il tipo di valore restituito è impostato su `object` , il che significa che restituisce un oggetto modello.

Per ottenere la `primaryEndpoints` proprietà dall'account di archiviazione, usare il nome simbolico dell'account di archiviazione.

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Si è pronti per distribuire il file bicipite ed esaminare il valore restituito.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Nell'output del comando di distribuzione verrà visualizzato un oggetto simile all'esempio seguente solo se l'output è in formato JSON:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="review-your-work"></a>Esaminare il lavoro

Nelle ultime sei esercitazioni sono state completate numerose procedure. Dedichiamo qualche istante a esaminare il lavoro svolto finora. È stato creato un file bicipite con parametri facili da fornire. Il file bicipite è riutilizzabile in ambienti diversi perché consente la personalizzazione e crea dinamicamente i valori necessari. Restituisce anche informazioni sull'account di archiviazione che è possibile usare nello script.

A questo punto si esaminerà il gruppo di risorse e la cronologia di distribuzione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. A seconda dei passaggi eseguiti, dovrebbero essere presenti uno o più account di archiviazione nel gruppo di risorse,
1. oltre a diverse distribuzioni riuscite nella cronologia. Selezionare il collegamento.

   ![Selezionare le distribuzioni](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Vengono visualizzate tutte le distribuzioni presenti nella cronologia. Selezionare la distribuzione denominata **addoutputs**.

   ![Visualizzare la cronologia delle distribuzioni](./media/bicep-tutorial-add-outputs/show-history.png)

1. È possibile esaminare gli input.

   ![Visualizzare gli input](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. È possibile esaminare gli output.

   ![Visualizzare gli output](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. È possibile esaminare il modello JSON.

   ![Mostrare il modello](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un valore restituito al file bicipite. Nell'esercitazione successiva si apprenderà come esportare un modello JSON e usare parti del modello esportato nel file del bicipite.

> [!div class="nextstepaction"]
> [Usare il modello esportato](bicep-tutorial-export-template.md)
