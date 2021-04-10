---
title: Esercitazione-aggiungere parametri al file di Azure Resource Manager bicipite
description: Aggiungere parametri al file bicipite per renderlo riutilizzabile.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632782"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Esercitazione: aggiungere parametri al file bicipite Azure Resource Manager

Nell' [esercitazione precedente](bicep-tutorial-create-first-bicep.md)si è appreso come distribuire un account di archiviazione. In questa esercitazione si apprenderà come migliorare il file bicipite aggiungendo parametri. Per completare l'esercitazione, sono necessari circa **14 minuti**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Si consiglia di completare la [creazione del primo file bicipite](bicep-tutorial-create-first-bicep.md), ma non è obbligatorio.

È necessario avere Visual Studio Code con l'estensione bicipite e Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [strumenti bicipite](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Esaminare il file bicipite

Al termine dell'esercitazione precedente, il bicipite ha un aspetto simile al seguente:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Probabilmente si è notato che si è verificato un problema con questo file bicipite. Il nome dell'account di archiviazione è hardcoded. È possibile usare questo file bicipite solo per distribuire ogni volta lo stesso account di archiviazione. Per distribuire un account di archiviazione con un nome diverso, è necessario creare un nuovo file bicipite, che ovviamente non è un modo pratico per automatizzare le distribuzioni.

## <a name="make-bicep-file-reusable"></a>Rendi riutilizzabile il file bicipite

Per rendere riutilizzabile il file bicipite, aggiungere un parametro che è possibile usare per passare un nome dell'account di archiviazione. Il file bicipite seguente mostra le modifiche apportate al file. Il parametro `storageName` viene identificato come stringa. La lunghezza massima è impostata su 24 caratteri per impedire nomi troppo lunghi.

Copiare l'intero file e sostituirlo con il contenuto seguente.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Si noti che è possibile fare riferimento ai parametri direttamente usando i relativi nomi in bicipite rispetto alla richiesta di [Parameters (' storagename ')] nel modello JSON ARM.

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Viene ora distribuito il file bicipite. L'esempio seguente distribuisce il file bicipite con l'interfaccia della riga di comando di Azure o PowerShell. Si noti che il nome dell'account di archiviazione viene specificato come uno dei valori nel comando di distribuzione. Per il nome dell'account di archiviazione specificare lo stesso nome usato nell'esercitazione precedente.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](bicep-tutorial-create-first-bicep.md#create-resource-group). Nell'esempio si presuppone che la variabile sia stata impostata sul `bicepFile` percorso del file bicipite, come illustrato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Informazioni sugli aggiornamenti delle risorse

Nella sezione precedente è stato distribuito un account di archiviazione con lo stesso nome creato in precedenza. È possibile che ci si chieda quale sia l'impatto della ridistribuzione sulla risorsa.

Se la risorsa esiste già e non viene rilevata alcuna modifica nelle proprietà, non viene eseguita alcuna azione. Se la risorsa esiste già ed è stata modificata una proprietà, la risorsa viene aggiornata. Se la risorsa non esiste, viene creata.

Questo metodo di gestione degli aggiornamenti significa che il file bicipite può includere tutte le risorse necessarie per una soluzione di Azure. È possibile ridistribuire in modo sicuro il file bicipite e tenere presente che le risorse vengono modificate o create solo quando necessario. Se, ad esempio, sono stati aggiunti file all'account di archiviazione, è possibile ridistribuire l'account di archiviazione senza perdere tali file.

## <a name="customize-by-environment"></a>Personalizzare in base all'ambiente

I parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente. È, ad esempio, possibile passare valori diversi a seconda che si stia eseguendo la distribuzione in un ambiente di sviluppo, test o produzione.

Il file del bicipite precedente ha sempre distribuito un account di archiviazione **Standard_LRS** . Si potrebbe però voler distribuire SKU diversi a seconda dell'ambiente. Nell'esempio seguente sono illustrate le modifiche da apportare per aggiungere un parametro per lo SKU. Copiare l'intero file e incollare il file bicipite.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

Al parametro `storageSKU` è assegnato un valore predefinito. Questo valore viene usato quando non si specifica un valore durante la distribuzione. Include anche un elenco di valori consentiti. Questi valori corrispondono a quelli necessari per creare un account di archiviazione. Non si vuole che gli utenti del file bicipite passino gli SKU che non funzionano.

## <a name="redeploy-bicep-file"></a>Ridistribuisci file bicipite

È ora possibile distribuire di nuovo l'app. Dal momento che lo SKU predefinito è impostato su **Standard_LRS**, non è necessario specificare un valore per tale parametro.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

Per visualizzare la flessibilità del file bicipite, è possibile eseguire di nuovo la distribuzione. Questa volta impostare il parametro SKU su **Standard_GRS**. È possibile passare un nuovo nome per creare un account di archiviazione diverso oppure usare lo stesso nome per aggiornare l'account di archiviazione esistente. Entrambe le opzioni sono ugualmente valide.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Eseguire infine un ulteriore test e verificare cosa succede quando si passa uno SKU che non corrisponde a uno dei valori consentiti. In questo caso, viene testato lo scenario in cui un utente del file bicipite pensa che **Basic** sia uno degli SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Il comando non viene eseguito e viene visualizzato subito un messaggio di errore che indica i valori consentiti. Resource Manager identifica l'errore prima dell'avvio della distribuzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato migliorato il file bicipite creato nella [prima esercitazione](bicep-tutorial-create-first-bicep.md) aggiungendo parametri. Nell'esercitazione successiva verranno fornite informazioni sulle funzioni bicipite.

> [!div class="nextstepaction"]
> [Aggiungi funzioni](bicep-tutorial-add-functions.md)
