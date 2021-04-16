---
title: Configurare le credenziali di distribuzione
description: Informazioni sui tipi di credenziali di distribuzione presenti nel servizio app di Azure e su come configurarle e usarle.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ec48ec32250e271eff9e40535689f83dd9d3b60c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483634"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Per proteggere la distribuzione di app da un computer locale, [Servizio app di Azure](./overview.md) supporta due tipi di credenziali per la distribuzione [Git](deploy-local-git.md) locale [e la distribuzione FTP/S.](deploy-ftp.md) Queste credenziali sono diverse dalle credenziali della sottoscrizione di Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> La **pagina Centro sviluppo (versione classica)** nel portale di Azure, ovvero l'esperienza di distribuzione precedente, verrà deprecata a marzo 2021. Questa modifica non influirà sulle impostazioni di distribuzione esistenti nell'app ed è possibile continuare a gestire la distribuzione dell'app nella **pagina Centro** distribuzione.

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Configurare le credenziali dell'ambito utente

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Eseguire il [comando az webapp deployment user set.](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo â€ ̃@â€™. 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Non è possibile configurare le credenziali dell'ambito utente con Azure PowerShell. Usare un metodo diverso o prendere in [considerazione l'uso di credenziali dell'ambito dell'applicazione](#appscope). 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

È possibile configurare le credenziali dell'ambito utente nella pagina delle risorse [di qualsiasi app.](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) Indipendentemente dall'app in cui si configurano queste credenziali, si applica a tutte le app per tutte le sottoscrizioni nell'account Azure. 

Nel [portale di Azure](https://portal.azure.com)è necessario avere almeno un'app prima di poter accedere alla pagina delle credenziali di distribuzione. Per configurare le credenziali dell'ambito utente:

1. Nel menu a sinistra dell'app selezionare > credenziali FTPS del Centro distribuzione o Credenziali  >   **Git/FTPS locali**.

    ![Illustra come selezionare il dashboard FTP dal Centro distribuzione in App Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Scorrere verso il basso **fino a Ambito utente,** configurare il nome **utente** e **la password** e quindi selezionare **Salva**.

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella pagina **Panoramica** dell'app,

![Illustra come trovare il nome utente della distribuzione Git nella pagina Panoramica dell'app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se è configurata la distribuzione Git, nella pagina viene visualizzato un **nome utente Git/distribuzione**; in caso contrario, un **nome utente FTP/distribuzione**.

> [!NOTE]
> Azure non visualizza la password di distribuzione dell'ambito utente. Se si dimentica la password, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Usare le credenziali dell'ambito utente con FTP/FTPS

L'autenticazione a un endpoint FTP/FTPS usando le credenziali dell'ambito utente richiede un nome utente nel formato seguente: `<app-name>\<user-name>`

Poiché le credenziali dell'ambito utente sono collegate all'utente e non a una risorsa specifica, il nome utente deve essere in questo formato per indirizzare l'azione di accesso all'endpoint dell'app corretto.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Ottenere le credenziali dell'ambito dell'applicazione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Ottenere le credenziali dell'ambito dell'applicazione usando [il comando az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Ad esempio:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Per la [distribuzione Git](deploy-local-git.md)locale, è anche possibile usare il [comando az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) per ottenere un URI remoto Git per l'app, con le credenziali dell'ambito dell'applicazione già incorporate. Ad esempio:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ottenere le credenziali dell'ambito dell'applicazione usando [il comando Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Ad esempio:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Nel menu a sinistra dell'app selezionare Credenziali FTPS del **Centro**  >  **distribuzione** o **Credenziali Git/FTPS locali.**

    ![Illustra come selezionare il dashboard FTP dal Centro distribuzione in App Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Nella sezione **Ambito applicazione** selezionare il **collegamento** Copia per copiare il nome utente o la password.

-----

## <a name="reset-application-scope-credentials"></a>Reimpostare le credenziali dell'ambito dell'applicazione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Reimpostare le credenziali dell'ambito dell'applicazione usando [il comando az resource invoke-action:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Reimpostare le credenziali dell'ambito dell'applicazione usando il [comando Invoke-AzResourceAction:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Nel menu a sinistra dell'app selezionare Credenziali FTPS del **Centro**  >  **distribuzione** o **Credenziali Git/FTPS locali.**

    ![Illustra come selezionare il dashboard FTP dal Centro distribuzione in App Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Nella sezione **Ambito applicazione** selezionare **Reimposta**.

-----

## <a name="disable-basic-authentication"></a>Disabilitare l'autenticazione di base

Alcune organizzazioni devono soddisfare i requisiti di sicurezza e preferirebbe disabilitare l'accesso tramite FTP o WebDeploy. In questo modo, i membri dell'organizzazione possono accedere ai servizi app solo tramite API controllate da Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Per disabilitare l'accesso FTP al sito, eseguire il comando dell'interfaccia della riga di comando seguente. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che l'accesso FTP sia bloccato, è possibile provare a eseguire l'autenticazione usando un client FTP, ad esempio FileZilla. Per recuperare le credenziali di pubblicazione, passare al pannello panoramica del sito e fare clic su Scarica profilo di pubblicazione. Usare ™ il nome host FTP, il nome utente e la password FTP del file per l'autenticazione e si otterrà una risposta di errore 401, che indica che non si è autorizzati.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Per disabilitare l'accesso di base dell'autenticazione alla porta WebDeploy e al sito SCM, eseguire il comando seguente dell'interfaccia della riga di comando. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che le credenziali del profilo di pubblicazione siano bloccate in WebDeploy, provare a pubblicare un'app Web usando Visual Studio [2019.](/visualstudio/deployment/quickstart-deploy-to-azure)

### <a name="disable-access-to-the-api"></a>Disabilitare l'accesso all'API

L'API nella sezione precedente è supportata dal controllo degli accessi [](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) in base al ruolo di Azure, ovvero è possibile creare un ruolo personalizzato e assegnare utenti con livello inferiore al ruolo in modo che non possano abilitare l'autenticazione di base in qualsiasi sito. Per configurare il ruolo personalizzato, [seguire queste istruzioni.](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)

È anche possibile usare [Monitoraggio di Azure](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) per controllare eventuali [](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) richieste di autenticazione riuscite e usare Criteri di Azure per applicare questa configurazione per tutti i siti nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
