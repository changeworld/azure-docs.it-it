---
title: Configurare la distribuzione continua
description: Informazioni su come abilitare l'integrazione continua/distribuzione continua per app Azure servizio da GitHub, BitBucket, Azure Repos o altri repository. Selezionare la pipeline di compilazione che soddisfa le proprie esigenze.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 5af8294518759181326e7736ef755f0a83581014
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564961"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure

Il [servizio app Azure](overview.md) consente la distribuzione continua da [GitHub](https://help.github.com/articles/create-a-repo), [bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)e [Azure Repos](/azure/devops/repos/git/creatingrepo) repository eseguendo il pull degli aggiornamenti più recenti.

> [!NOTE]
> La pagina **centro di sviluppo (classica)** nell'portale di Azure, che è l'esperienza di distribuzione precedente, sarà deprecata a partire da marzo 2021. Questa modifica non avrà alcun effetto sulle impostazioni di distribuzione esistenti nell'app ed è possibile continuare a gestire la distribuzione delle app nella pagina **centro distribuzione** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Configurare l'origine della distribuzione

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

1. Nel menu a sinistra fare clic su impostazioni di **Deployment Center**  >  . 

1. In **origine** selezionare una delle opzioni ci/CD.

    ![Mostra come scegliere un'origine di distribuzione in centro distribuzione per app Azure servizio](media/app-service-continuous-deployment/choose-source.png)

Scegliere la scheda che corrisponde alla selezione per i passaggi.

# <a name="github"></a>[GitHub](#tab/github)

4. [Azioni di GitHub](#how-the-github-actions-build-provider-works) è il provider di compilazione predefinito. Per modificarlo, fare clic su **cambia provider**  >  **app Service Build Service** (kudu) > **OK**.

    > [!NOTE]
    > Per usare Azure Pipelines come provider di compilazione per l'app del servizio app, non configurarlo nel servizio app. In alternativa, configurare CI/CD direttamente da Azure Pipelines. L'opzione **Azure Pipelines** indica semplicemente la direzione corretta.

1. Se si esegue la distribuzione da GitHub per la prima volta, fare clic su **autorizza** e seguire le istruzioni di autorizzazione. Se si desidera eseguire la distribuzione da un repository di un altro utente, fare clic su **Cambia account**.

1. Dopo aver autorizzato l'account Azure con GitHub, selezionare l' **organizzazione**, il **repository** e il **ramo** per configurare ci/CD per.

1. Quando GitHub actions è il provider di compilazione scelto, è possibile selezionare il file del flusso di lavoro desiderato con gli elenchi a discesa **dello stack** e della **versione** di Runtime. Azure esegue il commit di questo file del flusso di lavoro nel repository GitHub selezionato per gestire le attività di compilazione e distribuzione. Per visualizzare il file prima di salvare le modifiche, fare clic su **file di anteprima**.

    > [!NOTE]
    > Il servizio app rileva l' [impostazione dello stack della lingua](configure-common.md#configure-language-stack-settings) dell'app e seleziona il modello di flusso di lavoro più appropriato. Se si sceglie un modello diverso, è possibile che venga distribuita un'app che non viene eseguita correttamente. Per altre informazioni, vedere funzionamento [del provider di compilazione azioni di GitHub](#how-the-github-actions-build-provider-works).

1. Fare clic su **Salva**.
   
    I nuovi commit nel repository e nel ramo selezionati vengono distribuiti in modo continuo nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella scheda **logs** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

L'integrazione di BitBucket usa i servizi di compilazione del servizio app (kudu) per l'automazione della compilazione.

4. Se si esegue la distribuzione da BitBucket per la prima volta, fare clic su **autorizza** e seguire le istruzioni di autorizzazione. Se si desidera eseguire la distribuzione da un repository di un altro utente, fare clic su **Cambia account**.

1. Per bitbucket selezionare il **Team**, il **repository** e il **ramo** di bitbucket che si desidera distribuire in modo continuo.

1. Fare clic su **Salva**.
   
    I nuovi commit nel repository e nel ramo selezionati vengono distribuiti in modo continuo nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella scheda **logs** .
   
# <a name="local-git"></a>[GIT locale](#tab/local)

Vedere [distribuzione git locale nel servizio app Azure](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos come origine della distribuzione è il supporto per le app di Windows.
>

4. Il servizio di compilazione del servizio app (kudu) è il provider di compilazione predefinito.

    > [!NOTE]
    > Per usare Azure Pipelines come provider di compilazione per l'app del servizio app, non configurarlo nel servizio app. In alternativa, configurare CI/CD direttamente da Azure Pipelines. L'opzione **Azure Pipelines** indica semplicemente la direzione corretta.

1. Selezionare l'organizzazione, il **progetto**, il **repository** e il **ramo** di **Azure DevOps** che si vuole distribuire in modo continuo. 

    Se l'organizzazione DevOps non è elencata, non è ancora collegata alla sottoscrizione di Azure. Per altre informazioni, vedere [creare una connessione al servizio di Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

1. Nel menu a sinistra fare clic su impostazioni **centro distribuzione**  >    >  **Disconnetti**. 

    ![Mostra come disconnettere la sincronizzazione delle cartelle Cloud con l'app del servizio app nel portale di Azure.](media/app-service-continuous-deployment/disable.png)

1. Per impostazione predefinita, il file del flusso di lavoro azioni di GitHub viene mantenuto nel repository, ma continuerà ad attivare la distribuzione nell'app. Per eliminarlo dal repository, selezionare **Elimina file del flusso di lavoro**.

1. Fare clic su **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Funzionamento del provider di compilazione azioni di GitHub

Il provider di compilazione azioni di GitHub è un'opzione per [ci/CD da GitHub](#configure-deployment-source)ed esegue le operazioni seguenti per configurare ci/CD:

- Deposita un file del flusso di lavoro di azioni GitHub nel repository GitHub per gestire le attività di compilazione e distribuzione nel servizio app.
- Aggiunge il profilo di pubblicazione per l'app come segreto GitHub. Il file del flusso di lavoro usa questo segreto per l'autenticazione con il servizio app.
- Acquisisce le informazioni dai [log di esecuzione del flusso di lavoro](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) e le Visualizza nella scheda **log** del **centro distribuzione** dell'app.

È possibile personalizzare il provider di compilazione azioni di GitHub nei modi seguenti:

- Personalizzare il file del flusso di lavoro dopo che è stato generato nel repository GitHub. Per altre informazioni, vedere [sintassi del flusso di lavoro per le azioni di GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). È sufficiente assicurarsi che il flusso di lavoro venga distribuito nel servizio app con l'azione [Azure/webapps-Distribuisci](https://github.com/Azure/webapps-deploy) .
- Se il ramo selezionato è protetto, è ancora possibile visualizzare l'anteprima del file del flusso di lavoro senza salvare la configurazione, quindi aggiungerla manualmente nel repository. Questo metodo non fornisce l'integrazione dei log con la portale di Azure.
- Anziché un profilo di pubblicazione, eseguire la distribuzione usando un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Eseguire l'autenticazione con un'entità servizio

1. Generare un'entità servizio con il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](/cli/azure/). Nell'esempio seguente sostituire *\<subscription-id>* , *\<group-name>* e *\<app-name>* con i propri valori:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Per la sicurezza, concedere l'accesso minimo necessario all'entità servizio. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.
    
1. Salvare l'intero output JSON per il passaggio successivo, incluso il primo livello `{}` .

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**.

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto un nome come `AZURE_CREDENTIALS` .

1. Nel file del flusso di lavoro generato dal **centro distribuzione**, rivedere il `azure/webapps-deploy` passaggio con codice come l'esempio seguente (modificato da un file di flusso di lavoro Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Distribuisci da altri repository

Per le app di Windows, è possibile configurare manualmente la distribuzione continua da un repository git cloud o Mercurial che il portale non supporta direttamente, ad esempio [GitLab](https://gitlab.com/). A tale scopo, scegliere git esterno nell'elenco a discesa **origine** . Per ulteriori informazioni, vedere la pagina relativa alla [configurazione della distribuzione continua mediante passaggi manuali](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Altre risorse

* [Distribuire da Azure Pipelines a servizi app Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Esaminare i problemi comuni con la distribuzione continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usare Azure PowerShell](/powershell/azure/)
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)
