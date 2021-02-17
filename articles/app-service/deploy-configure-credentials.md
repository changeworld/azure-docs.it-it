---
title: Configurare le credenziali di distribuzione
description: Informazioni sui tipi di credenziali di distribuzione presenti nel servizio app di Azure e su come configurarle e usarle.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 2a53ecb1b3411561da50f7dbf3be79f9d70b42bc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560417"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Per proteggere la distribuzione di app da un computer locale, [app Azure servizio](./overview.md) supporta due tipi di credenziali per la distribuzione [git locale](deploy-local-git.md) e la [distribuzione FTP/S](deploy-ftp.md). Queste credenziali sono diverse dalle credenziali della sottoscrizione di Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Configurare le credenziali dell'ambito utente

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire il comando [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "\@". 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Non è possibile configurare le credenziali dell'ambito utente con Azure PowerShell. Usare un metodo diverso oppure provare a [usare le credenziali dell'ambito dell'applicazione](#appscope). 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

È possibile configurare le credenziali dell'ambito utente nella [pagina delle risorse](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)dell'app. Indipendentemente dall'App configurata, le credenziali vengono applicate a tutte le app per tutte le sottoscrizioni dell'account Azure. 

Nel [portale di Azure](https://portal.azure.com)è necessario disporre di almeno un'app prima di poter accedere alla pagina delle credenziali di distribuzione. Per configurare le credenziali dell'ambito utente:

1. Dal menu a sinistra dell'app selezionare > le credenziali FTPS del **centro distribuzione**  >   o le **credenziali git/FTPS locali**.

    ![Mostra come è possibile selezionare il dashboard FTP dal centro distribuzione in app Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Scorrere verso il basso fino a **ambito utente**, configurare il **nome utente** e la **password** e quindi selezionare **Salva**.

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella pagina **Panoramica** dell'app,

![Mostra come trovare il nome utente della distribuzione git nella pagina Panoramica dell'app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se è configurata la distribuzione Git, nella pagina viene visualizzato un **nome utente Git/distribuzione**; in caso contrario, un **nome utente FTP/distribuzione**.

> [!NOTE]
> Azure non Mostra la password di distribuzione dell'ambito utente. Se si dimentica la password, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Usare le credenziali dell'ambito utente con FTP/FTPS

Per l'autenticazione a un endpoint FTP/FTPS usando le credenziali dell'ambito utente è necessario un nome utente nel formato seguente: `<app-name>\<user-name>`

Poiché le credenziali dell'ambito utente sono collegate all'utente e non a una risorsa specifica, il nome utente deve essere in questo formato per indirizzare l'azione di accesso all'endpoint dell'app corretto.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Ottenere le credenziali dell'ambito dell'applicazione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Ottenere le credenziali dell'ambito dell'applicazione usando il comando [AZ webapp Deployment list-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Ad esempio:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Per la [distribuzione git locale](deploy-local-git.md)è anche possibile usare il comando [AZ webapp Deployment list-Publishing-Credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) per ottenere un URI remoto Git per l'app, con le credenziali dell'ambito dell'applicazione già incorporate. Ad esempio:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ottenere le credenziali dell'ambito dell'applicazione usando il comando [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Ad esempio:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dal menu a sinistra dell'app selezionare le credenziali FTPS del **centro distribuzione**  >   o le **credenziali git/FTPS locali**.

    ![Mostra come è possibile selezionare il dashboard FTP dal centro distribuzione in app Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Nella sezione **ambito applicazione** selezionare il collegamento **copia** per copiare il nome utente o la password.

-----

## <a name="reset-application-scope-credentials"></a>Reimposta le credenziali dell'ambito dell'applicazione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Reimpostare le credenziali dell'ambito dell'applicazione usando il comando [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Reimpostare le credenziali dell'ambito dell'applicazione usando il comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dal menu a sinistra dell'app selezionare le credenziali FTPS del **centro distribuzione**  >   o le **credenziali git/FTPS locali**.

    ![Mostra come è possibile selezionare il dashboard FTP dal centro distribuzione in app Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Nella sezione **ambito applicazione** selezionare **Reimposta**.

-----

## <a name="disable-basic-authentication"></a>Disabilitare l'autenticazione di base

Alcune organizzazioni devono soddisfare i requisiti di sicurezza e piuttosto disabilitare l'accesso tramite FTP o WebDeploy. In questo modo, i membri dell'organizzazione possono accedere ai propri servizi app solo tramite le API controllate da Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Per disabilitare l'accesso FTP al sito, eseguire il comando dell'interfaccia della riga di comando seguente. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che l'accesso FTP sia bloccato, è possibile provare a eseguire l'autenticazione usando un client FTP come FileZilla. Per recuperare le credenziali di pubblicazione, passare al pannello panoramica del sito e fare clic su Scarica profilo di pubblicazione. Usare il nome host FTP del file, il nome utente e la password per l'autenticazione e verrà restituita una risposta di errore 401, che indica che l'utente non è autorizzato.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Per disabilitare l'accesso con autenticazione di base alla porta WebDeploy e al sito SCM, eseguire il comando dell'interfaccia della riga di comando seguente. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che le credenziali del profilo di pubblicazione siano bloccate su WebDeploy, provare a [pubblicare un'app Web con Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Disabilitare l'accesso all'API

L'API nella sezione precedente è supportata dal controllo degli accessi in base al ruolo di Azure (RBAC di Azure), il che significa che è possibile [creare un ruolo personalizzato](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) e assegnare utenti priveldged inferiori al ruolo, in modo che non possano abilitare l'autenticazione di base in tutti i siti. Per configurare il ruolo personalizzato, [seguire queste istruzioni](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

È anche possibile usare [monitoraggio di Azure](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) per controllare le richieste di autenticazione riuscite e usare i [criteri di Azure](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) per applicare questa configurazione per tutti i siti nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
