---
title: Usare le identità gestite per accedere a Configurazione app
titleSuffix: Azure App Configuration
description: Eseguire l'autenticazione Configurazione app di Azure con identità gestite
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877535"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Usare le identità gestite per accedere a Configurazione app

Azure Active Directory identità [gestite semplificano la](../active-directory/managed-identities-azure-resources/overview.md) gestione dei segreti per l'applicazione cloud. Con un'identità gestita, il codice può usare l'entità servizio creata per il servizio di Azure in cui viene eseguito. È possibile usare un'identità gestita invece di usare credenziali distinte archiviate in Azure Key Vault o una stringa di connessione locale.

Configurazione app di Azure e le librerie client .NET Core, .NET Framework e Java Spring sono integrate nel supporto delle identità gestite. Anche se non è necessario usarla, l'identità gestita elimina la necessità di un token di accesso che contiene segreti. Il codice può accedere all'archivio di Configurazione app usando solo l'endpoint di servizio. È possibile incorporare questo URL direttamente nel codice senza esporre alcun segreto.

Questo articolo illustra come sfruttare l'identità gestita per accedere a Configurazione app. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare,  [creare un'app ASP.NET Core con Configurazione](./quickstart-aspnet-core-app.md) app.

> [!NOTE]
> Questo articolo usa Servizio app di Azure esempio, ma lo stesso concetto si applica a qualsiasi altro servizio di Azure che supporta l'identità gestita, ad esempio [servizio Azure Kubernetes](../aks/use-azure-ad-pod-identity.md), Macchina virtuale di [Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)e [Istanze di Azure Container](../container-instances/container-instances-managed-identity.md). Se il carico di lavoro è ospitato in uno di questi servizi, è anche possibile sfruttare il supporto delle identità gestite del servizio.

Questo articolo illustra anche come usare l'identità gestita in combinazione con i riferimenti Key Vault configurazione app. Con una singola identità gestita, è possibile accedere facilmente sia ai segreti Key Vault che ai valori di configurazione da Configurazione app. Se si vuole esplorare questa funzionalità, completare prima Use Key Vault References with ASP.NET Core (Usa Key Vault [riferimenti con ASP.NET Core).](./use-key-vault-references-dotnet-core.md)

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Concedere a un'identità gestita l'accesso a Configurazione app.
> * Configurare l'app in modo da usare un'identità gestita per connettersi a Configurazione app.
> * Facoltativamente, configurare l'app per l'uso di un'identità gestita quando ci si connette a Key Vault tramite un riferimento Key Vault configurazione app.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari:

* [.NET Core SDK](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell configurato](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Aggiungere un'identità gestita

Per configurare un'identità gestita nel portale, creare prima un'applicazione e quindi abilitare la funzionalità.

1. Creare un'istanza di Servizi [app nel portale di Azure](https://portal.azure.com) come di consueto. Passare all'app nel portale.

1. Scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro a sinistra e selezionare **Identità**.

1. Nella scheda **Assegnata dal sistema** impostare **Stato** su **Attivato** e selezionare **Salva**.

1. Risposta **Sì quando** viene richiesto di abilitare l'identità gestita assegnata dal sistema.

    ![Impostare l'identità gestita nel servizio app](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Concedere l'accesso a Configurazione app

1. Nel [portale di Azure](https://portal.azure.com)selezionare Tutte **le risorse** e selezionare l'archivio di Configurazione app creato nell'avvio rapido.

1. Selezionare **Controllo di accesso (IAM)** .

1. Nella scheda **Verifica l'accesso** selezionare **Aggiungi** nell'interfaccia utente della scheda **Aggiungi un'assegnazione di ruolo**.

1. In **Ruolo selezionare** **Lettore dati configurazione app**. In **Assegna accesso a** selezionare **Servizio app** in **Identità gestita assegnata dal sistema**.

1. In **Sottoscrizione** selezionare la sottoscrizione di Azure. Selezionare la risorsa Servizio app per la propria app.

1. Selezionare **Salva**.

    ![Aggiungere un'identità gestita](./media/add-managed-identity.png)

1. Facoltativo: se si vuole concedere l'accesso anche Key Vault, seguire le istruzioni in Assegnare un criterio Key Vault [di accesso.](../key-vault/general/assign-access-policy-portal.md)

## <a name="use-a-managed-identity"></a>Usare un'identità gestita

1. Aggiungere un riferimento al *pacchetto Azure.Identity:*

    ```bash
    dotnet add package Azure.Identity
    ```

1. Trovare l'endpoint nell'archivio di Configurazione app. Questo URL è elencato nella scheda **Chiavi di** accesso per l'archivio nel portale di Azure.

1. Aprire *appsettings.json* e aggiungere lo script seguente. Sostituire *\<service_endpoint>* , incluse le parentesi quadre, con l'URL dell'archivio di Configurazione app.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Aprire *Program.cs* e aggiungere un riferimento agli spazi `Azure.Identity` dei nomi e `Microsoft.Azure.Services.AppAuthentication` :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Se si vuole accedere solo ai valori archiviati direttamente in Configurazione app, aggiornare il metodo sostituendo il metodo `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` (disponibile nel `Microsoft.Azure.AppConfiguration.AspNetCore` pacchetto).

    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > Nel caso in cui si voglia usare **un'identità** gestita assegnata dall'utente, assicurarsi di specificare clientId durante la creazione di [ManagedIdentityCredential.](/dotnet/api/azure.identity.managedidentitycredential)
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Come illustrato nelle domande frequenti sulle identità gestite per le risorse di [Azure,](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)esiste un modo predefinito per risolvere l'identità gestita usata. In questo caso, la libreria di identità di Azure impone di specificare l'identità desiderata per evitare problemi di runtime in futuro (ad esempio, se viene aggiunta una nuova identità gestita assegnata dall'utente o se l'identità gestita assegnata dal sistema è abilitata). Sarà quindi necessario specificare clientId anche se è definita una sola identità gestita assegnata dall'utente e non è presente alcuna identità gestita assegnata dal sistema.


1. Per usare sia i valori di Configurazione app che Key Vault riferimenti, aggiornare *Program.cs* come illustrato di seguito. Questo codice chiama come parte di per indicare al provider di configurazione quali credenziali usare durante `SetCredential` `ConfigureKeyVault` l'autenticazione Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    È ora possibile accedere ai Key Vault come qualsiasi altra chiave di configurazione dell'app. Il provider di configurazione userà `ManagedIdentityCredential` per eseguire l'autenticazione Key Vault e recuperare il valore.

    > [!NOTE]
    > funziona `ManagedIdentityCredential` solo in ambienti Azure di servizi che supportano l'autenticazione dell'identità gestita. Non funziona nell'ambiente locale. Usare per il funzionamento del codice sia negli ambienti locali che in quelli di Azure, perché verrà utilizzato il fall back ad alcune opzioni di autenticazione, [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) inclusa l'identità gestita.
    > 
    > Se si vuole usare **un'identità gestita assegnata dall'utente** con quando viene distribuita in `DefaultAzureCredential` Azure, specificare [clientId](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Eseguire la distribuzione dall'archivio Git locale

Il modo più semplice per abilitare la distribuzione Git locale per l'app con il server di compilazione Kudu è usare Azure Cloud Shell [.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Abilitare l'archivio Git locale con Kudu
Se non si ha un repository Git locale per l'app, è necessario inizializzarne uno. Per inizializzare un repository Git locale, eseguire i comandi seguenti dalla directory del progetto dell'app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Per abilitare la distribuzione Git locale per l'app con il server di compilazione Kudu, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Questo comando offre un output simile al seguente:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Distribuire il progetto

Nella finestra _del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _\<url>_ con l'URL del git remoto ottenuto da Enable local Git with Kudu (Abilita [Git locale con Kudu).](#enable-local-git-with-kudu)

```bash
git remote add azure <url>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene chiesta la password, immettere quella creata in [Configurare un utente della distribuzione](#configure-a-deployment-user). Non usare la stessa password usata per accedere al portale di Azure.

```bash
git push azure main
```

Nell'output potrebbe essere visualizzata l'automazione specifica di runtime, ad esempio MSBuild per ASP.NET, `npm install` per Node.js e `pip install` per Python.

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare all'app Web usando un browser per verificare che il contenuto sia stato distribuito.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Usare l'identità gestita in altri linguaggi

Anche i provider di Configurazione app per .NET Framework e Java Spring offrono supporto incorporato per l'identità gestita. Quando si configura uno di questi provider, è possibile usare l'endpoint dell'URL dell'archivio anziché la stringa di connessione completa.

Ad esempio, è possibile aggiornare l'app console .NET Framework creata nella guida introduttiva per specificare le impostazioni seguenti nel file *App.config:*

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata aggiunta un'identità gestita di Azure per semplificare l'accesso a Configurazione app e migliorare la gestione delle credenziali per l'app. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)