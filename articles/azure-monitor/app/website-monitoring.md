---
title: 'Guida introduttiva: Monitorare siti Web con Application Insights di Monitoraggio di Azure'
description: Questo argomento di avvio rapido illustra come configurare il monitoraggio di siti Web sul lato client/browser con Application Insights di Monitoraggio di Azure.
ms.topic: quickstart
ms.date: 03/19/2021
ms.custom: mvc
ms.openlocfilehash: 0e10db39c8dbbf81087d696cfbb5b2ded1ae79ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654939"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Guida introduttiva: Avviare il monitoraggio del sito Web con Application Insights di Monitoraggio di Azure

Questo argomento di avvio rapido illustra come aggiungere JavaScript SDK per Application Insights open source al sito Web. Descrive inoltre in maggior dettaglio l'esperienza lato client/browser per i visitatori del sito Web.

Con Application Insights di Monitoraggio di Azure è possibile monitorare facilmente la disponibilità, le prestazioni e l'utilizzo di un sito Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente. Application Insights offre funzionalità di monitoraggio sia lato server che lato client/browser.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un sito Web a cui è possibile aggiungere JavaScript SDK per Application Insights.

## <a name="enable-application-insights"></a>Abilitare Application Insights

Application Insights può raccogliere dati di telemetria da qualsiasi applicazione connessa a Internet in esecuzione in locale o nel cloud. Per visualizzare questi dati, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Strumenti di gestione** > **Application Insights**.

   > [!NOTE]
   >Se è la prima volta che viene creata una risorsa di Application Insights, vedere [Creare una risorsa di Application Insights](./create-new-resource.md).
1. Quando viene visualizzata la finestra di configurazione, usare la tabella seguente per completare i campi di input:

    | Impostazioni        | valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Il nome che identifica l'app da monitorare. |
   | **Gruppo di risorse**     | myResourceGroup      | Il nome del nuovo gruppo di risorse in cui ospitare i dati di Application Insights. è possibile creare un nuovo gruppo di risorse o selezionarne uno esistente. |
   | **Posizione** | Stati Uniti orientali | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app. |
1. Selezionare **Create** (Crea).

## <a name="create-an-html-file"></a>Creare un file HTML

1. Nel computer locale creare un file denominato ``hello_world.html``. Per questo esempio, creare il file nella radice dell'unità C, ad esempio ``C:\hello_world.html``.
1. Copiare e incollare lo script seguente in ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configura Application Insights SDK

1. Selezionare **Panoramica** , quindi copiare la stringa di **connessione** dell'applicazione. Per questo esempio, è necessaria solo la parte relativa alla chiave di strumentazione della stringa di connessione `InstrumentationKey=00000000-0000-0000-0000-000000000000;` .

    :::image type="content" source="media/website-monitoring/keys.png" alt-text="Screenshot della pagina panoramica con la chiave di strumentazione e la stringa di connessione.":::

1. Aggiungere lo script seguente al file ``hello_world.html`` prima del tag ``</head>`` di chiusura:

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
        connectionString:"InstrumentationKey=YOUR_INSTRUMENTATION_KEY_GOES_HERE;" 
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > Il frammento corrente (elencato sopra) è la versione "5", la versione è codificata nel frammento come SV: "#" e la [versione corrente e i dettagli di configurazione sono disponibili in GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

1. Modificare il file ``hello_world.html`` e aggiungere la chiave di strumentazione.

1. Aprire ``hello_world.html`` in una sessione del browser locale. Questa azione crea una visualizzazione a pagina singola. È possibile aggiornare il browser per generare più visualizzazioni di pagine di test.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorare il sito Web nel portale di Azure

1. Riaprire la pagina **Panoramica** di Application Insights nel portale di Azure per visualizzare i dettagli relativi all'applicazione in esecuzione. La pagina **Panoramica** è la posizione in cui è stata recuperata la chiave di strumentazione.

   I quattro grafici predefiniti nella pagina di panoramica hanno come ambiti i dati dell'applicazione lato server. Dato che vengono instrumentate le interazioni lato client/browser con JavaScript SDK, questa specifica visualizzazione non si applica a meno che non sia installato anche un SDK lato server.

1. Selezionare **Log**.  Questa azione apre i **log**, che fornisce un linguaggio di query avanzato per l'analisi di tutti i dati raccolti da Application Insights. Per visualizzare i dati relativi alle richieste del browser sul lato client, eseguire la query seguente:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   :::image type="content" source="media/website-monitoring/log-query.png" alt-text="Screenshot del grafico di log Analytics delle richieste utente in un periodo di tempo.":::

1. Tornare alla pagina **Panoramica**. Nell'intestazione **analizza** selezionare **prestazioni** e quindi selezionare la scheda **esplorazione** .  Vengono visualizzate le metriche correlate alle prestazioni del sito Web. È disponibile una visualizzazione corrispondente per l'analisi degli errori e delle eccezioni nel sito Web. È possibile selezionare **Esempi** per accedere a [Dettagli sulle transazioni end-to-end](./transaction-diagnostics.md).

     :::image type="content" source="media/website-monitoring/performance.png" alt-text="Screenshot della scheda prestazioni con il grafico delle metriche del browser.":::

1. Nel menu principale di Application Insights, nell'intestazione **Utilizzo** selezionare [**Utenti**](./usage-segmentation.md) per iniziare a esplorazione gli [strumenti di analisi del comportamento degli utenti](./usage-overview.md). Poiché i test vengono eseguiti da un singolo computer, si vedranno i dati per un unico utente.

1. Per un sito Web più complesso con più pagine, è possibile usare lo strumento [**Flussi utente**](./usage-flows.md) per tenere traccia del percorso seguito dai visitatori attraverso le varie sezioni.

Per informazioni su configurazioni più avanzate per il monitoraggio di siti Web, vedere le [informazioni di riferimento sull'API di JavaScript SDK](./javascript.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a seguire altri argomenti di avvio rapido o esercitazioni, non pulire le risorse create in questa guida. In caso contrario, seguire questa procedura per eliminare tutte le risorse create in questa guida nel portale di Azure.

> [!NOTE]
> Se è stato usato un gruppo di risorse esistente, le istruzioni seguenti non si applicano. È invece possibile eliminare solo la singola risorsa di Application Insights. Tenere presente che ogni volta che si elimina un gruppo di risorse vengono eliminati anche tutti i membri e le risorse di tale gruppo.

1. Nel menu sinistro del portale di Azure selezionare **Gruppi di risorse** e quindi **myResourceGroup** oppure il nome di un proprio gruppo di risorse temporaneo.
1. Nella pagina del gruppo di risorse selezionare **Elimina**, immettere **myResourceGroup** nella casella di testo e quindi scegliere **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Rilevare e diagnosticare i problemi di prestazioni](../logs/log-query-overview.md)

