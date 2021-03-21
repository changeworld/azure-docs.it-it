---
title: Creare un'app Web ASP.NET con la cache di Azure per Redis
description: In questa guida introduttiva viene illustrato come creare un'app Web ASP.NET con Cache Redis di Azure
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 19c54ad62e45ecf6e31b46d0291f61dca8e8d9b3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722464"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Guida introduttiva: usare cache di Azure per Redis con un'app Web ASP.NET 

In questa guida di avvio rapido si usa Visual Studio 2019 per creare un'applicazione Web ASP.NET che si connette ad Azure Cache for Redis per archiviare e recuperare i dati dalla cache. Si distribuisce quindi l'app nel Servizio app di Azure.

## <a name="skip-to-the-code-on-github"></a>Passa al codice su GitHub

Se si vuole passare direttamente al codice, vedere la [Guida introduttiva di ASP.NET](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) su GitHub.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) con i carichi di lavoro **Sviluppo ASP.NET e Web** e **Sviluppo di Azure**.

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aprire Visual Studio e selezionare **file**  >  **nuovo**  >  **progetto**.

2. Nella finestra di dialogo **Crea un nuovo progetto** seguire questa procedura:

    ![Crea progetto](./media/cache-web-app-howto/cache-create-project.png)

    a. Nella casella di ricerca immettere _C# ASP.NET Web Application_.

    b. Selezionare **ASP.NET Web Application (.NET Framework)** (Applicazione Web ASP.NET - .NET Framework).

    c. Selezionare **Avanti**.

3. Nella casella **nome progetto** assegnare un nome al progetto. Per questo esempio è stato usato il nome **ContosoTeamStats**.

4. Verificare che sia selezionato **.NET Framework 4.6.1** o versione successiva.

5. Selezionare **Crea**.
   
6. Selezionare **MVC** come tipo di progetto.

7. Assicurarsi che per l'impostazione **Autenticazione** sia selezionata l'opzione **Nessuna autenticazione**. A seconda della versione di Visual Studio, il valore predefinito dell'impostazione **Autenticazione** può essere diverso. Per modificarlo, selezionare **Modifica autenticazione** e quindi **Nessuna autenticazione**.

8. Selezionare **Crea** per creare il progetto.

## <a name="create-a-cache"></a>Creare una cache

A questo punto creare la cache per l'app.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>Per modificare il file *CacheSecrets.config*

1. Creare un file nel computer denominato *CacheSecrets.config*. Inserirla in una posizione in cui non verrà archiviata con il codice sorgente dell'applicazione di esempio. Per questa guida introduttiva, il file *CacheSecrets.config* si trova in *C:\AppSecrets\CacheSecrets.config*.

1. Modificare il file *CacheSecrets.config*. Aggiungere quindi il contenuto seguente:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. Sostituire `<cache-name>` con il nome host della cache.

1. Sostituire `<access-key>` con la chiave primaria per la cache.

    > [!TIP]
    > È possibile usare la chiave di accesso secondaria durante la rotazione delle chiavi come chiave alternativa mentre si rigenera la chiave di accesso primaria.
   >
1. Salvare il file.

## <a name="update-the-mvc-application"></a>Aggiornare l'applicazione MVC

In questa sezione si aggiorna l'applicazione per supportare una nuova visualizzazione con un semplice test su Cache Redis di Azure.

* [Aggiornare il file web.config con un'impostazione di app per la cache](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* Configurare l'applicazione per l'uso del client StackExchange.Redis
* Aggiornare HomeController e Layout
* Aggiungere una nuova visualizzazione di RedisCache

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Aggiornare il file web.config con un'impostazione di app per la cache

Quando si esegue l'applicazione in locale, queste informazioni in *CacheSecrets.config* vengono usate per connettersi all'istanza di Cache Redis di Azure. In un secondo momento si distribuirà l'applicazione in Azure. A quel punto si configurerà un'impostazione dell'app in Azure usata dall'applicazione per recuperare le informazioni di connessione alla cache al posto di questo file. 

Poiché il file *CacheSecrets.config* non viene distribuito in Azure con l'applicazione, lo si usa solo quando si testa l'applicazione in locale. Conservare queste informazioni nel modo più sicuro possibile per impedire l'accesso ai dati della cache da parte di utenti malintenzionati.

#### <a name="to-update-the-webconfig-file"></a>Per aggiornare il file *web.config*
1. In **Esplora soluzioni** aprire il file *web.config* facendo doppio clic su di esso.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. Nel file *web.config* individuare l'elemento `<appSetting>`. Aggiungere quindi l'attributo `file` seguente. Se è stato usato un nome o un percorso di file diverso, sostituire i valori dell'esempio con questi valori.

* Prima: `<appSettings>`
* Dopo: `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

Il runtime ASP.NET unisce il contenuto del file esterno con il markup nell'elemento `<appSettings>` . Il runtime ignora l'attributo file, se non è possibile trovare il file specificato. I segreti, ovvero la stringa di connessione alla cache, non sono inclusi come parte del codice sorgente per l'applicazione. Quando si distribuisce l'app Web in Azure, il file *CacheSecrets.config* non viene distribuito.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Per configurare l'applicazione per l'uso di StackExchange.Redis

1. Per configurare l'app per usare il pacchetto NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) per Visual Studio, selezionare **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**.

2. Eseguire questo comando nella finestra `Package Manager Console`:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis. Se si preferisce usare una versione con nome sicuro della libreria client `StackExchange.Redis`, installare il pacchetto `StackExchange.Redis.StrongName`.

### <a name="to-update-the-homecontroller-and-layout"></a>Per aggiornare la classe HomeController e il layout

1. In **Esplora soluzioni** espandere la cartella **Controllers** e aprire il file *HomeController.cs*.

2. Aggiungere le seguenti `using` istruzioni all'inizio del file.

    ```csharp
    using StackExchange.Redis;
    using System.Configuration;
    using System.Net.Sockets;
    using System.Text;
    using System.Threading;
    ```

3. Aggiungere i membri seguenti alla `HomeController` classe per supportare una nuova `RedisCache` azione che esegue alcuni comandi sulla nuova cache.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

        IDatabase cache = GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        // Note that this requires allowAdmin=true in the connection string
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();
        var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
        IServer server = GetServer(endpoint.Host, endpoint.Port);
        ClientInfo[] clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (ClientInfo client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }

    private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
    private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
    private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

    private static readonly object reconnectLock = new object();

    // In general, let StackExchange.Redis handle most reconnects,
    // so limit the frequency of how often ForceReconnect() will
    // actually reconnect.
    public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

    // If errors continue for longer than the below threshold, then the
    // multiplexer seems to not be reconnecting, so ForceReconnect() will
    // re-create the multiplexer.
    public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

    public static int RetryMaxAttempts => 5;

    private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

    private static Lazy<ConnectionMultiplexer> CreateConnection()
    {
        return new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    }

    private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
    {
        if (oldConnection == null)
            return;

        try
        {
            oldConnection.Value.Close();
        }
        catch (Exception)
        {
            // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
        }
    }

    /// <summary>
    /// Force a new ConnectionMultiplexer to be created.
    /// NOTES:
    ///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
    ///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
    ///     3. Call this method every time you see a connection exception. The code will:
    ///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
    ///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
    /// </summary>
    public static void ForceReconnect()
    {
        var utcNow = DateTimeOffset.UtcNow;
        long previousTicks = Interlocked.Read(ref lastReconnectTicks);
        var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
        TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return;

        lock (reconnectLock)
        {
            utcNow = DateTimeOffset.UtcNow;
            elapsedSinceLastReconnect = utcNow - previousReconnectTime;

            if (firstErrorTime == DateTimeOffset.MinValue)
            {
                // We haven't seen an error since last reconnect, so set initial values.
                firstErrorTime = utcNow;
                previousErrorTime = utcNow;
                return;
            }

            if (elapsedSinceLastReconnect < ReconnectMinFrequency)
                return; // Some other thread made it through the check and the lock, so nothing to do.

            TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
            TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

            bool shouldReconnect =
                elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
                && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

            // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
            previousErrorTime = utcNow;

            if (!shouldReconnect)
                return;

            firstErrorTime = DateTimeOffset.MinValue;
            previousErrorTime = DateTimeOffset.MinValue;

            Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
            CloseConnection(oldConnection);
            lazyConnection = CreateConnection();
            Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
        }
    }

    // In real applications, consider using a framework such as
    // Polly to make it easier to customize the retry approach.
    private static T BasicRetry<T>(Func<T> func)
    {
        int reconnectRetry = 0;
        int disposedRetry = 0;

        while (true)
        {
            try
            {
                return func();
            }
            catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
            {
                reconnectRetry++;
                if (reconnectRetry > RetryMaxAttempts)
                    throw;
                ForceReconnect();
            }
            catch (ObjectDisposedException)
            {
                disposedRetry++;
                if (disposedRetry > RetryMaxAttempts)
                    throw;
            }
        }
    }

    public static IDatabase GetDatabase()
    {
        return BasicRetry(() => Connection.GetDatabase());
    }

    public static System.Net.EndPoint[] GetEndPoints()
    {
        return BasicRetry(() => Connection.GetEndPoints());
    }

    public static IServer GetServer(string host, int port)
    {
        return BasicRetry(() => Connection.GetServer(host, port));
    }
    ```

4. In **Esplora soluzioni** espandere la cartella **Views** > **Shared**. Aprire quindi il file *_Layout.cshtml*.

    Sostituire:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    con:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Per aggiungere una nuova visualizzazione RedisCache

1. In **Esplora soluzioni** espandere la cartella **Views** e fare clic con il pulsante destro del mouse sula cartella **Home**. Scegliere **Aggiungi**  >  **visualizzazione...**.

2. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **RedisCache** per Nome visualizzazione. Quindi selezionare **Aggiungi**.

3. Sostituire il codice nel file *RedisCache.cshtml* con il codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Per impostazione predefinita, il progetto è configurato per ospitare l'app localmente in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) per il test e il debug.

### <a name="to-run-the-app-locally"></a>Per eseguire l'app in locale
1. In Visual Studio selezionare **debug**  >  **Avvia debug** per compilare e avviare l'app localmente per il test e il debug.

2. Nel browser selezionare **Azure Cache for Redis Test** (Test di Cache Redis di Azure) sulla barra di spostamento.

3. Nell'esempio seguente la chiave `Message` in precedenza aveva un valore memorizzato nella cache, impostato usando la console di Cache Redis di Azure nel portale. L'app ha aggiornato questo valore memorizzato nella cache. L'app ha anche eseguito i comandi `PING` e `CLIENT LIST`.

    ![Testo semplice completato in locale](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Pubblicare ed eseguire l'app in Azure

Dopo avere testato correttamente l'app in locale, è possibile distribuirla in Azure ed eseguirla nel cloud.

### <a name="to-publish-the-app-to-azure"></a>Per pubblicare l'app in Azure

1. In Visual Studio fare clic con il pulsante destro del mouse sul nodo di progetto in Esplora soluzioni. Quindi selezionare **pubblica**.

    ![Pubblica](./media/cache-web-app-howto/cache-publish-app.png)

2. Selezionare **Servizio app di Microsoft Azure**, quindi **Crea nuovo** e infine **Pubblica**.

    ![Eseguire la pubblicazione nel servizio app](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. Nella finestra di dialogo **Crea servizio app** apportare le modifiche seguenti:

    | Impostazione | Valore consigliato | Descrizione |
    | ------- | :---------------: | ----------- |
    | **Nome app** | Usare quello predefinito. | Il nome dell'app è il nome host per l'app quando viene distribuita in Azure. Se necessario, per rendere il nome univoco è possibile aggiungere un suffisso timestamp. |
    | **Sottoscrizione** | Scegliere la sottoscrizione di Azure. | Eventuali costi di hosting correlati vengono addebitati in questa sottoscrizione. Se si hanno più sottoscrizioni di Azure, verificare che sia selezionata la sottoscrizione desiderata.|
    | **Gruppo di risorse** | Usare lo stesso gruppo di risorse in cui è stata creata la cache, ad esempio *TestResourceGroup*. | Il gruppo di risorse consente di gestire tutte le risorse come gruppo. Successivamente, quando si vuole eliminare l'app, è sufficiente eliminare il gruppo. |
    | **Piano di servizio app** | Selezionare **Nuovo** e quindi creare un nuovo piano di servizio app denominato *TestingPlan*. <br />Usare lo stesso **percorso** usato durante la creazione della cache. <br />Scegliere il livello **Gratuito** per le dimensioni. | Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app Web. |

    ![Finestra di dialogo del servizio app](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Dopo aver configurato le impostazioni di hosting del servizio app, selezionare **Crea**.

5. Monitorare la finestra **Output** in Visual Studio per esaminare lo stato della pubblicazione. Dopo che l'app è stata pubblicata, viene registrato il relativo URL:

    ![Output della pubblicazione](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Aggiungere l'impostazione dell'app per la cache

Dopo che la nuova app è stata pubblicata, aggiungere una nuova impostazione dell'app. Questa impostazione viene usata per archiviare le informazioni di connessione alla cache. 

#### <a name="to-add-the-app-setting"></a>Per aggiungere l'impostazione dell'app 

1. Digitare il nome dell'app nella barra di ricerca nella parte superiore del portale di Azure per trovare l'app appena creata.

    ![Trovare l'app](./media/cache-web-app-howto/cache-find-app-service.png)

2. Aggiungere una nuova impostazione dell'app denominata **CacheConnection** per l'app da usare per connettersi alla cache. Usare lo stesso valore configurato per `CacheConnection` nel file *CacheSecrets.config*. Il valore contiene la chiave di accesso e il nome host della cache.

    ![Aggiungere l'impostazione dell'app](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Eseguire l'app in Azure

Nel browser passare all'URL per l'app. L'URL viene visualizzato nei risultati dell'operazione di pubblicazione nella finestra dell'output di Visual Studio. È disponibile anche nel portale di Azure nella pagina di panoramica dell'app creata.

Selezionare **Azure Cache for Redis Test** (Test di Cache Redis di Azure) sulla barra di spostamento per testare l'accesso alla cache.

![Test semplice completato in Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prosegue con l'esercitazione successiva, è possibile conservare le risorse create in questa guida introduttiva e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio della guida introduttiva, è possibile eliminare le risorse di Azure create in questa guida introduttiva per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Quando si elimina un gruppo di risorse, tutte le risorse in esso contenute vengono eliminate in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.

### <a name="to-delete-a-resource-group"></a>Per eliminare un gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella **Filtra per nome** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse, nell'elenco dei risultati, selezionare **...** e quindi **Elimina gruppo di risorse**.

    ![Elimina](./media/cache-web-app-howto/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Digitare il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**.

Dopo qualche istante, il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva si userà Cache Redis di Azure in uno scenario più realistico per migliorare le prestazioni di un'app. L'applicazione viene aggiornata per memorizzare nella cache i risultati del tabellone punteggi usando il modello cache-aside con ASP.NET e un database.

> [!div class="nextstepaction"]
> [Creare un tabellone punteggi cache-aside in ASP.NET](cache-web-app-cache-aside-leaderboard.md)