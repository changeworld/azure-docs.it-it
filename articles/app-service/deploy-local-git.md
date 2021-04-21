---
title: Distribuire dal repository GIT locale
description: Informazioni su come abilitare la distribuzione dell'archivio Git locale nel servizio app di Azure. Uno dei modi più semplici per distribuire il codice dal computer locale.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e0dc9093503cab92a71517a21a8788814d16cbbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772866"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione dell'archivio Git locale nel servizio app di Azure

Questa guida dettagliata illustra come distribuire l'app in Servizio app di Azure [da](overview.md) un repository Git nel computer locale.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida dettagliata:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installare Git](https://www.git-scm.com/downloads).

- Avere un repository Git locale con il codice che si vuole distribuire. Per scaricare un repository di esempio, eseguire il comando seguente nella finestra del terminale locale:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

Vedere [Configurare le credenziali di distribuzione per Servizio app di Azure](deploy-configure-credentials.md). È possibile usare le credenziali dell'ambito utente o le credenziali dell'ambito dell'applicazione.

## <a name="create-a-git-enabled-app"></a>Creare un'app abilitata per Git

Se si ha già un'app del servizio app e si vuole configurare la distribuzione Git locale, vedere [Configurare un'app](#configure-an-existing-app) esistente.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire [`az webapp create`](/cli/azure/webapp#az_webapp_create) con `--deployment-local-git` l'opzione . Ad esempio:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

L'output contiene un URL simile al seguente: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Usare questo URL per distribuire l'app nel passaggio successivo.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Eseguire [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) dalla radice del repository Git. Ad esempio:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Quando si esegue questo cmdlet da una directory che è un repository Git, viene automaticamente creato un database Git remoto per l'app del servizio app, denominato `azure` .

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Nel portale è prima necessario creare un'app e quindi configurarne la distribuzione. Vedere [Configurare un'app esistente.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Configurare un'app esistente

Se non è ancora stata creata un'app, vedere [Creare un'app abilitata per Git.](#create-a-git-enabled-app)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) . Ad esempio:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

L'output contiene un URL simile al seguente: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Usare questo URL per distribuire l'app nel passaggio successivo.

> [!TIP]
> Questo URL contiene il nome utente della distribuzione dell'ambito utente. Se si desidera, è possibile [usare le credenziali dell'ambito dell'applicazione.](deploy-configure-credentials.md#appscope) 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Impostare `scmType` l'oggetto dell'app eseguendo il cmdlet [Set-AzResource.](/powershell/module/az.resources/set-azresource)

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

1. Nel [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app.

1. Nel menu a sinistra selezionare **Impostazioni centro**  >  **distribuzione.** Selezionare **Git locale** in Origine **e** quindi fare clic su **Salva.**

    ![Illustra come abilitare la distribuzione Git locale per il servizio app nel portale di Azure](./media/deploy-local-git/enable-portal.png)

1. Nella sezione Git locale copiare **l'URI git clone per** un momento successivo. Questo URI non contiene credenziali.

-----

## <a name="deploy-the-web-app"></a>Distribuire l'app Web

1. In una finestra del terminale locale passare alla directory radice del repository Git e aggiungere un repository Git remoto usando l'URL ottenuto dall'app. Se il metodo scelto non restituisce un URL, usare `https://<app-name>.scm.azurewebsites.net/<app-name>.git` con il nome dell'app in `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Se è [stata creata un'app abilitata per Git in PowerShell usando New-AzWebApp,](#create-a-git-enabled-app)il computer remoto è già stato creato automaticamente.
   
1. Eseguire il push nell'istanza remota di Azure con `git push azure master` . 
   
1. Nella finestra **git Gestione credenziali** immettere le credenziali dell'ambito utente o dell'ambito dell'applicazione [e](#configure-a-deployment-user)non le credenziali di accesso di Azure.

    Se l'URL remoto Git contiene già il nome utente e la password, non verrà richiesto. 
   
1. Esaminare l'output. È possibile visualizzare l'automazione specifica del runtime, ad esempio MSBuild per ASP.NET, `npm install` per Node.js e per `pip install` Python. 
   
1. Passare all'app nel portale di Azure per verificare che il contenuto sia distribuito.

## <a name="troubleshoot-deployment"></a>Risolvere i problemi di distribuzione

Quando si usa Git per pubblicare in un'app del servizio app in Azure, possono essere visualizzati i messaggi di errore comuni seguenti:

|Message|Causa|Soluzione
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|L'app non è in esecuzione.|avviare l'app nel portale di Azure. La distribuzione Git non è disponibile quando l'app Web viene arrestata.|
|`Couldn't resolve host 'hostname'`|Le informazioni sull'indirizzo per il remoto 'azure' non sono corrette.|usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Non è stato specificato un ramo durante o non è `git push` stato impostato il valore in `push.default` `.gitconfig` .|Eseguire `git push` di nuovo, specificando il ramo principale: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|È stato inserito un ramo locale che non corrisponde al ramo di distribuzione dell'app in 'azure'.|Verificare che current branch sia `master` . Per modificare il ramo predefinito, usare `DEPLOYMENT_BRANCH` l'impostazione dell'applicazione.|
|`src refspec [branchname] does not match any.`|Si è provato a eseguire il push in un ramo diverso da main nel remoto 'azure'.|Eseguire `git push` di nuovo, specificando il ramo principale: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|questo errore può verificarsi se si tenta di eseguire il push di un repository Git di grandi dimensioni tramite HTTPS.|Modificare la configurazione git nel computer locale per aumentarne le `postBuffer` dimensioni. Ad esempio: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|È stata distribuita un'app Node.js con un _package.jsnel_ file che specifica moduli aggiuntivi necessari.|Esaminare i `npm ERR!` messaggi di errore prima di questo errore per un maggiore contesto sull'errore. Di seguito sono riportate le cause note di questo errore e i messaggi `npm ERR!` corrispondenti:<br /><br />**Errore di package.jsnel file**: `npm ERR! Couldn't read dependencies.`<br /><br />**Il modulo nativo non ha una distribuzione binaria per Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />oppure <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Server di compilazione del servizio app (documentazione di Project Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Distribuzione continua nel servizio app di Azure](deploy-continuous-deployment.md)
- [Esempio: Creare un'app Web e distribuire il codice da un repository Git locale (interfaccia della riga di comando di Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Esempio: Creare un'app Web e distribuire il codice da un repository Git locale (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
