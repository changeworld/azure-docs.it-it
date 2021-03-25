---
title: Risolvere i problemi relativi a applicazione Azure Profiler Insights
description: Questo articolo presenta informazioni e procedure per la risoluzione dei problemi che consentono agli sviluppatori di abilitare e usare Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c80362c3407f1404c6657997de89c2741264909
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026556"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Risolvere i problemi di abilitazione o visualizzazione di Application Insights Profiler

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Risoluzione dei problemi generali

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Assicurarsi di usare l'endpoint del profiler appropriato

Attualmente le uniche aree che richiedono modifiche all'endpoint sono [Azure per enti pubblici](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [Azure Cina](/azure/china/resources-developer-guide).

|Impostazione app    | Cloud per enti pubblici degli Stati Uniti | Cloud per la Cina |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>I profili vengono caricati solo se sono presenti richieste all'applicazione mentre Profiler è in esecuzione

Applicazione Azure Insights profiler raccoglie i dati per due minuti ogni ora. È inoltre in grado di raccogliere dati quando si seleziona il pulsante **profilo ora** nel riquadro **Configura Application Insights Profiler** .

> [!NOTE]
> I dati di profilatura vengono caricati solo quando è possibile collegarli a una richiesta che si è verificata durante l'esecuzione di Profiler. 

Profiler scrive i messaggi di analisi e gli eventi personalizzati nella risorsa di Application Insights. È possibile usare questi eventi per vedere come profiler è in esecuzione:

1. Cercare i messaggi di analisi e gli eventi personalizzati inviati da Profiler alla risorsa di Application Insights. È possibile usare questa stringa di ricerca per trovare i dati rilevanti:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    L'immagine seguente illustra due esempi di ricerche da due risorse di Application Insights: 
    
   * Come si può vedere a sinistra, l'applicazione non riceve richieste durante l'esecuzione di Profiler. Il messaggio spiega che il caricamento è stato annullato a causa dell'assenza di attività. 

   * Come si può vedere a destra, Profiler è stato avviato e ha inviato eventi personalizzati a seguito di richieste avvenute durante l'esecuzione. Se `ServiceProfilerSample` viene visualizzato l'evento personalizzato, significa che un profilo è stato acquisito e che è disponibile nel riquadro **prestazioni Application Insights** .

     Se non viene visualizzato alcun record, il profiler non è in esecuzione. Per risolvere il problema, vedere le sezioni relative alla risoluzione dei problemi per il tipo di app specifico più avanti in questo articolo.  

     ![Cercare dati di telemetria di Profiler][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Altri aspetti da controllare
* Assicurarsi che l'app sia in esecuzione in .NET Framework 4.6.
* Se l'app Web è un'applicazione ASP.NET Core, deve eseguire almeno ASP.NET Core 2.0.
* Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, limitare il filtro temporale e riprovare. Le tracce vengono eliminate dopo sette giorni.
* Assicurarsi che i proxy o un firewall non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net .
* Profiler non è supportato nei piani di servizio app gratuiti o condivisi. Se si usa uno di questi piani, provare a scalare fino a uno dei piani di base e Profiler dovrebbe iniziare a funzionare.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Doppio conteggio in thread paralleli

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata della richiesta.

Questa situazione può verificarsi quando due o più thread paralleli sono associati a una richiesta. In questo caso, il tempo totale di thread è superiore al tempo trascorso.

Un thread può essere in attesa del completamento dell'altro. Il visualizzatore prova a rilevare questa situazione e omette l'attesa non interessante. Ciò facendo, sbaglia visualizzando troppe informazioni anziché omettendo informazioni potenzialmente critiche.

Quando vengono visualizzati thread paralleli nelle tracce, determinare quali thread sono in attesa in modo da poter identificare il percorso critico per la richiesta.

Un thread che passa rapidamente in uno stato di attesa è in genere in attesa di altri thread. Concentrarsi sugli altri thread e ignorare il tempo nei thread in attesa.

### <a name="error-report-in-the-profile-viewer"></a>Report degli errori nel visualizzatore profili
Inviare un ticket di supporto nel portale. Verificare di includere l'ID di correlazione dal messaggio di errore.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Risolvere i problemi di Profiler nel Servizio app di Azure

Per il corretto funzionamento di Profiler:
* Il piano di servizio dell'app Web deve essere di livello Basic o superiore.
* Application Insights deve essere abilitato per l'app Web.
* L'app Web deve avere le impostazioni dell'app seguenti:

    |Impostazione app    | Valore    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey della risorsa di Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Il processo Web **ApplicationInsightsProfiler3** deve essere in esecuzione. Per controllare il processo Web:
   1. Passare a [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Scegliere **WebJobs Dashboard** (Dashboard processi Web) dal menu **Tools** (Strumenti).  
      Verrà visualizzato il riquadro **WebJobs** (Processi Web). 
   
      ![Screenshot mostra il riquadro processi Web, che consente di visualizzare il nome, lo stato e l'ora dell'ultima esecuzione dei processi.][profiler-webjob]   
   
   1. Per visualizzare i dettagli del processo Web, incluso il log, selezionare il collegamento **ApplicationInsightsProfiler3** .  
     Verrà visualizzato il riquadro **Continuous WebJob Details** (Dettagli processo Web continuo).

      ![Screenshot che mostra il riquadro dei dettagli di processo Web continuo.][profiler-webjob-log]

Se Profiler non funziona, è possibile scaricare il log e inviarlo al team per assistenza serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Controllare la pagina di stato dell'estensione del sito dei servizi di diagnostica
Se Profiler è stato abilitato tramite il [riquadro Application Insights](profiler.md) nel portale, è stato abilitato dall'estensione del sito dei servizi di diagnostica.

> [!NOTE]
> L'installazione di Application Insights Profiler non codificata segue i criteri di supporto di .NET Core.
> Per altre informazioni sui Runtime supportati, vedere [criteri di supporto di .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Per controllare la pagina stato di questa estensione, passare all'URL seguente: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Il dominio del collegamento alla pagina di stato può variare a seconda del cloud.
Questo dominio sarà uguale al sito di gestione Kudu per il servizio app.

In questa pagina di stato viene visualizzato lo stato di installazione del profiler e degli agenti di Snapshot Collector. Se si è verificato un errore imprevisto, questo verrà visualizzato e mostrerà come risolverlo.

È possibile usare il sito di gestione Kudu per il servizio app per ottenere l'URL di base di questa pagina di stato:
1. Aprire l'applicazione del servizio app nel portale di Azure.
2. Selezionare **strumenti avanzati** o cercare **Kudu**.
3. Selezionare **Vai**.
4. Quando ci si trova nel sito di gestione Kudu, nell'URL **aggiungere quanto segue `/DiagnosticServices` e premere invio**.
 Si concluderà come segue: `https://<kudu-url>/DiagnosticServices`

Verrà visualizzata una pagina di stato simile alla seguente: ![ pagina stato servizi di diagnostica](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Installazione manuale

Quando si configura Profiler, vengono apportati alcuni aggiornamenti alle impostazioni dell'app Web. Se l'ambiente lo richiede, è possibile applicare gli aggiornamenti manualmente. Ad esempio nel caso in cui l'applicazione sia in esecuzione in un ambiente di App Web per PowerApps. Per applicare gli aggiornamenti manualmente:

1. Nel riquadro **controllo app Web** aprire **Impostazioni**.

1. Impostare la **versione di .NET Framework** su **v 4.6**.

1. Attivare Always On.
1. Creare le impostazioni dell'app:

    |Impostazione app    | Valore    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey della risorsa di Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Troppe sessioni di profilatura attive

È possibile abilitare Profiler in un massimo di quattro app Web in esecuzione nello stesso piano di servizio. Se si dispone di più di quattro, il profiler potrebbe generare un oggetto *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException*. Per risolvere il problemi, spostare alcune app Web in un piano di servizio diverso.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Errore di distribuzione: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si sta ridistribuendo l'app Web a una risorsa di App Web con Profiler abilitato, può essere visualizzato il messaggio seguente:

*Directory non vuota ' d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs '*

Questo errore si verifica se si esegue Distribuzione Web da script o dalla Azure Pipelines. La soluzione consiste nell'aggiungere i parametri di distribuzione seguenti all'attività Distribuzione Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Questi parametri eliminano la cartella usata da Application Insights Profiler e sbloccano il processo di ridistribuzione. Non influiscono sull'istanza di Profiler attualmente in esecuzione.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il Application Insights Profiler è in esecuzione?

Profiler viene eseguito come processo Web continuo nell'app Web. È possibile aprire la risorsa dell'app Web nel [portale di Azure](https://portal.azure.com). Nel riquadro **WebJobs** controllare lo stato di **ApplicationInsightsProfiler**. Se non è in esecuzione, aprire **Log** per altre informazioni.

## <a name="troubleshoot-vms-and-cloud-services"></a>Risolvere i problemi relativi alle macchine virtuali e ai servizi cloud

>**Il bug nel profiler fornito in WAD per i servizi cloud è stato risolto.** La versione più recente di WAD (1.12.2.0) per i servizi cloud funziona con tutte le versioni recenti di App Insights SDK. Gli host del servizio cloud eseguiranno l'aggiornamento automatico di WAD, ma non è immediato. Per forzare un aggiornamento, è possibile ridistribuire il servizio o riavviare il nodo.

Per verificare se Profiler è configurato correttamente da Diagnostica di Azure, attenersi alla procedura seguente: 
1. Verificare che il contenuto della configurazione Diagnostica di Azure distribuita sia quello previsto. 

1. In secondo luogo, assicurarsi che Diagnostica di Azure passi la chiave di strumentazione corretta nella riga di comando di Profiler. 

1. In terzo luogo, controllare nel file di log di Profiler se quest'ultimo è stato eseguito ma ha restituito un errore. 

Per controllare le impostazioni usate per configurare Diagnostica di Azure:

1. Accedere alla macchina virtuale (VM), quindi aprire il file di log in questo percorso. La versione del plug-in può essere più recente nel computer.
    
    Per le macchine virtuali:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Per Servizi cloud:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. Cercare nel file la stringa **WadCfg** per trovare le impostazioni passate alla macchina virtuale per configurare Diagnostica di Azure. È possibile verificare se la chiave di strumentazione usata dal sink di Profiler è corretta.

1. Controllare la riga di comando usata per avviare Profiler. Gli argomenti utilizzati per avviare profiler si trovano nel file seguente. (L'unità potrebbe essere c: o d: e la directory potrebbe essere nascosta).

    Per le macchine virtuali:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    per i servizi cloud:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Assicurarsi che la chiave di strumentazione nella riga di comando di Profiler sia corretta. 

1. Utilizzando il percorso trovato nella *config.js* precedente nel file, controllare il file di log del profiler, denominato **bootstrapn. log**. che visualizza le informazioni di debug che indicano le impostazioni usate da Profiler, nonché i messaggi di errore e di stato di Profiler.  

    Per le macchine virtuali, il file è il seguente:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Per Servizi cloud:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Se Profiler è in esecuzione mentre l'applicazione riceve le richieste, viene visualizzato il messaggio seguente: *attività rilevata da Ikey*. 

    Quando viene caricata la traccia, viene visualizzato il messaggio seguente: *inizia a caricare la traccia*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Modificare le regole proxy o firewall di rete

Se l'applicazione si connette a Internet tramite un proxy o un firewall, potrebbe essere necessario aggiornare le regole per comunicare con il servizio Profiler.

Gli IP usati da Application Insights Profiler sono inclusi nel tag del servizio di monitoraggio di Azure. Per ulteriori informazioni, vedere la [documentazione dei tag di servizio](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png