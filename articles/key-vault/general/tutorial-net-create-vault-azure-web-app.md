---
title: Esercitazione - Usare Azure Key Vault con un'app Web di Azure in .NET
description: In questa esercitazione verrà configurata un'app Web di Azure in un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788738"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Esercitazione: Usare un'identità gestita per connettere Key Vault a un'app Web di Azure in .NET

[Azure Key Vault](./overview.md) consente di archiviare credenziali e altri segreti con un maggiore livello di sicurezza. Per recuperare questi elementi, è tuttavia necessario eseguire l'autenticazione del codice con Key Vault. Le [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md) consentono di risolvere il problema assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza visualizzare le credenziali nel codice.

In questa esercitazione si crea e si distribuisce un'applicazione Web di Azure nel [servizio app di Azure](../../app-service/overview.md). Si userà un'identità gestita per autenticare l'app Web di Azure con un insieme di credenziali delle chiavi di Azure usando la [libreria client di segreti di Azure Key Vault per .NET](/dotnet/api/overview/azure/key-vault) e l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli). Valgono gli stessi principi di base se si usa il linguaggio di programmazione preferito, Azure PowerShell e/o il portale di Azure.

Per altre informazioni sulle applicazioni Web del servizio app di Azure e sulla distribuzione presentate in questa esercitazione, vedere:
- [Panoramica del Servizio app](../../app-service/overview.md)
- [Creare un'app Web ASP.NET Core nel servizio app di Azure](../../app-service/quickstart-dotnetcore.md)
- [Distribuzione dell'archivio Git locale nel servizio app di Azure](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. [Crearne una gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK (o versione successiva)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Un'installazione di [Git](https://www.git-scm.com/downloads).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault](./overview.md). È possibile creare un insieme di credenziali delle chiavi usando il [portale di Azure](quick-create-portal.md), l'[interfaccia della riga di comando di Azure](quick-create-cli.md) o [Azure PowerShell](quick-create-powershell.md).
* Un [segreto](../secrets/about-secrets.md) di Key Vault. È possibile creare un segreto tramite il [portale di Azure](../secrets/quick-create-portal.md), [PowerShell](../secrets/quick-create-powershell.md) o l'[interfaccia della riga di comando di Azure](../secrets/quick-create-cli.md).

Se l'applicazione Web è già stata distribuita nel servizio app di Azure, è possibile passare direttamente alle sezioni [Configurare l'accesso dell'app Web all'insieme di credenziali delle chiavi](#create-and-assign-a-managed-identity) e [Modificare il codice dell'applicazione Web](#modify-the-app-to-access-your-key-vault).

## <a name="create-a-net-core-app"></a>Creare un'app .NET Core
In questo passaggio si configurerà il progetto .NET Core locale.

In una finestra del terminale nel computer creare una directory denominata `akvwebapp` e impostarla come directory corrente:

```bash
mkdir akvwebapp
cd akvwebapp
```

Creare un'app .NET Core con il comando [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Eseguire l'applicazione in locale in modo da vedere l'aspetto che avrà dopo la distribuzione in Azure:

```bash
dotnet run
```

In un Web browser passare all'app all'indirizzo `http://localhost:5000`.

Verrà visualizzato il messaggio "Hello World!" Messaggio dell'app di esempio visualizzato nella pagina.

Per altre informazioni sulla creazione di applicazioni Web per Azure, vedere [Creare un'app Web ASP.NET Core nel servizio app di Azure](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio l'applicazione .NET Core viene distribuita nel servizio app di Azure tramite Git locale. Per altre informazioni su come creare e distribuire applicazioni, vedere [Creare un'app Web ASP.NET Core in Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Configurare la distribuzione con Git locale

Nella finestra del terminale premere **CTRL+C** per chiudere il server Web.  Inizializzare un repository Git per il progetto .NET Core:

```bash
git init
git add .
git commit -m "first commit"
```

È possibile usare FTP e l'istanza Git locale per distribuire un'app Web di Azure tramite un *utente della distribuzione*. Dopo averlo configurato, l'utente della distribuzione può essere usato per tutte le distribuzioni di Azure. Il nome utente e la password della distribuzione a livello di account sono diversi dalle credenziali della sottoscrizione di Azure. 

Per configurare l'utente della distribuzione, eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set). Scegliere un nome utente e una password che rispettino le linee guida seguenti: 

- Il nome utente deve essere univoco in Azure. Per i push nell'istanza Git locale, non può contenere il simbolo di chiocciola (@). 
- La password deve essere composta da almeno otto caratteri e contenere due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, cambiare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

Registrare il nome utente e la password in modo da poterle usare per distribuire le app Web.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui si distribuiscono e si gestiscono le risorse di Azure. Creare un gruppo di risorse in cui inserire sia l'insieme di credenziali delle chiavi che l'app Web usando il comando [az group create](/cli/azure/group?#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un [piano di servizio app](../../app-service/overview-hosting-plans.md) usando il comando [az appservice plan create](/cli/azure/appservice/plan) dell'interfaccia della riga di comando di Azure. L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario `FREE`:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Una volta creato il piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle riportate di seguito:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Per altre informazioni, vedere [Gestire un piano di servizio app in Azure](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Creare un'app Web

Creare un'[app Web di Azure](../../app-service/overview.md) nel piano di servizio app `myAppServicePlan`. 

> [!Important]
> Analogamente a un insieme di credenziali delle chiavi, un'app Web di Azure deve avere un nome univoco. Negli esempi seguenti sostituire `<your-webapp-name>` con il nome dell'app Web.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile a quello riportato di seguito:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


L'URL dell'istanza Git remota è visualizzato nella proprietà `deploymentLocalGitUrl`, nel formato `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Salvare questo URL. Sarà necessario più avanti.

Passare alla nuova app usando il comando seguente. Sostituire `<your-webapp-name>` con il nome dell'app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Verrà visualizzata la pagina Web predefinita di una nuova app Web di Azure.

### <a name="deploy-your-local-app"></a>Distribuire l'app locale

Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Nel comando seguente sostituire `<deploymentLocalGitUrl-from-create-step>` con l'URL dell'istanza Git remota salvato nella sezione [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Usare il comando seguente per eseguire il push all'istanza remota di Azure per distribuire l'app. Quando Git Credential Manager richiede le credenziali, usare quelle create nella sezione [Configurare la distribuzione con Git locale](#configure-the-local-git-deployment).

```bash
git push azure main
```

L'esecuzione del comando può impiegare alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili a quelle riportate di seguito:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Passare all'applicazione distribuita (o aggiornare la pagina) usando il Web browser:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Verrà visualizzato il messaggio "Hello World!" visualizzato in precedenza quando si è visitata la pagina `http://localhost:5000`.

Per altre informazioni sulla distribuzione di applicazioni Web tramite Git, vedere [Distribuzione dell'archivio Git locale nel servizio app di Azure](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Configurare l'app Web per la connessione a Key Vault

In questa sezione si configurerà l'accesso Web a Key Vault e si aggiornerà il codice dell'applicazione per recuperare un segreto da Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Creare e assegnare un'identità gestita

In questa esercitazione si userà l'[identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) per eseguire l'autenticazione con Key Vault. L'identità gestita gestisce automaticamente le credenziali delle applicazioni.

Nell'interfaccia della riga di comando di Azure eseguire il comando [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) per creare l'identità per l'applicazione:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Il comando restituirà il frammento JSON seguente:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Per concedere all'app Web l'autorizzazione per eseguire operazioni **get** e **list** sull'insieme di credenziali delle chiavi, passare `principalId` al comando [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

È anche possibile assegnare criteri di accesso usando il [portale di Azure](./assign-access-policy-portal.md) o [PowerShell](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Modificare l'app per l'accesso all'insieme di credenziali delle chiavi

In questa esercitazione si userà la [libreria client dei segreti di Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.secrets-readme) a scopo dimostrativo. Si può usare anche la [libreria client dei certificati di Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.certificates-readme) o la [libreria client delle chiavi di Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Installare i pacchetti

Nella finestra del terminale installare la libreria client dei segreti di Azure Key Vault per .NET e i pacchetti della libreria client delle identità di Azure:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aggiornare il codice

Trovare e aprire il file Startup.cs nel progetto akvwebapp. 

Aggiungere queste righe all'intestazione:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Aggiungere le righe seguenti prima della chiamata di `app.UseEndpoints`, aggiornando l'URI in modo da riflettere il valore di `vaultUri` dell'insieme di credenziali delle chiavi. Questo codice usa [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) per eseguire l'autenticazione con Key Vault, che a sua volta usa un token dell'identità gestita per l'autenticazione. Per altre informazioni sull'autenticazione con Key Vault, vedere la [guida per sviluppatori](./developers-guide.md#authenticate-to-key-vault-in-code). Il codice usa anche il backoff esponenziale per la ripetizione dei tentativi in caso di limitazione di Key Vault. Per altre informazioni sui limiti di transazioni di Key Vault, vedere [Informazioni sui limiti di Azure Key Vault](./overview-throttling.md).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Aggiornare la riga `await context.Response.WriteAsync("Hello World!");` in modo che sia simile a questa:

```csharp
await context.Response.WriteAsync(secretValue);
```

Assicurarsi di salvare le modifiche prima di continuare con il passaggio successivo.

#### <a name="redeploy-your-web-app"></a>Ridistribuire l'app Web

Ora che il codice è stato aggiornato, è possibile ridistribuirlo in Azure tramite i comandi Git seguenti:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Passare all'app Web completata

```bash
http://<your-webapp-name>.azurewebsites.net
```

Dove prima veniva visualizzato "Hello World", verrà ora visualizzato il valore del segreto.

## <a name="next-steps"></a>Passaggi successivi

- [Usare Azure Key Vault con le applicazioni distribuite in una macchina virtuale in .NET](./tutorial-net-virtual-machine.md)
- Vedere altre informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Visualizzare la [Guida per sviluppatori](./developers-guide.md)
- [Proteggere l'accesso a un insieme di credenziali delle chiavi](./secure-your-key-vault.md)