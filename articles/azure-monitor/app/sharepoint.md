---
title: Monitorare un sito di SharePoint con Application Insights
description: Avviare il monitoraggio di una nuova applicazione con una nuova chiave di strumentazione
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 74f0eeecb303d68af8aaa48b5c1806ceeef4d318
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486370"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorare un sito di SharePoint con Application Insights

Azure Application Insights consente di monitorare disponibilità, prestazioni e utilizzo delle app. Di seguito verrà illustrato come impostarlo per un sito di SharePoint.

> [!NOTE]
> A causa di problemi di sicurezza, non è possibile aggiungere direttamente lo script descritto in questo articolo alle pagine Web nell'esperienza utente di SharePoint moderna. In alternativa, è possibile utilizzare [SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) per creare un'estensione personalizzata che è possibile utilizzare per installare Application Insights nei siti di SharePoint.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights
Nel [portale di Azure](https://portal.azure.com) creare una nuova risorsa di Application Insights. Scegliere ASP.NET come tipo di applicazione.

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/sharepoint/001.png)

Verrà visualizzata una finestra che mostra i dati di prestazioni e utilizzo relativi all'app. Per visualizzare di nuovo questo pannello al successivo accesso ad Azure, nella schermata Start dovrebbe venire visualizzato un riquadro per l'app. In alternativa, fare clic su Sfoglia per cercarla.

## <a name="add-the-script-to-your-web-pages"></a>Aggiungere lo script alle pagine Web

Il frammento corrente (elencato di seguito) è la versione "5", la versione è codificata nel frammento come SV: "#" e la [versione corrente è disponibile anche in GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  instrumentationKey:"INSTRUMENTATION_KEY"
}});
</script>
```

> [!NOTE]
> L'URL per SharePoint utilizza un formato di modulo diverso "...\ai.2.gbl.min.js" (si noti il **. GBL** aggiuntivo). questo formato di modulo alternativo è necessario per evitare un problema causato dall'ordine con cui vengono caricati gli script, causando il mancato inizializzazione dell'SDK e la perdita di eventi di telemetria.
>
> Il problema è causato dal caricamento e dall'inizializzazione di requireJS prima dell'SDK.

Inserire lo script immediatamente prima del &lt; &gt; tag/Head di ogni pagina di cui si vuole tenere traccia. Se il sito Web dispone di una pagina master, è possibile inserire lo script in tale pagina. Ad esempio, in un progetto ASP.NET MVC inserire lo script in View\Shared\_Layout.cshtml.

Lo script contiene la chiave di strumentazione che indirizza i dati di telemetria alla risorsa di Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Aggiungere il codice alle pagine del sito
#### <a name="on-the-master-page"></a>Nella pagina master
Se è possibile modificare la pagina master del sito, che fornisce il monitoraggio per ogni pagina del sito.

Consultare la pagina master e modificarla mediante SharePoint Designer o un altro editor.

![Screenshot che illustra come modificare la pagina master utilizzando SharePoint Designer o un altro editor.](./media/sharepoint/03-master.png)

Aggiungere il codice immediatamente prima del </head> Tag. 

![Screenshot che mostra dove aggiungere il codice alla pagina del sito.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>oppure nelle singole pagine
Per monitorare un set limitato di pagine, aggiungere lo script separatamente a ogni pagina. 

Inserire una Web part e incorporarvi il frammento di codice.

![Screenshot che Mostra come aggiungere lo script per monitorare un set limitato di pagine.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Visualizza i dati sull'app
Ridistribuire l'app.

Tornare al pannello dell'applicazione nel [portale di Azure](https://portal.azure.com).

I primi eventi verranno visualizzati nella ricerca. 

![Screenshot che mostra i nuovi dati che è possibile visualizzare nell'app.](./media/sharepoint/09-search.png)

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

## <a name="capturing-user-id"></a>Acquisizione dell'ID utente
Il frammento di codice della pagina Web standard non acquisisce l'ID utente da SharePoint. Per l'acquisizione è necessario eseguire una piccola modifica.

1. Copiare la chiave di strumentazione dell'app dall'elenco a discesa Informazioni di base in Application Insights. 

    ![Screenshot che mostra la copia della strumentazione dell'app dall'elenco a discesa Essentials in Application Insights.](./media/sharepoint/02-props.png)

1. Sostituire la chiave di strumentazione per "XXXX" nel frammento di codice riportato di seguito. 
2. Incorporare lo script nell'app di SharePoint anziché il frammento di codice ottenuto dal portale.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Passaggi successivi
* [Test web](./monitor-web-app-availability.md) per monitorare la disponibilità del sito.
* [Application Insights](./app-insights-overview.md) per altri tipi di app.

<!--Link references-->
