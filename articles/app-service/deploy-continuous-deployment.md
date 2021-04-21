---
title: Configurare la distribuzione continua
description: Informazioni su come abilitare CI/CD per Servizio app di Azure da GitHub, BitBucket, Azure Repos o altri repository. Selezionare la pipeline di compilazione più adatta alle proprie esigenze.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789484"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure

[Servizio app di Azure](overview.md) la distribuzione continua da [GitHub,](https://help.github.com/articles/create-a-repo) [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) [e Azure Repos](/azure/devops/repos/git/creatingrepo) repository tramite il pull degli aggiornamenti più recenti.

> [!NOTE]
> La **pagina Centro sviluppo (versione classica)** nel portale di Azure, ovvero l'esperienza di distribuzione precedente, verrà deprecata a marzo 2021. Questa modifica non influirà sulle impostazioni di distribuzione esistenti nell'app ed è possibile continuare a gestire la distribuzione dell'app nella **pagina Centro** distribuzione.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Configurare l'origine della distribuzione

1. Nel [portale di Azure](https://portal.azure.com)passare alla pagina di gestione per l'app del servizio app.

1. Scegliere Impostazioni centro distribuzione dal menu  >  **a sinistra.** 

1. In **Origine** selezionare una delle opzioni CI/CD.

    ![Illustra come scegliere l'origine di distribuzione nel Centro distribuzione per Servizio app di Azure](media/app-service-continuous-deployment/choose-source.png)

Scegliere la scheda corrispondente alla selezione per i passaggi.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) è il provider di compilazione predefinito. Per modificarlo, fare clic **su Change provider** App Service Build  >  **Service** (Kudu) > **OK**.

    > [!NOTE]
    > Per usare Azure Pipelines come provider di compilazione per l'app del servizio app, non configurarlo nel servizio app. Configurare invece CI/CD direttamente da Azure Pipelines. **L Azure Pipelines'opzione** indica semplicemente la direzione giusta.

1. Se si esegue la distribuzione da GitHub per la prima volta, fare clic su **Autorizza** e seguire le richieste di autorizzazione. Se si vuole eseguire la distribuzione da un repository di un altro utente, fare clic **su Cambia account**.

1. Dopo aver autorizzato l'account Azure con GitHub, selezionare **l'organizzazione,** il **repository** e **il ramo** per cui configurare CI/CD.

1. Quando GitHub Actions è il provider di compilazione scelto, è possibile selezionare il file del flusso di lavoro desiderato con gli elenchi a discesa **Stack di runtime** **e** Versione. Azure esegue il commit di questo file del flusso di lavoro nel repository GitHub selezionato per gestire le attività di compilazione e distribuzione. Per visualizzare il file prima di salvare le modifiche, fare clic su **File di anteprima**.

    > [!NOTE]
    > Il servizio app rileva [l'impostazione dello stack di lingue](configure-common.md#configure-language-stack-settings) dell'app e seleziona il modello di flusso di lavoro più appropriato. Se si sceglie un modello diverso, è possibile che venga distribuita un'app che non viene eseguita correttamente. Per altre informazioni, vedere [Funzionamento del provider GitHub Actions compilazione.](#how-the-github-actions-build-provider-works)

1. Fare clic su **Salva**.
   
    I nuovi commit nel repository e nel ramo selezionati vengono distribuiti in modo continuo nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella **scheda** Log.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

L'integrazione di BitBucket usa Servizi di compilazione del servizio app (Kudu) per l'automazione della compilazione.

4. Se si esegue la distribuzione da BitBucket per la prima volta, fare clic su **Autorizza** e seguire le istruzioni di autorizzazione. Se si vuole eseguire la distribuzione da un repository di un altro utente, fare clic **su Cambia account.**

1. Per Bitbucket selezionare il team di Bitbucket, il **repository** e **il ramo** da distribuire in modo continuo.

1. Fare clic su **Salva**.
   
    I nuovi commit nel repository e nel ramo selezionati vengono distribuiti in modo continuo nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella **scheda** Log.
   
# <a name="local-git"></a>[GIT locale](#tab/local)

Vedere [Distribuzione Git locale in Servizio app di Azure](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos come origine della distribuzione è il supporto per le app di Windows.
>

4. Il servizio di compilazione del servizio app (Kudu) è il provider di compilazione predefinito.

    > [!NOTE]
    > Per usare Azure Pipelines come provider di compilazione per l'app del servizio app, non configurarlo nel servizio app. Configurare invece CI/CD direttamente da Azure Pipelines. **L Azure Pipelines'opzione** punta semplicemente nella direzione giusta.

1. Selezionare **l Azure DevOps,** **il progetto,** il  **repository** e il ramo da distribuire in modo continuo. 

    Se l'organizzazione DevOps non è elencata, non è ancora collegata alla sottoscrizione di Azure. Per altre informazioni, vedere [Creare una connessione al servizio di Azure.](/azure/devops/pipelines/library/connect-to-azure)

-----

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

1. Nel [portale di Azure](https://portal.azure.com)passare alla pagina di gestione per l'app del servizio app.

1. Dal menu a sinistra fare clic su **Impostazioni centro distribuzione**  >    >  **Disconnetti**. 

    ![Illustra come disconnettere la sincronizzazione della cartella cloud con l'app del servizio app nel portale di Azure.](media/app-service-continuous-deployment/disable.png)

1. Per impostazione predefinita, il GitHub Actions del flusso di lavoro viene mantenuto nel repository, ma continuerà a attivare la distribuzione nell'app. Per eliminarlo dal repository, selezionare **Elimina file del flusso di lavoro**.

1. Fare clic su **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Funzionamento del provider GitHub Actions compilazione

Il GitHub Actions di compilazione è un'opzione per [CI/CD di GitHub](#configure-deployment-source)ed esegue le operazioni seguenti per configurare CI/CD:

- Deposita un file GitHub Actions flusso di lavoro nel repository GitHub per gestire le attività di compilazione e distribuzione nel servizio app.
- Aggiunge il profilo di pubblicazione per l'app come segreto GitHub. Il file del flusso di lavoro usa questo segreto per l'autenticazione con il servizio app.
- Acquisisce informazioni dai log di [esecuzione del flusso](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) di lavoro e le visualizza nella scheda **Log** nel Centro distribuzione **dell'app.**

È possibile personalizzare il GitHub Actions di compilazione nei modi seguenti:

- Personalizzare il file del flusso di lavoro dopo che è stato generato nel repository GitHub. Per altre informazioni, vedere Sintassi [del flusso di lavoro per GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Assicurarsi che il flusso di lavoro sia distribuito nel servizio app con [l'azione azure/webapps-deploy.](https://github.com/Azure/webapps-deploy)
- Se il ramo selezionato è protetto, è comunque possibile visualizzare in anteprima il file del flusso di lavoro senza salvare la configurazione, quindi aggiungerlo manualmente nel repository. Questo metodo non consente l'integrazione del log con l'portale di Azure.
- Anziché un profilo di pubblicazione, eseguire la distribuzione usando [un'entità](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) servizio in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Eseguire l'autenticazione con un'entità servizio

Questa configurazione facoltativa sostituisce l'autenticazione predefinita con i profili di pubblicazione nel file del flusso di lavoro generato.

1. Generare un'entità servizio con [il comando az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) nell'interfaccia della riga [di comando di Azure.](/cli/azure/) Nell'esempio seguente sostituire *\<subscription-id>* , e con valori *\<group-name>* *\<app-name>* personalizzati:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Per motivi di sicurezza, concedere l'accesso minimo richiesto all'entità servizio. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.
    
1. Salvare l'intero output JSON per il passaggio successivo, incluso il primo `{}` livello.

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto.**

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto un nome come `AZURE_CREDENTIALS` .

1. Nel file del flusso di lavoro generato dal Centro distribuzione rivedere il passaggio con codice simile all'esempio seguente (modificato da un file Node.js `azure/webapps-deploy` flusso di lavoro):

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
    
## <a name="deploy-from-other-repositories"></a>Distribuire da altri repository

Per le app di Windows, è possibile configurare manualmente la distribuzione continua da un repository Git o Mercurial cloud che il portale non supporta direttamente, ad esempio [GitLab.](https://gitlab.com/) A tale scopo, scegliere Git esterno **nell'elenco a discesa Origine.** Per altre informazioni, vedere [Configurare la distribuzione continua usando i passaggi manuali.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>Altre risorse

* [Distribuire da Azure Pipelines a app Azure Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Analizzare i problemi comuni relativi alla distribuzione continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usare Azure PowerShell](/powershell/azure/)
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)
