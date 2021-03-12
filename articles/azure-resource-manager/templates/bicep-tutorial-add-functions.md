---
title: Esercitazione-aggiungere funzioni ai file del bicipite Azure Resource Manager
description: Aggiungere funzioni ai file Bicipit per costruire i valori.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633156"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Esercitazione: aggiungere funzioni al file Azure Resource Manager bicipite

In questa esercitazione si apprenderà come aggiungere [funzioni di modello](template-functions.md) al file bicipite. Le funzioni consentono di costruire i valori in modo dinamico. Oltre alle funzioni del modello fornite dal sistema, è anche possibile creare [funzioni definite dall'utente](./template-user-defined-functions.md). Per completare l'esercitazione, sono necessari **7 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui parametri](bicep-tutorial-add-parameters.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

La località dell'account di archiviazione è impostata come hardcoded su **Stati Uniti orientali**. Può però essere necessario distribuire l'account di archiviazione in altre aree. Si sta ripetendo un problema del file del bicipite senza flessibilità. È possibile aggiungere un parametro per la località, ma sarebbe ancor meglio se il relativo valore predefinito fosse più significativo rispetto a un valore hardcoded.

## <a name="use-function"></a>Usare la funzione

Le funzioni aggiungono flessibilità al file bicipite ottenendo dinamicamente i valori durante la distribuzione. In questa esercitazione si userà una funzione per ottenere la località del gruppo di risorse usato per la distribuzione.

Nell'esempio seguente vengono illustrate le modifiche apportate all'aggiunta di un parametro denominato `location` . Il valore predefinito del parametro chiama la funzione [resourceGroup](template-functions-resource.md#resourcegroup). Questa funzione restituisce un oggetto contenente informazioni sul gruppo di risorse usato per la distribuzione. Una delle proprietà dell'oggetto è una proprietà location. Quando si usa il valore predefinito, la località dell'account di archiviazione è uguale a quella del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non devono condividere la stessa località. Quando necessario, è anche possibile specificare una località diversa.

Copiare l'intero file e sostituire il file bicipite con il relativo contenuto.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Nelle esercitazioni precedenti è stato creato un account di archiviazione nell'area Stati Uniti orientali, ma il gruppo di risorse è stato creato nell'area Stati Uniti centrali. Per questa esercitazione l'account di archiviazione viene creato nella stessa area del gruppo di risorse. Usare il valore predefinito per location, in modo che non sia necessario specificare il valore di tale parametro. È necessario specificare un nuovo nome per l'account di archiviazione perché si sta creando un account di archiviazione in una località diversa. Usare, ad esempio, il prefisso **store2** invece di **store1**.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Si noterà che una risorsa dell'account di archiviazione è stata distribuita nella stessa località del gruppo di risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata usata una funzione durante la definizione del valore predefinito per un parametro. In questa serie di esercitazioni si continueranno a usare le funzioni. Al termine della serie, verranno aggiunte funzioni a ogni sezione del file bicipite.

> [!div class="nextstepaction"]
> [Aggiungere le variabili](bicep-tutorial-add-variables.md)
