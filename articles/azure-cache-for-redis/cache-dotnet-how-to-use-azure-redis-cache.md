---
title: 'Avvio rapido: Usare la cache di Azure per Redis in .NET Framework'
description: Questa guida di avvio rapido illustra come accedere a Cache Redis di Azure dalle app .NET
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: effab14316c4a959f22467b9cc50984b6571da55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872156"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Avvio rapido: Usare la cache di Azure per Redis in .NET Framework

In questa guida di avvio rapido si incorpora Azure Cache for Redis in un'app .NET Framework per accedere a una cache sicura e dedicata accessibile da qualsiasi applicazione in Azure. Si usa specificamente il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) con codice C# in un'app console .NET.

## <a name="skip-to-the-code-on-github"></a>Passare al codice in GitHub

Se si vuole passare direttamente al codice, vedere la guida .NET Framework [guida introduttiva](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) su GitHub.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 o versione successiva](https://dotnet.microsoft.com/download/dotnet-framework), richiesto dal client StackExchange.Redis.

## <a name="create-a-cache"></a>Creare una cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Creare un file nel computer denominato *CacheSecrets.config* e inserirlo in una posizione in cui non verrà archiviato con il codice sorgente dell'applicazione di esempio. Per questo avvio rapido, il file *CacheSecrets.config* si trova nel percorso *C:\AppSecrets\CacheSecrets.config*.

Modificare il file *CacheSecrets.config* e aggiungere il contenuto seguente:

```xml
<appSettings>
    <add key="CacheConnection" value="<host-name>,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

Sostituire `<host-name>` con il nome host della cache.

Sostituire `<access-key>` con la chiave primaria per la cache.


## <a name="create-a-console-app"></a>Creare un'app console

In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.

Selezionare **App console (.NET Framework)** e quindi **Avanti** per configurare l'app. Digitare un **Nome progetto**, verificare **che .NET Framework 4.6.1** o  versione successiva sia selezionato e quindi fare clic su Crea per creare una nuova applicazione console.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Configurare il client della cache

In questa sezione, si configurerà l'applicazione console per usare il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) per .NET.

In Visual Studio fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** ed eseguire il comando seguente dalla finestra Console di Gestione pacchetti.

```powershell
Install-Package StackExchange.Redis
```

Una volta completata l'installazione, il client della cache *StackExchange.Redis* è disponibile per l'uso con il progetto.


## <a name="connect-to-the-cache"></a>Connettersi alla cache

In Visual Studio aprire il file *App.config* e aggiornarlo includendo un attributo `appSettings` `file` che fa riferimento al file *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**. Aggiungere un riferimento all'assembly **System.Configuration**.

Aggiungere le istruzioni `using` seguenti a *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

La connessione a Cache Redis di Azure è gestita dalla classe `ConnectionMultiplexer`. Questa classe deve essere condivisa e riusata in tutta l'applicazione client. Non creare una nuova connessione per ogni operazione. 

Non archiviare mai le credenziali nel codice sorgente. Per semplificare questo esempio, viene usato solo un file di configurazione dei segreti esterno. Un approccio migliore potrebbe consistere nell'usare [Azure Key Vault con certificati](/rest/api/keyvault/certificate-scenarios).

In *Program.cs* aggiungere i membri seguenti alla classe `Program` dell'applicazione console:

```csharp
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
```


Questo approccio per la condivisione di un'istanza di `ConnectionMultiplexer` nell'applicazione usa una proprietà statica che restituisce un'istanza connessa. Il codice offre un modo thread-safe per inizializzare solo una singola istanza di `ConnectionMultiplexer` connessa. La proprietà `abortConnect` è impostata su false, a indicare che la chiamata riuscirà anche se non viene stabilita una connessione a Cache Redis di Azure. Una delle funzionalità principali di `ConnectionMultiplexer` è il ripristino automatico della connettività alla cache non appena l'errore di rete o eventuali altri problemi vengono risolti.

Il valore dell'appSetting *CacheConnection* viene usato per fare riferimento alla stringa di connessione della cache dal portale di Azure come parametro password.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Gestire RedisConnectionException e SocketException riconnettendo

Una procedura consigliata quando si chiamano i metodi su consiste nel tentare di risolvere automaticamente le eccezioni e chiudendo e ristabilindo `ConnectionMultiplexer` `RedisConnectionException` la `SocketException` connessione.

Aggiungere le istruzioni `using` seguenti a *Program.cs*:

```csharp
using System.Net.Sockets;
using System.Threading;
```

In *Program.cs* aggiungere i membri seguenti alla `Program` classe :

```csharp
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

## <a name="executing-cache-commands"></a>Esecuzione dei comandi di cache

Aggiungere il codice seguente per la routine `Main` della classe `Program` per l'applicazione console:

```csharp
static void Main(string[] args)
{
    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Cache Redis di Azure ha un numero configurabile di database (16 per impostazione predefinita) che è possibile usare per separare in modo logico i dati all'interno di un'istanza di Cache Redis di Azure. Il codice si connette al database predefinito, DB 0. Per altre informazioni, vedere [Informazioni sui database Redis](cache-development-faq.md#what-are-redis-databases) e [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration).

Per archiviare e recuperare gli elementi nella cache, è possibile usare i metodi `StringSet` e `StringGet`.

Redis archivia la maggior parte dei dati come stringhe Redis, tuttavia queste stringhe possono contenere molti tipi di dati, inclusi dati binari serializzati, utilizzabili durante la memorizzazione di oggetti .NET nella cache.

Premere **CTRL+F5** per compilare ed eseguire l'app console.

Nell'esempio seguente è possibile notare che la chiave `Message` in precedenza aveva un valore nella cache che era stato impostato usando la Console Redis nel portale di Azure. L'app ha aggiornato questo valore memorizzato nella cache. L'app ha anche eseguito i comandi `PING` e `CLIENT LIST`.

![App console parziale](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Gestire gli oggetti .NET nella cache

Cache Redis di Azure è in grado di memorizzare nella cache sia oggetti .NET che tipi di dati primitivi, ma prima della memorizzazione nella cache un oggetto .NET deve essere serializzato. La serializzazione di un oggetto .NET spetta allo sviluppatore dell'applicazione, che può scegliere liberamente il serializzatore da usare.

Un modo semplice per serializzare gli oggetti prevede l'uso dei metodi di serializzazione `JsonConvert` in [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) e la serializzazione a e da JSON. In questa sezione si aggiungerà un oggetto .NET alla cache.

In Visual Studio fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** ed eseguire il comando seguente dalla finestra Console di Gestione pacchetti.

```powershell
Install-Package Newtonsoft.Json
```

Aggiungere l'istruzione `using` seguente all'inizio del file *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Aggiungere la definizione di classe `Employee` seguente a *Program.cs*:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Alla fine della routine `Main()` in *Program.cs* e prima della chiamata a `CloseConnection()`, aggiungere le righe di codice seguenti alla cache e recuperare un oggetto .NET serializzato:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Premere **CTRL+F5** per compilare ed eseguire l'app console per testare la serializzazione degli oggetti .NET. 

![App console completata](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo avvio rapido e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
>

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

![Delete](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.



<a name="next-steps"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è visto come usare Cache Redis di Azure da un'applicazione .NET. Continuare con la guida di avvio rapido successiva per usare Cache Redis di Azure con un'app Web ASP.NET.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
