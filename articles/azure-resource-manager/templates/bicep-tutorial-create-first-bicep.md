---
title: 'Esercitazione: creare & distribuire file Azure Resource Manager bicipite'
description: Creare il primo file bicipite per la distribuzione delle risorse di Azure. Nell'esercitazione vengono fornite informazioni sulla sintassi dei file bicipite e su come distribuire un account di archiviazione.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594275"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Esercitazione: creare e distribuire il primo file Azure Resource Manager bicipite

Questa esercitazione introduce il [bicipite](./bicep-overview.md). Viene illustrato come creare un file Starter bicipite e distribuirlo in Azure. Verranno fornite informazioni sulla struttura del file bicipite e sugli strumenti necessari per l'uso dei file bicipite. Per completare l'esercitazione sono necessari circa **12 minuti**, ma il tempo effettivo può variare in base al numero di strumenti da installare.

Questa è la prima esercitazione di una serie. Mentre si procede attraverso la serie, è possibile modificare il file bicipite iniziale passo dopo passo fino a quando non si esplorano tutte le parti principali di un file bicipite. Questi elementi sono i blocchi predefiniti per file bicipite molto più complessi. Ci auguriamo che alla fine della serie tu sia sicuro creare i tuoi file bicipite e pronto per automatizzare le distribuzioni con i file bicipite.

Per informazioni sui vantaggi derivanti dall'uso di bicipite e sui motivi per cui è consigliabile automatizzare la distribuzione con i file bicipite, vedere [bicipite](./bicep-overview.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Ottenere gli strumenti

Per iniziare, verificare di disporre degli strumenti necessari per creare e distribuire i file bicipite. Installare questi strumenti nel computer locale.

### <a name="editor"></a>Editor

Per creare i file bicipite, è necessario un editor valido. Si consiglia di Visual Studio Code con l'estensione bicipite. Se è necessario installare questi strumenti, vedere [configurare l'ambiente di sviluppo bicipite](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Distribuzione dalla riga di comando

È possibile distribuire i file bicipite usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Per l'interfaccia della riga di comando di Azure è necessaria la versione 2.20.0 o successiva. per Azure PowerShell, è necessaria la versione 5.6.0 o successiva. Per le istruzioni di installazione, vedere:

- [Installare Azure PowerShell](/powershell/azure/install-az-ps)
- [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows)
- [Installare l'interfaccia della riga di comando di Azure in Linux](/cli/azure/install-azure-cli-linux)
- [Installare l'interfaccia della riga di comando di Azure in macOS](/cli/azure/install-azure-cli-macos)

Dopo aver installato Azure PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di accedere per la prima volta. Per informazioni, vedere [Accesso - PowerShell](/powershell/azure/install-az-ps#sign-in) or [Accesso - Interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Si è pronti per iniziare a conoscere il bicipite.

## <a name="create-your-first-bicep-file"></a>Creare il primo file bicipite

1. Aprire Visual Studio Code con l'estensione Bicipit installata.
1. Nel menu **File** selezionare **Nuovo file** per creare un nuovo file.
1. Nel menu **File** selezionare **Salva con nome**.
1. Denominare il file _file azuredeploy_ e selezionare l'estensione di file _bicipite_ . Il nome completo del file è _file azuredeploy. bicipit_.
1. Salvare il file nella workstation. Selezionare un percorso facile da ricordare perché questo percorso verrà fornito in un secondo momento durante la distribuzione del file bicipite.
1. Copiare e incollare il contenuto seguente nel file:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Ecco come appare l'ambiente Visual Studio Code:

    ![Visual Studio Code primo file bicipite](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    La dichiarazione di risorsa è costituita da quattro componenti:

    - **Resource**: parola chiave.
    - **nome simbolico** (STG): un nome simbolico è un identificatore per fare riferimento alla risorsa nell'intero file bicipite. Non si tratta del nome della risorsa quando viene distribuito. Il nome della risorsa viene definito dalla proprietà **Name** .  Vedere il quarto componente in questo elenco. Per semplificare la procedura, è possibile usare **STG** come nome simbolico per la risorsa dell'account di archiviazione in questa serie di esercitazioni. Per informazioni su come usare il nome simbolico per ottenere un elenco completo delle proprietà dell'oggetto, vedere [aggiungere output](./bicep-tutorial-add-outputs.md).
    - **tipo di risorsa** ( Microsoft.Storage/storageAccounts@2019-06-01 ): è costituito dal provider di risorse (Microsoft. Storage), dal tipo di risorsa (storageAccounts) e da apiVersion (2019-06-01). Ogni provider di risorse pubblica le proprie versioni API, di conseguenza questo valore è specifico del tipo. Per altre risorse di Azure, vedere informazioni di [riferimento sui modelli ARM](/azure/templates/).
    - **Properties** (tutti gli elementi all'interno di = {...}): proprietà specifiche che si desidera specificare per il tipo di risorsa specificato. Si tratta esattamente delle stesse proprietà disponibili in un modello ARM. Ogni risorsa ha una `name` Proprietà. Alla maggior parte delle risorse è assegnata anche una proprietà `location`, che consente di impostare l'area in cui viene distribuita la risorsa. Le altre proprietà variano in base al tipo di risorsa e alla versione dell'API. Dal momento che è importante comprendere la connessione tra la versione dell'API e le proprietà disponibili, verranno approfonditi questi aspetti.

        Per questo account di archiviazione, è possibile vedere la versione dell'API in [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Si noti che non sono state aggiunte tutte le proprietà al file bicipite. Molte proprietà sono infatti facoltative. Il provider di risorse `Microsoft.Storage` potrebbe rilasciare una nuova versione dell'API, ma non è necessario modificare la versione distribuita. È possibile continuare a usare tale versione ed essere certi che i risultati della distribuzione saranno comunque coerenti.

        Se si visualizza una versione precedente dell'API, ad esempio [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), si noterà che è disponibile un set più piccolo di proprietà.

        Se si decide di modificare la versione dell'API per una risorsa, assicurarsi di valutare le proprietà della versione e di regolare correttamente il file bicipite.

1. Sostituire `{provide-unique-name}` includendo le parentesi graffe `{}` con un nome di account di archiviazione univoco.

    > [!IMPORTANT]
    > Il nome dell'account di archiviazione deve essere univoco in Azure. Il nome deve essere composto solo da lettere minuscole e numeri e non deve superare i 24 caratteri. Come criterio di denominazione è possibile provare a usare il prefisso **store1** e aggiungere le proprie iniziali e la data odierna. Il nome usato può, ad esempio, essere simile a **store1abc09092019**.

    Formulare un nome univoco per un account di archiviazione non è semplice e non funziona correttamente per l'automazione di distribuzioni di grandi dimensioni. Più avanti in questa serie di esercitazioni si useranno le funzionalità bicipite che semplificano la creazione di un nome univoco.

1. Salvare il file.

Congratulazioni, è stato creato il primo file bicipite.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per iniziare a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, accedere con le credenziali di Azure.

Selezionare le schede nelle sezioni di codice seguenti per scegliere tra Azure PowerShell e l'interfaccia della riga di comando di Azure. Gli esempi dell'interfaccia della riga di comando in questo articolo sono scritti per la shell Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se si hanno più sottoscrizioni di Azure, selezionare quella da usare. Sostituire `[SubscriptionID/SubscriptionName]` e le parentesi quadre `[]` con le informazioni sulla sottoscrizione:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Quando si distribuisce un file bicipite, si specifica un gruppo di risorse che conterrà le risorse. Prima di eseguire il comando di distribuzione, creare il gruppo di risorse usando l'interfaccia della riga di comando di Azure oppure Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Distribuisci file bicipite

Bicipite è un'astrazione trasparente su modelli di Azure Resource Manager (modelli ARM). Ogni file bicipite viene compilato in un modello ARM standard prima della distribuzione. È possibile compilare il file del bicipite in un modello ARM prima di distribuirlo o distribuire direttamente il file bicipite. Per distribuire il file bicipite, usare l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare il gruppo di risorse creato. Assegnare un nome alla distribuzione in modo da poterla identificare facilmente nella cronologia di distribuzione. Per praticità, creare anche una variabile che archivia il percorso del file bicipite. Questa variabile semplifica l'esecuzione dei comandi di distribuzione perché non è necessario digitare nuovamente il percorso a ogni nuova distribuzione. Sostituire `{provide-the-path-to-the-bicep-file}` incluse le parentesi graffe `{}` con il percorso del file bicipite con il nome dell'estensione di file _bicipite_ .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire questo cmdlet di distribuzione, è necessario disporre della [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Il comando di distribuzione restituisce risultati. Cercare `ProvisioningState` per verificare se la distribuzione è riuscita.

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione `verbose` per ottenere informazioni sulle risorse create. Usare l'opzione `debug` per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu di sinistra selezionare **Gruppi di risorse**.

1. Selezionare il gruppo di risorse distribuito nell'ultima procedura. Il nome predefinito è **myResourceGroup**. Si noterà che nel gruppo di risorse non ci sono risorse distribuite.

1. Notare che in alto a destra nella panoramica viene visualizzato lo stato della distribuzione. Selezionare **1 riuscita**.

   ![Visualizzare lo stato della distribuzione](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Viene visualizzata una cronologia delle distribuzioni per il gruppo di risorse. Selezionare **firstbicep**.

   ![Selezionare la distribuzione](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Viene visualizzato un riepilogo della distribuzione. È stato distribuito un account di archiviazione. Si noti che a sinistra è possibile visualizzare gli input, gli output e il modello usato durante la distribuzione.

   ![Visualizzare il riepilogo della distribuzione](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato creato un semplice file bicipite per la distribuzione in Azure.  Nelle esercitazioni successive si apprenderà come aggiungere parametri, variabili, output e moduli a un file bicipite. Queste funzionalità sono i blocchi predefiniti per file bicipite molto più complessi.

> [!div class="nextstepaction"]
> [Aggiungere i parametri](bicep-tutorial-add-parameters.md)
