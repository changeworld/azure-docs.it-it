---
title: Telemetria per Analisi del traffico di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare Analisi del traffico di ricerca per Ricerca cognitiva di Azure, raccogliere i dati di telemetria e gli eventi avviati dall'utente tramite Application Insights e quindi analizzare i risultati in un report di Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485470"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Raccogliere i dati di telemetria per Analisi del traffico di ricerca

Analisi del traffico di ricerca è un modello per la raccolta di dati di telemetria sulle interazioni degli utenti con l'applicazione Ricerca cognitiva di Azure, ad esempio gli eventi Click avviati dall'utente e gli input da tastiera. Usando queste informazioni, è possibile determinare l'efficacia della soluzione di ricerca, inclusi i termini di ricerca più diffusi, il tasso di click-through e gli input della query che producono zero risultati.

Questo modello dipende da [Application Insights](../azure-monitor/app/app-insights-overview.md) (una funzionalità di [Monitoraggio di Azure](../azure-monitor/index.yml)) per la raccolta dei dati degli utenti. È necessario aggiungere la strumentazione al codice client, come descritto in questo articolo. Infine, sarà necessario un meccanismo di creazione di report per analizzare i dati. Si consiglia di Power BI, ma è possibile usare il dashboard dell'applicazione o qualsiasi strumento che si connette a Application Insights.

> [!NOTE]
> Il modello descritto in questo articolo è destinato agli scenari avanzati e ai dati clickstream generati dal codice aggiunto al client. Al contrario, i log del servizio sono facili da configurare, offrono numerose metriche e possono essere eseguiti nel portale senza che sia necessario alcun codice. L'abilitazione della registrazione è consigliata per tutti gli scenari. Per altre informazioni, vedere [Raccogliere e analizzare i dati dei log](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificare i dati di ricerca rilevanti

Per ottenere metriche utili per l'analisi del traffico di ricerca, è necessario registrare alcuni segnali provenienti dagli utenti dell'applicazione di ricerca. Questi segnali indicano contenuti che gli utenti trovano interessanti e considerano rilevanti. Per l'analisi del traffico di ricerca, includono:

+ Eventi di ricerca generati dall'utente: sono interessanti solo le query di ricerca avviate da un utente. Altre richieste di ricerca, ad esempio quelle usate per popolare i facet o recuperare informazioni interne, non sono importanti. Assicurarsi di instrumentare solo gli eventi avviati dall'utente per evitare distorsioni o distorsioni nei risultati.

+ Eventi Click generati dall'utente: in una pagina dei risultati della ricerca un evento Click in genere indica che un documento è un risultato rilevante per una specifica query di ricerca.

Collegando la ricerca e gli eventi Click con un ID di correlazione, è possibile conoscere in modo più approfondito le prestazioni della funzionalità di ricerca dell'applicazione.

## <a name="add-search-traffic-analytics"></a>Aggiungere Analisi del traffico di ricerca

Nella pagina del [portale](https://portal.azure.com) per il servizio ricerca cognitiva di Azure aprire la pagina Cerca analisi del traffico per accedere a un foglio informativo per seguire questo modello di telemetria. Da questa pagina è possibile selezionare o creare una risorsa Application Insights, ottenere la chiave di strumentazione, copiare frammenti che è possibile adattare per la soluzione e scaricare un report di Power BI compilato in base allo schema riflesso nel modello.

![Pagina Analisi del traffico di ricerca nel portale](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pagina Analisi del traffico di ricerca nel portale")

## <a name="1---set-up-application-insights"></a>1 - Impostare Application Insights

Selezionare una risorsa di Application Insights esistente o [crearne una](../azure-monitor/app/create-new-resource.md), se non è già disponibile. Se si usa la pagina Analisi del traffico di ricerca, è possibile copiare la chiave di strumentazione necessaria per la connessione dell'applicazione ad Application Insights.

Dopo aver creato una risorsa di Application Insights, è possibile seguire le [istruzioni per le piattaforme e le lingue supportate](../azure-monitor/app/platforms.md) per registrare l'app. La registrazione consiste semplicemente nell'aggiungere al codice la chiave di strumentazione ottenuta da Application Insights, che configura l'associazione. La chiave è disponibile nel portale o nella pagina Analisi del traffico di ricerca quando si seleziona una risorsa esistente.

Di seguito è riportata una procedura utilizzabile per alcuni tipi di progetto di Visual Studio. In tal modo, è possibile creare una risorsa e registrare l'app in pochi clic.

1. Per lo sviluppo di Visual Studio e ASP.NET, aprire la soluzione e selezionare **Progetto** > **Aggiungi Application Insights Telemetry**.

1. Fare clic su **Per iniziare**.

1. Registrare l'app fornendo un account Microsoft, una sottoscrizione di Azure e una risorsa di Application Insights (per impostazione predefinita, viene creata una nuova risorsa). Fare clic su **Register**.

A questo punto, l'applicazione è configurata per il monitoraggio, ovvero tutti i caricamenti di pagina vengono registrati con le metriche predefinite. Per altre informazioni sui passaggi precedenti, vedere [Abilitare la telemetria lato server di Application Insights](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Aggiungere la strumentazione

Questo è il passaggio in cui si esegue la strumentazione della propria applicazione di ricerca, usando la risorsa di Application Insights creata al passaggio precedente. Questo processo prevede quattro passaggi, a partire dalla creazione di un client di telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passaggio 1: Creare un client di telemetria

Creare un oggetto per l'invio degli eventi ad Application Insights. È possibile aggiungere la strumentazione al codice dell'applicazione lato server o al codice lato client in esecuzione in un browser, espressi qui come varianti C# e JavaScript (per altri linguaggi, vedere l'[elenco completo delle piattaforme e dei framework supportati](../azure-monitor/app/platforms.md). Scegliere l'approccio che fornisce il livello di dettaglio delle informazioni desiderato.

La telemetria lato server acquisisce le metriche a livello di applicazione, ad esempio nelle applicazioni in esecuzione come servizio Web nel cloud o come app locale in una rete aziendale. La telemetria lato server acquisisce la ricerca e gli eventi Click, la posizione di un documento nei risultati e le informazioni sulle query, ma l'ambito della raccolta dei dati sarà limitato alle informazioni disponibili in tale livello.

Sul client può essere presente codice aggiuntivo che modifica gli input della query, aggiunge funzionalità di spostamento o include il contesto (ad esempio, le query avviate da una home page anziché da una pagina di un prodotto). Se la soluzione in uso presenta questi requisiti, è possibile optare per la strumentazione lato client, in modo che i dati di telemetria riflettano i dettagli aggiuntivi. Il modo in cui vengono raccolti i dettagli aggiuntivi esula dall'ambito di questo modello, ma è possibile consultare [Application Insights per le pagine Web](../azure-monitor/app/javascript.md#explore-browserclient-side-data) per altre indicazioni. 

**Usare C#**

Per C#, il **InstrumentationKey** deve essere definito nella configurazione dell'applicazione, ad esempio appsettings.jsse il progetto è ASP.NET. Se non si è certi della posizione della chiave, consultare le istruzioni per la registrazione.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Usare JavaScript**

Il frammento corrente (elencato di seguito) è la versione "5", la versione è codificata nel frammento come SV: "#" e la [versione corrente è disponibile anche in GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Per migliorare la leggibilità e ridurre i possibili errori di JavaScript, tutte le opzioni di configurazione possibili sono elencate in una nuova riga nel codice del frammento precedente, se non si vuole modificare il valore di una riga commentata, è possibile rimuoverla.


### <a name="step-2-request-a-search-id-for-correlation"></a>Passaggio 2: Richiedere un ID di ricerca per la correlazione

Per correlare le richieste di ricerca con i clic, è necessario disporre di un ID di correlazione che metta in correlazione questi due eventi distinti. Ricerca cognitiva di Azure fornisce un ID di ricerca quando viene richiesto con un'intestazione HTTP.

L'ID di ricerca consente la correlazione delle metriche emesse da Ricerca cognitiva di Azure per la richiesta stessa con le metriche personalizzate che si stanno registrando in Application Insights.

**Usare C# (V11 SDK più recente)**

L'SDK più recente richiede l'uso di una pipeline HTTP per impostare l'intestazione come descritto in dettaglio in questo [esempio](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Usare C# (precedente V10 SDK)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Usare JavaScript (chiamata di API REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passaggio 3: Registrare gli eventi di ricerca

Ogni volta che un utente esegue una richiesta di ricerca, è necessario registrarla come evento di ricerca con lo schema seguente in un evento personalizzato di Application Insights. Ricordare di registrare solo le query di ricerca generate dall'utente.

+ **SearchServiceName**: (stringa) nome del servizio di ricerca
+ **SearchId**: (GUID) identificatore univoco della query di ricerca (incluso nella risposta alla ricerca)
+ **IndexName**: (stringa) indice del servizio di ricerca da sottoporre a query
+ **QueryTerms**: (stringa) termini di ricerca immessi dall'utente
+ **ResultCount**: (numero intero) numero di documenti restituiti (incluso nella risposta alla ricerca)
+ **ScoringProfile**: (stringa) nome del profilo di punteggio usato, se disponibile

> [!NOTE]
> Richiedere il conteggio delle query generate dall'utente mediante l'aggiunta di $count=true alla query di ricerca. Per altre informazioni, vedere [Cercare documenti (API REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**Usare C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Usare JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Passaggio 4: Registrare gli eventi Click

Ogni clic di un utente su un documento è un segnale che deve essere registrato a scopo di analisi delle ricerche. Utilizzare gli eventi personalizzati di Application Insights per registrare questi eventi con lo schema seguente:

+ **ServiceName**: (stringa) nome del servizio di ricerca
+ **SearchId**: (GUID) identificatore univoco della query di ricerca correlata
+ **DocId**: (stringa) identificatore del documento
+ **Position**: (numero intero) posizione del documento nella pagina dei risultati della ricerca

> [!NOTE]
> Position fa riferimento all'ordine cardinale nell'applicazione. È possibile impostare questo numero, purché si tratti sempre dello stesso, per consentire il confronto.
>

**Usare C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Usare JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Eseguire l'analisi in Power BI

Dopo avere instrumentato l'app e averne verificata la corretta connessione ad Application Insights, è possibile scaricare un modello di report predefinito per l'analisi dei dati in Power BI Desktop. Il report contiene tabelle e grafici predefiniti utili per analizzare i dati aggiuntivi acquisiti per Analisi del traffico di ricerca.

1. Nel riquadro di navigazione sinistro del dashboard di Ricerca cognitiva di Azure, nella sezione **Impostazioni**, fare clic su **Analisi del traffico di ricerca**.

1. Nella pagina **Analisi del traffico di ricerca**, nel passaggio 3, fare clic su **Scarica Power BI Desktop** per installare Power BI.

   ![Ottenere report di Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Ottenere report di Power BI")

1. Nella stessa pagina fare clic su **Scarica report di Power BI**.

1. Il report viene aperto in Power BI Desktop e viene richiesto di connettersi ad Application Insights e di specificare le credenziali. È possibile trovare le informazioni di connessione nelle pagine del portale di Azure relative alla risorsa Application Insights. Per le credenziali, specificare lo stesso nome utente e la stessa password usati per l'accesso al portale.

   ![Connettersi ad Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Connettersi ad Application Insights")

1. Fare clic su **Carica**.

Il report contiene grafici e tabelle che consentono di prendere decisioni più informate per migliorare le prestazioni di ricerca e la rilevanza.

Sono incluse le metriche seguenti:

+ Volume di ricerca e coppie termine-documento più comuni: termini che producono lo stesso documento selezionato, ordinati per clic.
+ Ricerche senza clic: termini delle query principali per i quali non sono registrati clic.

Lo screenshot seguente mostra l'aspetto di un report predefinito se sono stati usati tutti gli elementi dello schema.

![Dashboard di Power BI per Ricerca cognitiva di Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Dashboard di Power BI per Ricerca cognitiva di Azure")

## <a name="next-steps"></a>Passaggi successivi

Instrumentare l'applicazione di ricerca per ottenere dati estremamente utili e dettagliati relativi al servizio di ricerca.

È possibile trovare altre informazioni su [Application Insights](../azure-monitor/app/app-insights-overview.md) e visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/) per conoscere i diversi livelli di servizio.

Altre informazioni sulla creazione di report utili Per informazioni dettagliate, vedere [Introduzione a Power BI Desktop](/power-bi/fundamentals/desktop-getting-started).
