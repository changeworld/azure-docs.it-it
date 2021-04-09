---
title: Esercitazione-esportare un modello JSON dal portale di Azure per lo sviluppo di bicipiti
description: Informazioni su come usare un modello JSON esportato per completare lo sviluppo bicipite.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632550"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Esercitazione: usare il modello JSON esportato dalla portale di Azure

In questa serie di esercitazioni è stato creato un file bicipite per distribuire un account di archiviazione di Azure. Nelle due esercitazioni successive verrà aggiunto un *piano di servizio app* e un *sito Web*. Anziché creare modelli da zero, si apprenderà come esportare i modelli JSON dal portale di Azure e come usare i modelli di esempio dei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/). e si personalizzeranno tali modelli per l'uso. Questa esercitazione è incentrata sull'esportazione di modelli e sulla personalizzazione del risultato per il file bicipite. Per completare l'esercitazione, sono necessari circa **14 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sugli output](bicep-tutorial-add-outputs.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Questo file bicipite funziona correttamente per la distribuzione degli account di archiviazione, ma potrebbe essere necessario aggiungervi altre risorse. È possibile esportare un modello JSON da una risorsa esistente per ottenere rapidamente il codice JSON per tale risorsa. Quindi convertire il codice JSON in bicipite prima di poterlo aggiungere al file bicipite.

## <a name="create-app-service-plan"></a>Creare un piano di servizio app

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**.
1. In **Cerca nel Marketplace** immettere **Piano di servizio app** e quindi selezionare **Piano di servizio app**.  Non selezionare **Piano di servizio app (versione classica)**
1. Selezionare **Create** (Crea).
1. Digitare:

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e quindi specificare un nome. Specificare un nome di gruppo di risorse diverso rispetto a quello usato in questa serie di esercitazioni.
    - **Nome**: immettere un nome per il piano di servizio app.
    - **Sistema operativo**: selezionare **Linux**.
    - **Località**: selezionare una località di Azure. Ad esempio **Stati Uniti centrali**.
    - **Piano tariffario**: per ridurre i costi, modificare lo SKU in **Basic B1** (in Sviluppo/test).

    ![Modello di Resource Manager - Esporta modello - Portale](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea). La creazione della risorsa richiede alcuni minuti.

## <a name="export-template"></a>Esportare il modello

Attualmente, il portale di Azure supporta solo l'esportazione di modelli JSON. Sono disponibili strumenti che è possibile usare per decompilare modelli JSON in file bicipite.

1. Selezionare **Vai alla risorsa**.

    ![Vai alla risorsa](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selezionare **Esporta modello**.

    ![Modello di Resource Manager - Esporta modello](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   La funzionalità Esporta modello accetta lo stato corrente di una risorsa e genera un modello per distribuirlo. L'esportazione di un modello può essere utile per ottenere rapidamente il codice JSON necessario per distribuire una risorsa.

1. La `Microsoft.Web/serverfarms` definizione e la definizione dei parametri sono le parti necessarie.

    ![Modello di Resource Manager - Esporta modello - Modello esportato](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > In genere, il modello esportato è più dettagliato di quanto necessario quando si crea un modello. Ad esempio, l'oggetto SKU nel modello esportato include cinque proprietà. Questo modello funziona, ma è possibile usare semplicemente la `name` Proprietà. È possibile iniziare con il modello esportato e quindi modificarlo in base alle esigenze.

1. Selezionare **Download**.  Il file zip scaricato contiene un **template.js** e un **parameters.jssu**. Decomprimere i file.
1. Individuare **https://bicepdemo.z22.web.core.windows.net/** , selezionare **decompile**, quindi aprire **template.js**. Il modello viene ottenuto in bicipite.

## <a name="revise-existing-bicep-file"></a>Modificare il file bicipite esistente

Il modello esportato derispettato offre la maggior parte del bicipite necessario, ma è necessario personalizzarlo per il file bicipite. Prestare particolare attenzione alle differenze tra parametri e variabili tra il file bicipite e il file bicipite esportato. Ovviamente, il processo di esportazione non conosce i parametri e le variabili già definiti nel file bicipite.

Nell'esempio seguente vengono illustrate le aggiunte al file bicipite. Contiene infatti il codice esportato oltre ad alcune modifiche. In primo luogo, modifica il nome del parametro in modo che corrisponda alla convenzione di denominazione. Quindi, usa il parametro location per la località del piano di servizio app. In terzo luogo, rimuove alcune proprietà in cui il valore predefinito è corretto.

Copiare l'intero file e sostituire il file bicipite con il relativo contenuto.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Usare l'interfaccia della riga di comando di Azure o Azure PowerShell per distribuire un file bicipite.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Il gruppo di risorse contiene un account di archiviazione e un piano di servizio app.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come esportare un modello JSON dalla portale di Azure, come convertire il modello JSON in un file bicipite e come usare il modello esportato per lo sviluppo bicipite. È anche possibile usare i modelli di avvio rapido di Azure per semplificare lo sviluppo di bicipiti.

> [!div class="nextstepaction"]
> [Usare i modelli di avvio rapido di Azure](bicep-tutorial-quickstart-template.md)
