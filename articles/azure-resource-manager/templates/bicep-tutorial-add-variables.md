---
title: Esercitazione-aggiungere una variabile al file Azure Resource Manager bicipite
description: Aggiungere variabili al file del bicipite per semplificare la sintassi.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632476"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Esercitazione: aggiungere variabili al file bicipite Azure Resource Manager

In questa esercitazione si apprenderà come aggiungere una variabile al file bicipite. Le variabili semplificano i file bicipite consentendo di scrivere un'espressione una sola volta e riusarla in tutto il file bicipite. Per completare l'esercitazione, sono necessari **7 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sulle funzioni](bicep-tutorial-add-functions.md).

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il file del bicipite aveva il contenuto seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Il parametro per il nome dell'account di archiviazione è difficile da usare perché richiede la specifica di un nome univoco. Se sono state completate le esercitazioni precedenti di questa serie, probabilmente non si ha voglia di formulare un nome univoco. Per risolvere questo problema, aggiungere una variabile che costruisce un nome univoco per l'account di archiviazione.

## <a name="use-variable"></a>Usare la variabile

Nell'esempio seguente vengono illustrate le modifiche per aggiungere una variabile al file bicipite che crea un nome di account di archiviazione univoco. Copiare l'intero file e sostituire il file bicipite con il relativo contenuto.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Si noti che include una variabile denominata `uniqueStorageName`. Questa variabile usa tre funzioni per costruire un valore stringa.

Si ha familiarità con la funzione [resourceGroup](template-functions-resource.md#resourcegroup) . In questo caso, si ottiene la proprietà `id` invece della proprietà `location`, come illustrato nell'esercitazione precedente. La proprietà `id` restituisce l'identificatore completo del gruppo di risorse, inclusi l'ID sottoscrizione e il nome del gruppo di risorse.

La funzione [uniqueString](template-functions-string.md#uniquestring) crea un valore hash di 13 caratteri. Il valore restituito dipende dai parametri passati. Per questa esercitazione come input per il valore hash si userà l'ID del gruppo di risorse. Ciò significa che è possibile distribuire questo file bicipite in gruppi di risorse diversi e ottenere un valore stringa univoco diverso. Si ottiene però lo stesso valore se si esegue la distribuzione nello stesso gruppo di risorse.

Bicipite supporta una sintassi di [interpolazione di stringhe](https://en.wikipedia.org/wiki/String_interpolation#) . Per questa variabile accetta la stringa dal parametro e la stringa dalla funzione `uniqueString` e le combina in un'unica stringa.

Il parametro `storagePrefix` consente di passare un prefisso per facilitare l'identificazione degli account di archiviazione. È possibile creare una convenzione di denominazione personalizzata per agevolare l'identificazione degli account di archiviazione dopo la distribuzione da un lungo elenco di risorse.

Si noti infine che il nome dell'account di archiviazione è ora impostato sulla variabile e non su un parametro.

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Viene ora distribuito il file bicipite. La distribuzione di questo file bicipite è più semplice dei file bicipiti precedenti perché si fornisce solo il prefisso per il nome di archiviazione.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addnamevariable \
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
1. Si noterà che è stata distribuita una risorsa dell'account di archiviazione. Il nome dell'account di archiviazione è costituito dal prefisso **store** e da una stringa di caratteri casuali.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta una variabile che consente di creare un nome univoco per un account di archiviazione. Nell'esercitazione successiva si restituirà un valore dall'account di archiviazione distribuito.

> [!div class="nextstepaction"]
> [Aggiungere gli output](bicep-tutorial-add-outputs.md)
