---
title: Esercitazione per l'uso della configurazione dinamica di Configurazione app di Azure in un'app di Funzioni di Azure | Microsoft Docs
description: Questa esercitazione illustra come aggiornare dinamicamente i dati di configurazione per le app di Funzioni di Azure
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963562"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Esercitazione: Usare la configurazione dinamica in un'app di Funzioni di Azure

Il provider di configurazione .NET di Configurazione app supporta la memorizzazione nella cache e l'aggiornamento della configurazione dinamicamente in base all'attività dell'applicazione. Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app di Funzioni di Azure presentata negli argomenti di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app di Funzioni di Azure con Configurazione app](./quickstart-azure-functions-csharp.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare l'app di Funzioni di Azure per aggiornarne la configurazione in risposta alle modifiche in un archivio di Configurazione app.
> * Inserire la configurazione più recente nelle chiamate di Funzioni di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro **Sviluppo di Azure**
- [Strumenti di Funzioni di Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Completare l'argomento di avvio rapido [Creare un'app di Funzioni di Azure con Configurazione app di Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app

1. Aprire *Startup.cs* e aggiornare il metodo `ConfigureAppConfiguration`. 

   Il metodo `ConfigureRefresh` registra un'impostazione da controllare per rilevare la presenza di modifiche ogni volta che viene attivato un aggiornamento all'interno dell'applicazione, operazione che verrà eseguita nel passaggio successivo con l'aggiunta di `_configurationRefresher.TryRefreshAsync()`. Il parametro `refreshAll` indica al provider di Configurazione app di ricaricare l'intera configurazione ogni volta che viene rilevata una modifica nell'impostazione registrata.

    Tutte le impostazioni registrate per l'aggiornamento hanno una scadenza della cache predefinita di 30 secondi. Questo valore può essere aggiornato chiamando il metodo `AzureAppConfigurationRefreshOptions.SetCacheExpiration`.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Quando si aggiornano più coppie chiave-valore in Configurazione app, in genere è preferibile evitare che l'applicazione ricarichi la configurazione prima che vengano apportate tutte le modifiche. È possibile registrare una chiave **sentinella** e aggiornarla solo quando vengono completate tutte le altre modifiche di configurazione. Ciò consente di garantire la coerenza della configurazione nell'applicazione.

2. Aggiornare il metodo `Configure` per rendere disponibili i servizi di Configurazione app di Azure tramite l'inserimento di dipendenze.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Aprire *Function1.cs* e aggiungere gli spazi dei nomi seguenti.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Aggiornare il costruttore per ottenere l'istanza di `IConfigurationRefresherProvider` tramite l'inserimento di dipendenze, da cui è possibile ottenere l'istanza di `IConfigurationRefresher`.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Aggiornare il metodo `Run` e segnalare di aggiornare la configurazione usando il metodo `TryRefreshAsync` all'inizio della chiamata di Funzioni. Questa operazione non verrà eseguita se non viene raggiunta la finestra temporale di scadenza della cache. Rimuovere l'operatore `await` se si preferisce che la configurazione venga aggiornata senza bloccare la chiamata a Funzioni corrente. In tal caso, le successive chiamate a Funzioni riceveranno il valore aggiornato.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testare la funzione in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Per testare la funzione premere F5. Se viene visualizzata, accettare la richiesta di Visual Studio di scaricare e installare gli strumenti dell'**interfaccia della riga di comando Azure Functions Core Tools**. Potrebbe essere necessario anche abilitare un'eccezione del firewall per consentire agli strumenti di gestire le richieste HTTP.

3. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Guida introduttiva: debug di funzioni in VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. L'immagine seguente mostra la risposta nel browser alla richiesta GET locale restituita dalla funzione.

    ![Guida introduttiva: avvio della funzione in locale](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'archivio di Configurazione app creato nella guida di avvio rapido.

6. Selezionare **Configuration Explorer** e aggiornare i valori della chiave seguente:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure - Aggiornati |

   Creare quindi la chiave sentinella o modificarne il valore se esiste già, ad esempio:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. Aggiornare alcune volte il browser. Quando l'impostazione memorizzata nella cache scade dopo 30 secondi, nella pagina viene visualizzata la risposta della chiamata a Funzioni con il valore aggiornato.

    ![Avvio rapido: aggiornamento della funzione in locale](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Il codice di esempio usato in questa esercitazione può essere scaricato dal [repository GitHub di Configurazione app](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app di Funzioni di Azure per aggiornare dinamicamente le impostazioni di configurazione di Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
