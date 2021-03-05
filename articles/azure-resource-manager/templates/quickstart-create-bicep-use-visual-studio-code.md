---
title: Crea file bicipite-Visual Studio Code
description: Usare Visual Studio Code e l'estensione bicipite per i file bicipite per la distribuzione delle risorse di Azure
author: mumian
ms.date: 03/02/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: ab1d7b88321ce5959b99423ae2ca1332369ef691
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179008"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Guida introduttiva: creare file bicipite con Visual Studio Code

L'estensione bicipite per Visual Studio Code fornisce il supporto del linguaggio e il completamento automatico delle risorse. Questi strumenti consentono di creare e convalidare i file [bicipite](./bicep-overview.md) . In questa Guida introduttiva si userà l'estensione per creare un file bicipite da zero. In questo modo si verificano le funzionalità di estensione, ad esempio la convalida e i completamenti.

Per completare questa Guida introduttiva, è necessario [Visual Studio Code](https://code.visualstudio.com/), con l' [estensione bicipit](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) installata. È necessario anche l'interfaccia della riga di comando di [Azure](/cli/azure/) più recente o il [modulo Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-3.7.0&preserve-view=true) più recente installato e autenticato.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-bicep-file"></a>Creare un file bicipite

Creare e aprire con Visual Studio Code un nuovo file denominato *file azuredeploy. bicipit*.

## <a name="add-an-azure-resource"></a>Aggiungere una risorsa di Azure

Aggiungere una risorsa dell'account di archiviazione di base al file bicipite.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

La dichiarazione di risorsa è costituita da quattro componenti:

- **Resource**: parola chiave.
- **nome simbolico** (STG): il nome simbolico è un identificatore per fare riferimento alla risorsa nel file bicipite. Non si tratta del nome della risorsa quando viene distribuito. Il nome della risorsa viene definito dalla proprietà **Name** .  Vedere il quarto componente in questo elenco.
- **tipo di risorsa** ( Microsoft.Storage/storageAccounts@2019-06-01 ): è costituito dal provider di risorse (Microsoft. Storage), dal tipo di risorsa (storageAccounts) e da apiVersion (2019-06-01). Ogni provider di risorse pubblica le proprie versioni API, di conseguenza questo valore è specifico del tipo. Per altre risorse di Azure, vedere informazioni di [riferimento sui modelli ARM](/azure/templates/).
- **Proprietà** (tutti gli elementi all'interno di = {...}): specificare le proprietà per il tipo di risorsa. Ogni risorsa ha una `name` Proprietà. Alla maggior parte delle risorse è assegnata anche una proprietà `location`, che consente di impostare l'area in cui viene distribuita la risorsa. Le altre proprietà variano in base al tipo di risorsa e alla versione dell'API.

## <a name="completion-and-validation"></a>Completamento e convalida

Una delle funzionalità più avanzate dell'estensione è l'integrazione con gli schemi di Azure. Gli schemi di Azure forniscono all'estensione funzionalità di convalida e completamento con riconoscimento della risorsa. Modificare l'account di archiviazione per vedere il funzionamento di convalida e completamento.

Aggiornare prima di tutto il tipo di account di archiviazione impostandolo su un valore non valido, ad esempio `megaStorage`. Si noti che questa azione genera un avviso che indica che `megaStorage` non è un valore valido.

![Immagine che mostra una configurazione di archiviazione non valida](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Per utilizzare le funzionalità di completamento, rimuovere `megaStorage` , posizionare il cursore all'interno delle virgolette singole e premere `ctrl`  +  `space` . Questa azione presenta un elenco di completamento di valori validi.

![Immagine che mostra il completamento automatico dell'estensione](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Aggiungere parametri

A questo punto, creare e usare un parametro per specificare il nome dell'account di archiviazione.

Aggiungere il codice seguente all'inizio del file:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

I nomi degli account di archiviazione di Azure possono essere composti da un minimo di 3 e un massimo di 24 caratteri. Usare `minLength` e `maxLength` per fornire i valori appropriati.

A questo punto, nella risorsa di archiviazione aggiornare la proprietà name in modo da usare il parametro. A tale scopo, rimuovere il nome della risorsa di archiviazione corrente, incluse le virgolette singole. Premere `ctrl`  +  `space` . Selezionare il parametro **storageAccountName** dall'elenco. Si noti che è possibile fare riferimento ai parametri direttamente usando i relativi nomi in bicipite. I modelli JSON richiedono una funzione Parameter ().

![Immagine che mostra il completamento automatico quando si usano i parametri nelle risorse bicipite](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Distribuire il file bicipite

Aprire il terminale di Visual Studio Code integrato usando la `ctrl`  +  ```` ` ```` combinazione di tasti, modificare la directory corrente in cui si trova il file bicipite e quindi usare l'interfaccia della riga di comando di Azure o il modulo Azure PowerShell per distribuire il file bicipite.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse non sono più necessarie, usare l'interfaccia della riga di comando di Azure o il modulo di Azure PowerShell per eliminare il gruppo di risorse dell'argomento di avvio rapido.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazioni sul bicipite per principianti](./bicep-tutorial-create-first-bicep.md)
