---
title: Guida ai processi .NET isolati per .NET 5,0 in funzioni di Azure
description: Informazioni su come usare un processo .NET isolato per eseguire le funzioni C# in .NET 5,0 out-of-process in Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: c6ab0748a310a970ad845bc27d915c7fa767b0c5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631694"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guida per l'esecuzione di funzioni in .NET 5,0 in Azure

Questo articolo è un'introduzione all'uso di C# per sviluppare funzioni di processo isolate .NET, che eseguono out-of-process in funzioni di Azure. L'esecuzione out-of-process consente di separare il codice di funzione dal runtime di funzioni di Azure. Fornisce inoltre un modo per creare ed eseguire funzioni destinate alla versione corrente di .NET 5,0. 

| Guida introduttiva | Concetti| Esempi |
|--|--|--| 
| <ul><li>[Uso di Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Uso degli strumenti da riga di comando](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Con Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opzioni di hosting](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[Esempi di riferimento](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Se non è necessario supportare .NET 5,0 o eseguire le funzioni out-of-process, potrebbe essere necessario [sviluppare funzioni della libreria di classi C#](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Perché il processo isolato .NET?

In precedenza funzioni di Azure ha supportato solo una modalità strettamente integrata per le funzioni .NET, che vengono eseguite [come libreria di classi](functions-dotnet-class-library.md) nello stesso processo dell'host. Questa modalità offre una profonda integrazione tra il processo host e le funzioni. Ad esempio, le funzioni della libreria di classi .NET possono condividere API e tipi di associazione. Tuttavia, questa integrazione richiede anche un accoppiamento più stretto tra il processo host e la funzione .NET. Ad esempio, le funzioni .NET in esecuzione in-process sono necessarie per l'esecuzione nella stessa versione di .NET del runtime di funzioni. Per consentire l'esecuzione al di fuori di questi vincoli, è ora possibile scegliere di eseguire in un processo isolato. Questo isolamento dei processi consente anche di sviluppare funzioni che usano le versioni correnti di .NET, ad esempio .NET 5,0, non supportate in modo nativo dal runtime di funzioni.

Poiché queste funzioni vengono eseguite in un processo separato, esistono alcune [differenze tra funzionalità e funzionalità](#differences-with-net-class-library-functions) tra le app per le funzioni .NET isolate e le app per le funzioni della libreria di classi .NET.

### <a name="benefits-of-running-out-of-process"></a>Vantaggi dell'esecuzione out-of-process

Quando si esegue out-of-process, le funzioni .NET possono sfruttare i vantaggi seguenti: 

+ Minor numero di conflitti: poiché le funzioni vengono eseguite in un processo separato, gli assembly usati nell'app non sono in conflitto con la versione diversa degli stessi assembly usati dal processo host.  
+ Controllo completo del processo: è possibile controllare l'avvio dell'app e controllare le configurazioni usate e il middleware avviato.
+ Inserimento delle dipendenze: poiché si ha il controllo completo del processo, è possibile usare i comportamenti .NET correnti per l'inserimento di dipendenze e incorporare middleware nell'app per le funzioni. 

## <a name="supported-versions"></a>Versioni supportate

L'unica versione di .NET attualmente supportata per l'esecuzione out-of-process è .NET 5,0.

## <a name="net-isolated-project"></a>Progetto .NET isolato

Un progetto di funzione con isolamento .NET è fondamentalmente un progetto di app console .NET destinato a .NET 5,0. Di seguito sono riportati i file di base necessari in qualsiasi progetto .NET isolato:

+ [host.jssu](functions-host-json.md) file.
+ [local.settings.jssu](functions-run-local.md#local-settings-file) file.
+ File di progetto C# (con estensione csproj) che definisce il progetto e le dipendenze.
+ File Program.cs che è il punto di ingresso per l'app.

## <a name="package-references"></a>Riferimenti ai pacchetti

Quando si esegue out-of-process, il progetto .NET usa un set univoco di pacchetti, che implementano sia le funzionalità di base che le estensioni di binding. 

### <a name="core-packages"></a>Pacchetti principali 

I pacchetti seguenti sono necessari per eseguire le funzioni .NET in un processo isolato:

+ [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. Functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pacchetti di estensione

Poiché le funzioni eseguite in un processo .NET isolato utilizzano tipi di binding diversi, richiedono un set univoco di pacchetti di estensioni di associazione. 

Questi pacchetti di estensione sono disponibili in [Microsoft. Azure. Functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Avvio e configurazione 

Quando si usano le funzioni di isolamento .NET, è possibile accedere all'avvio dell'app per le funzioni, che in genere si trova in Program.cs. Si è responsabili della creazione e dell'avvio dell'istanza host. Di conseguenza, è anche possibile accedere direttamente alla pipeline di configurazione per l'app. È possibile inserire in modo più semplice le dipendenze ed eseguire il middleware quando si esegue out-of-process. 

Il codice seguente illustra un esempio di `HostBuilder` pipeline:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

`HostBuilder`Viene usato per compilare e restituire un'istanza completamente inizializzata `IHost` , che viene eseguita in modo asincrono per avviare l'app per le funzioni. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configurazione

L'accesso alla pipeline del generatore host significa che è possibile impostare qualsiasi configurazione specifica dell'app durante l'inizializzazione. Queste configurazioni si applicano all'app per le funzioni in esecuzione in un processo separato. Per apportare modifiche all'host di funzioni o al trigger e alla configurazione dell'associazione, sarà comunque necessario utilizzare il [host.jssu file](functions-host-json.md).      

<!--The following example shows how to add configuration `args`, which are read as command-line arguments: 
 
:::code language="csharp" 
                .ConfigureAppConfiguration(c =>
                {
                    c.AddCommandLine(args);
                })
                :::

The `ConfigureAppConfiguration` method is used to configure the rest of the build process and application. This example also uses an [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), which makes it easier to add multiple configuration items. Because `ConfigureAppConfiguration` returns the same instance of [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true), you can also just call it multiple times to add multiple configuration items.-->  
È possibile accedere al set completo di configurazioni sia da [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) che da [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Per ulteriori informazioni sulla configurazione, vedere la pagina relativa alla [configurazione in ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

### <a name="dependency-injection"></a>Inserimento di dipendenze

L'inserimento di dipendenze è semplificato rispetto alle librerie di classi .NET. Anziché dover creare una classe di avvio per registrare i servizi, è sufficiente chiamare `ConfigureServices` sul generatore host e usare i metodi di estensione su [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) per inserire servizi specifici. 

Nell'esempio seguente viene inserita una dipendenza del servizio singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Per altre informazioni, vedere [inserimento delle dipendenze in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

<!--### Middleware

.NET isolated also supports middleware registration, again by using a model similar to what exists in ASP.NET. This model gives you the ability to inject logic into the invocation pipeline, and before and after functions execute.

While the full middleware registration set of APIs is not yet exposed, we do support middleware registration and have added an example to the sample application under the Middleware folder.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::-->

## <a name="execution-context"></a>Contesto di esecuzione

.NET isolated passa un `FunctionContext` oggetto ai metodi della funzione. Questo oggetto consente di ottenere un' [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) istanza di per scrivere nei log chiamando il `GetLogger` metodo e fornendo una `categoryName` stringa. Per altre informazioni, vedere [registrazione](#logging). 

## <a name="bindings"></a>Associazioni 

Le associazioni vengono definite utilizzando attributi su metodi, parametri e tipi restituiti. Un metodo di funzione è un metodo con un oggetto `Function` e un attributo trigger applicato a un parametro di input, come illustrato nell'esempio seguente:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

L'attributo trigger specifica il tipo di trigger e associa i dati di input a un parametro del metodo. La funzione di esempio precedente viene attivata da un messaggio della coda e il messaggio della coda viene passato al metodo nel `myQueueItem` parametro.

L'attributo `Function` indica il metodo come punto di ingresso della funzione. Il nome deve essere univoco all'interno di un progetto, iniziare con una lettera e contenere solo lettere, numeri, `_` e `-` , fino a 127 caratteri di lunghezza. I modelli di progetto spesso creano un metodo denominato `Run`, ma il nome del metodo può essere qualsiasi nome di metodo c# valido.

Poiché i progetti isolati .NET vengono eseguiti in un processo di lavoro separato, le associazioni non possono sfruttare le classi di associazione avanzate, ad esempio `ICollector<T>` , `IAsyncCollector<T>` e `CloudBlockBlob` . Non è inoltre disponibile il supporto diretto per i tipi ereditati dagli SDK del servizio sottostanti, ad esempio [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) e [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Al contrario, le associazioni si basano su stringhe, matrici e tipi serializzabili, ad esempio oggetti POCO (Plain Old Class Objects). 

Per i trigger HTTP, è necessario usare `HttpRequestData` e `HttpResponseData` per accedere ai dati di richiesta e risposta. Ciò è dovuto al fatto che non è possibile accedere agli oggetti richiesta e risposta HTTP originali durante l'esecuzione out-of-process. 

### <a name="input-bindings"></a>Associazioni di input

Una funzione può avere zero o più associazioni di input che possono passare dati a una funzione. Analogamente ai trigger, le associazioni di input vengono definite applicando un attributo di associazione a un parametro di input. Quando la funzione viene eseguita, il runtime tenta di recuperare i dati specificati nell'associazione. I dati richiesti dipendono spesso dalle informazioni fornite dal trigger mediante parametri di associazione.  

### <a name="output-bindings"></a>Associazioni di output

Per scrivere in un'associazione di output, è necessario applicare un attributo di associazione di output al metodo Function, che definisce la modalità di scrittura nel servizio associato. Il valore restituito dal metodo viene scritto nell'associazione di output. Nell'esempio seguente, ad esempio, viene scritto un valore stringa in una coda `functiontesting2` di messaggi denominata utilizzando un'associazione di output:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Più associazioni di output

I dati scritti in un'associazione di output sono sempre il valore restituito della funzione. Se è necessario scrivere in più di un'associazione di output, è necessario creare un tipo restituito personalizzato. Questo tipo restituito deve avere l'attributo di associazione di output applicato a una o più proprietà della classe. Nell'esempio seguente viene scritta sia una risposta HTTP che un'associazione di output della coda:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>Trigger HTTP

Il trigger HTTP converte il messaggio di richiesta HTTP in ingresso in un `HttpRequestData` oggetto passato alla funzione. Questo oggetto fornisce i dati della richiesta, tra cui,,, `Headers` `Cookies` `Identities` `URL` e un messaggio facoltativo `Body` . Questo oggetto è una rappresentazione dell'oggetto richiesta HTTP e non della richiesta. 

Analogamente, la funzione restituisce un `HttpReponseData` oggetto che fornisce i dati utilizzati per creare la risposta http, incluso il messaggio `StatusCode` , `Headers` , e facoltativamente un messaggio `Body` .  

Il codice seguente è un trigger HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Registrazione

In .NET isolated è possibile scrivere nei log usando un' [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) istanza ottenuta da un `FunctionContext` oggetto passato alla funzione. Chiamare il `GetLogger` metodo, passando un valore stringa che rappresenta il nome della categoria in cui vengono scritti i log. La categoria è in genere il nome della funzione specifica da cui vengono scritti i log. Per ulteriori informazioni sulle categorie, vedere l' [articolo monitoraggio](functions-monitoring.md#log-levels-and-categories). 

Nell'esempio seguente viene illustrato come ottenere `ILogger` e scrivere i log all'interno di una funzione:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Usare diversi metodi di `ILogger` per scrivere diversi livelli di log, ad esempio `LogWarning` o `LogError` . Per altre informazioni sui livelli di registrazione, vedere l' [articolo monitoraggio](functions-monitoring.md#log-levels-and-categories).

[`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true)Viene anche fornito un oggetto quando si usa l' [inserimento delle dipendenze](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Differenze con le funzioni della libreria di classi .NET

Questa sezione descrive lo stato corrente delle differenze funzionali e comportamentali eseguite in .NET 5,0 out-of-process rispetto alle funzioni della libreria di classi .NET in esecuzione in-process:

| Funzionalità/comportamento |  In-process (.NET Core 3,1) | Out-of-process (.NET 5,0) |
| ---- | ---- | ---- |
| Versioni di .NET | LTS (.NET Core 3,1) | Corrente (.NET 5,0) |
| Pacchetti principali | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Binding di pacchetti di estensione | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | In [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Registrazione | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) passato alla funzione | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) ottenuta da `FunctionContext` |
| Token di annullamento | [Supportato](functions-dotnet-class-library.md#cancellation-tokens) | Non supportato |
| Associazioni di output | Parametri out | Valori restituiti |
| Tipi di associazioni di output |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)e altri tipi specifici del client | Tipi semplici, tipi serializzabili JSON e matrici. |
| Più associazioni di output | Supportato | [Supportato](#multiple-output-bindings) |
| Trigger HTTP | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Funzioni permanenti | [Supportato](durable/durable-functions-overview.md) | Non supportato | 
| Associazioni imperative | [Supportato](functions-dotnet-class-library.md#binding-at-runtime) | Non supportato |
| function.jssull'artefatto | Generato | Non generato |
| Configurazione | [host.json](functions-host-json.md) | [host.jssu](functions-host-json.md) e l'inizializzazione personalizzata |
| Inserimento di dipendenze | [Supportato](functions-dotnet-dependency-injection.md)  | [Supportato](#dependency-injection) |
| Middleware | Non supportato | Supportato |
| Ore di inizio a freddo | Typical (Tipica) | Più a lungo, a causa dell'avvio JIT. Eseguire in Linux anziché Windows per ridurre i potenziali ritardi. |
| ReadyToRun | [Supportato](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Problemi noti

Per informazioni sulle soluzioni alternative per conoscere i problemi relativi all'esecuzione di funzioni di processo con isolamento .NET, vedere [la pagina relativa ai problemi noti](https://aka.ms/AAbh18e). Per segnalare problemi, [creare un problema in questo repository GitHub](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>Passaggi successivi

+ [Altre informazioni sui trigger e le associazioni](functions-triggers-bindings.md)
+ [Altre informazioni sulle procedure consigliate per Funzioni di Azure](functions-best-practices.md)
