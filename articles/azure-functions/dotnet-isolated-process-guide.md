---
title: Guida al processo isolato .NET per .NET 5.0 in Funzioni di Azure
description: Informazioni su come usare un processo isolato .NET per eseguire le funzioni C# in .NET 5.0 out-of-process in Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 53f3c79886d26b20a584d747759176ea842741cf
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739279"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guida all'esecuzione di funzioni in .NET 5.0 in Azure

Questo articolo è un'introduzione all'uso di C# per sviluppare funzioni di processo isolate .NET, che vengono eseguite out-of-process in Funzioni di Azure. L'esecuzione out-of-process consente di separare il codice della funzione dal runtime Funzioni di Azure runtime. Offre anche un modo per creare ed eseguire funzioni che hanno come destinazione la versione corrente di .NET 5.0. 

| Guida introduttiva | Concetti| Esempi |
|--|--|--| 
| <ul><li>[Uso di Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Uso degli strumenti da riga di comando](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Con Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opzioni di hosting](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[Esempi di riferimento](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Se non è necessario supportare .NET 5.0 o eseguire le funzioni out-of-process, è possibile sviluppare funzioni della libreria di [classi C#.](functions-dotnet-class-library.md)

## <a name="why-net-isolated-process"></a>Perché il processo isolato .NET?

In Funzioni di Azure ha supportato solo una modalità strettamente integrata per [](functions-dotnet-class-library.md) le funzioni .NET, che vengono eseguite come libreria di classi nello stesso processo dell'host. Questa modalità offre una profonda integrazione tra il processo host e le funzioni. Ad esempio, le funzioni della libreria di classi .NET possono condividere API e tipi di associazione. Tuttavia, questa integrazione richiede anche un accoppiamento più stretto tra il processo host e la funzione .NET. Ad esempio, le funzioni .NET in esecuzione in-process devono essere eseguite nella stessa versione di .NET del runtime di Funzioni. Per consentire l'esecuzione al di fuori di questi vincoli, è ora possibile scegliere di eseguirla in un processo isolato. Questo isolamento del processo consente anche di sviluppare funzioni che usano le versioni correnti di .NET (ad esempio .NET 5.0), non supportate in modo nativo dal runtime di Funzioni.

Poiché queste funzioni vengono eseguite in [](#differences-with-net-class-library-functions) un processo separato, esistono alcune differenze di funzionalità e funzionalità tra le app per le funzioni isolate .NET e le app per le funzioni della libreria di classi .NET.

### <a name="benefits-of-running-out-of-process"></a>Vantaggi dell'esecuzione out-of-process

Quando si esegue out-of-process, le funzioni .NET possono sfruttare i vantaggi seguenti: 

+ Meno conflitti: poiché le funzioni vengono eseguite in un processo separato, gli assembly usati nell'app non saranno in conflitto con una versione diversa degli stessi assembly usati dal processo host.  
+ Controllo completo del processo: è possibile controllare l'avvio dell'app e controllare le configurazioni usate e il middleware avviato.
+ Inserimento delle dipendenze: poiché si ha il controllo completo del processo, è possibile usare i comportamenti .NET correnti per l'inserimento delle dipendenze e l'incorporamento del middleware nell'app per le funzioni. 

## <a name="supported-versions"></a>Versioni supportate

L'unica versione di .NET attualmente supportata per l'esecuzione out-of-process è .NET 5.0.

## <a name="net-isolated-project"></a>Progetto isolato .NET

Un progetto di funzione isolata .NET è fondamentalmente un progetto di app console .NET destinato a .NET 5.0. Di seguito sono riportati i file di base necessari in qualsiasi progetto isolato .NET:

+ [host.jssul](functions-host-json.md) file.
+ [local.settings.jssul](functions-run-local.md#local-settings-file) file.
+ File di progetto C# (con estensione csproj) che definisce il progetto e le dipendenze.
+ File Program.cs che rappresenta il punto di ingresso per l'app.

## <a name="package-references"></a>Riferimenti ai pacchetti

Quando si esegue out-of-process, il progetto .NET usa un set univoco di pacchetti, che implementano sia le funzionalità di base che le estensioni di associazione. 

### <a name="core-packages"></a>Pacchetti principali 

I pacchetti seguenti sono necessari per eseguire le funzioni .NET in un processo isolato:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pacchetti di estensione

Poiché le funzioni eseguite in un processo isolato .NET usano tipi di associazione diversi, richiedono un set univoco di pacchetti di estensioni di associazione. 

Questi pacchetti di estensione sono disponibili in [Microsoft.Azure.Functions.Worker.Extensions.](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)

## <a name="start-up-and-configuration"></a>Avvio e configurazione 

Quando si usano funzioni isolate .NET, è possibile accedere all'avvio dell'app per le funzioni, che in genere si trova in Program.cs. L'utente è responsabile della creazione e dell'avvio della propria istanza host. Di conseguenza, si ha anche accesso diretto alla pipeline di configurazione per l'app. Quando si esegue out-of-process, è molto più semplice aggiungere configurazioni, inserire dipendenze ed eseguire il proprio middleware. 

Il codice seguente illustra un esempio di pipeline [HostBuilder:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Questo codice richiede `using Microsoft.Extensions.DependencyInjection;` . 

HostBuilder [viene usato] per compilare e restituire un'istanza [IHost] completamente inizializzata, che viene eseguita in modo asincrono per avviare l'app per le funzioni. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configurazione

Il [metodo ConfigureFunctionsWorkerDefaults] viene usato per aggiungere le impostazioni necessarie per l'esecuzione out-of-process dell'app per le funzioni, che include le funzionalità seguenti:

+ Set predefinito di convertitori.
+ Impostare l'oggetto [JsonSerializerOptions predefinito per] ignorare la distinzione tra maiuscole e minuscole nei nomi delle proprietà.
+ Eseguire l'integrazione Funzioni di Azure registrazione.
+ Middleware di associazione di output e funzionalità.
+ Middleware di esecuzione della funzione.
+ Supporto predefinito di gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

L'accesso alla pipeline del generatore host significa che è anche possibile impostare configurazioni specifiche dell'app durante l'inizializzazione. È possibile chiamare il [metodo ConfigureAppConfiguration] in [HostBuilder] una o più volte per aggiungere le configurazioni richieste dall'app per le funzioni. Per altre informazioni sulla configurazione delle app, vedere [Configurazione in ASP.NET Core.](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true) 

Queste configurazioni si applicano all'app per le funzioni in esecuzione in un processo separato. Per apportare modifiche alla configurazione dell'host o del trigger e dell'associazione delle funzioni, è comunque necessario usare l'host.js[ nel file](functions-host-json.md).   

### <a name="dependency-injection"></a>Inserimento di dipendenze

L'inserimento delle dipendenze è semplificato rispetto alle librerie di classi .NET. Anziché creare una classe di avvio per registrare i servizi, è sufficiente chiamare [ConfigureServices] nel generatore di host e usare i metodi di estensione in [IServiceCollection] per inserire servizi specifici. 

L'esempio seguente inserisce una dipendenza del servizio singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Questo codice richiede `using Microsoft.Extensions.DependencyInjection;` . Per altre informazioni, vedere [Inserimento delle dipendenze in ASP.NET Core.](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)

### <a name="middleware"></a>Middleware

.NET isolato supporta anche la registrazione del middleware, anche in questo caso usando un modello simile a quello esistente in ASP.NET. Questo modello consente di inserire la logica nella pipeline di chiamata e prima e dopo l'esecuzione delle funzioni.

Il [metodo di estensione ConfigureFunctionsWorkerDefaults] ha un overload che consente di registrare il proprio middleware, come illustrato nell'esempio seguente.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Per un esempio più completo dell'uso del middleware personalizzato nell'app per le funzioni, vedere l'esempio di riferimento [del middleware personalizzato.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)

## <a name="execution-context"></a>Contesto di esecuzione

.NET Isolated passa un [oggetto FunctionContext] ai metodi della funzione. Questo oggetto consente di ottenere [un'istanza di ILogger] da scrivere nei log chiamando il [metodo GetLogger] e fornendo una `categoryName` stringa. Per altre informazioni, vedere [Registrazione.](#logging) 

## <a name="bindings"></a>Associazioni 

Le associazioni vengono definite usando attributi su metodi, parametri e tipi restituiti. Un metodo di funzione è un metodo con un attributo e un attributo trigger applicati a un parametro `Function` di input, come illustrato nell'esempio seguente:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

L'attributo trigger specifica il tipo di trigger e associa i dati di input a un parametro del metodo. La funzione di esempio precedente viene attivata da un messaggio della coda e il messaggio della coda viene passato al metodo nel `myQueueItem` parametro .

L'attributo `Function` indica il metodo come punto di ingresso della funzione. Il nome deve essere univoco all'interno di un progetto, iniziare con una lettera e contenere solo lettere, numeri, e fino a `_` `-` 127 caratteri. I modelli di progetto spesso creano un metodo denominato `Run`, ma il nome del metodo può essere qualsiasi nome di metodo c# valido.

Poiché i progetti isolati .NET vengono eseguiti in un processo di lavoro separato, le associazioni non possono sfruttare le classi di associazione più complessi, ad esempio `ICollector<T>` `IAsyncCollector<T>` , e `CloudBlockBlob` . Non è inoltre disponibile alcun supporto diretto per i tipi ereditati dagli SDK del servizio sottostanti, ad esempio [DocumentClient] [e BrokeredMessage.] Le associazioni si basano invece su stringhe, matrici e tipi serializzabili, ad esempio oggetti POCO (Plain Old Class Objects). 

Per i trigger HTTP, è necessario usare [HttpRequestData] e [HttpResponseData] per accedere ai dati di richiesta e risposta. Ciò è dovuto al fatto che non si ha accesso agli oggetti richiesta e risposta HTTP originali durante l'esecuzione out-of-process.

Per un set completo di esempi di riferimento per l'uso di trigger e associazioni durante l'esecuzione out-of-process, vedere l'esempio di riferimento delle estensioni [di associazione](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Associazioni di input

Una funzione può avere zero o più associazioni di input che possono passare dati a una funzione. Analogamente ai trigger, le associazioni di input vengono definite applicando un attributo di associazione a un parametro di input. Quando la funzione viene eseguita, il runtime tenta di ottenere i dati specificati nell'associazione. I dati richiesti dipendono spesso dalle informazioni fornite dal trigger usando i parametri di associazione.  

### <a name="output-bindings"></a>Associazioni di output

Per scrivere in un'associazione di output, è necessario applicare un attributo di associazione di output al metodo della funzione, che ha definito come scrivere nel servizio associato. Il valore restituito dal metodo viene scritto nell'associazione di output. Ad esempio, l'esempio seguente scrive un valore stringa in una coda di messaggi denominata `functiontesting2` usando un'associazione di output:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Più associazioni di output

I dati scritti in un'associazione di output sono sempre il valore restituito della funzione. Se è necessario scrivere in più di un'associazione di output, è necessario creare un tipo restituito personalizzato. A questo tipo restituito deve essere applicato l'attributo di associazione di output a una o più proprietà della classe . L'esempio seguente da un trigger HTTP scrive sia nella risposta HTTP che in un'associazione di output della coda:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

La risposta da un trigger HTTP viene sempre considerata un output, quindi non è necessario un attributo del valore restituito.

### <a name="http-trigger"></a>Trigger HTTP

I trigger HTTP converte il messaggio di richiesta HTTP in ingresso in un [oggetto HttpRequestData] passato alla funzione. Questo oggetto fornisce i dati della richiesta, tra cui `Headers` , , , e `Cookies` `Identities` `URL` facoltativamente un messaggio `Body` . Questo oggetto è una rappresentazione dell'oggetto richiesta HTTP e non della richiesta stessa. 

Analogamente, la funzione restituisce un oggetto [HttpResponseData,] che fornisce i dati usati per creare la risposta HTTP, tra cui il messaggio `StatusCode` , e `Headers` facoltativamente un messaggio `Body` .  

Il codice seguente è un trigger HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Registrazione

In .NET isolato è possibile scrivere nei log usando un'istanza [di ILogger] ottenuta da un [oggetto FunctionContext] passato alla funzione. Chiamare il [metodo GetLogger,] passando un valore stringa che rappresenta il nome della categoria in cui vengono scritti i log. La categoria è in genere il nome della funzione specifica da cui vengono scritti i log. Per altre informazioni sulle categorie, vedere [l'articolo sul monitoraggio.](functions-monitoring.md#log-levels-and-categories) 

L'esempio seguente illustra come ottenere un [ILogger] e scrivere log all'interno di una funzione:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Usare vari metodi di [ILogger] per scrivere vari livelli di log, ad esempio `LogWarning` o `LogError` . Per altre informazioni sui livelli di log, vedere [l'articolo sul monitoraggio.](functions-monitoring.md#log-levels-and-categories)

Un [ILogger viene fornito] anche quando si usa l'inserimento delle [dipendenze.](#dependency-injection)

## <a name="differences-with-net-class-library-functions"></a>Differenze con le funzioni della libreria di classi .NET

Questa sezione descrive lo stato corrente delle differenze funzionali e comportamentali in esecuzione in .NET 5.0 out-of-process rispetto alle funzioni della libreria di classi .NET in esecuzione in-process:

| Funzionalità/comportamento |  In-process (.NET Core 3.1) | Out-of-process (.NET 5.0) |
| ---- | ---- | ---- |
| Versioni di .NET | LTS (.NET Core 3.1) | Current (.NET 5.0) |
| Pacchetti di base | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Associazione di pacchetti di estensione | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | In [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Registrazione | [ILogger] passato alla funzione | [ILogger] ottenuto da [FunctionContext] |
| Token di annullamento | [Supportato](functions-dotnet-class-library.md#cancellation-tokens) | Non supportato |
| Associazioni di output | Parametri out | Valori restituiti |
| Tipi di associazioni di output |  `IAsyncCollector`, [DocumentClient,] [BrokeredMessage]e altri tipi specifici del client | Tipi semplici, tipi serializzabili JSON e matrici. |
| Più associazioni di output | Supportato | [Supportato](#multiple-output-bindings) |
| Trigger HTTP | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Funzioni permanenti | [Supportato](durable/durable-functions-overview.md) | Non supportato | 
| Associazioni imperative | [Supportato](functions-dotnet-class-library.md#binding-at-runtime) | Non supportato |
| function.jssull'artefatto | Generato | Non generato |
| Configurazione | [host.json](functions-host-json.md) | [host.js'inizializzazione personalizzata](functions-host-json.md) e |
| Inserimento di dipendenze | [Supportato](functions-dotnet-dependency-injection.md)  | [Supportato](#dependency-injection) |
| Middleware | Non supportato | Supportato |
| Orari di avvio a freddo | Typical (Tipica) | Più a lungo, a causa dell'avvio just-in-time. Eseguire in Linux anziché in Windows per ridurre i potenziali ritardi. |
| ReadyToRun | [Supportato](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Problemi noti

Per informazioni sulle soluzioni alternative per conoscere i problemi relativi all'esecuzione di funzioni di processo isolato .NET, vedere [questa pagina dei problemi noti](https://aka.ms/AAbh18e). Per segnalare i problemi, [creare un problema in questo repository GitHub.](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)  

## <a name="next-steps"></a>Passaggi successivi

+ [Altre informazioni sui trigger e le associazioni](functions-triggers-bindings.md)
+ [Altre informazioni sulle procedure consigliate per Funzioni di Azure](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[Configurare i servizi]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[Brokeredmessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
