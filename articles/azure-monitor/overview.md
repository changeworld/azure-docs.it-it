---
title: Panoramica di Monitoraggio di Azure | Microsoft Docs
description: Panoramica dei servizi e delle funzionalità Microsoft che contribuiscono a una strategia di monitoraggio completa per i servizi e le applicazioni di Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 43ceaa716bf9b39dd1686be0c5a853e350cbe118
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582931"
---
# <a name="azure-monitor-overview"></a>Panoramica di Monitoraggio di Azure

Monitoraggio di Azure consente di aumentare la disponibilità e le prestazioni di applicazioni e servizi. Offre una soluzione completa per la raccolta, l'analisi e l'esecuzione di operazioni sui dati di telemetria dal cloud e dagli ambienti locali. Queste informazioni sono utili per valutare il livello di prestazioni delle applicazioni e identificare proattivamente i problemi che le riguardano e le risorse da cui dipendono.

Ecco alcuni esempi di cosa è possibile fare con Monitoraggio di Azure:

- Rilevare e diagnosticare i problemi in applicazioni e dipendenze con [Application Insights](app/app-insights-overview.md).
- Correlare i problemi di infrastruttura con [Monitoraggio di Azure per le macchine virtuali](vm/vminsights-overview.md) e [Monitoraggio di Azure per i contenitori](containers/container-insights-overview.md).
- Esaminare i dati di monitoraggio con [Log Analytics](logs/log-query-overview.md) per la risoluzione dei problemi e la diagnostica approfondita.
- Supportare operazioni su larga scala con gli [avvisi intelligenti](alerts/alerts-smartgroups-overview.md) e le [azioni automatizzate](alerts/alerts-action-rules.md).
- Creare visualizzazioni con i [dashboard](visualize/tutorial-logs-dashboards.md) e le [cartelle di lavoro](visualize/workbooks-overview.md) di Azure.
- Raccogliere dati dalle [risorse monitorate](./monitor-reference.md) usando le [metriche di Monitoraggio di Azure](./essentials/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Panoramica

Il diagramma seguente offre una panoramica di Monitoraggio di Azure. Al centro del diagramma si trovano gli archivi dati per le metriche e i log, i due tipi fondamentali di dati usati da Monitoraggio di Azure. A sinistra si trovano le [origini dei dati di monitoraggio](agents/data-sources.md) che popolano tali [archivi dati](/data-platform.md). A destra ci sono le diverse funzioni svolte da Monitoraggio di Azure con i dati raccolti, come analisi, avvisi e streaming a sistemi esterni.

![Panoramica di Monitoraggio di Azure](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Piattaforma di monitoraggio dei dati

Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno di due tipi fondamentali, [metriche e log](/data-platform.md). Le [metriche](essentials/data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Sono elementi leggeri in grado di supportare scenari praticamente in tempo reale. I [log](logs/data-platform-logs.md) contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. I dati di telemetria, come eventi e tracce, vengono archiviati come log insieme ai dati sulle prestazioni in modo da poter essere combinati per l'analisi.

Per molte risorse di Azure, i dati raccolti da Monitoraggio di Azure sono visualizzati a destra nella pagina Panoramica nel portale di Azure. Osservando una qualsiasi macchina virtuale, ad esempio, si vedono diversi grafici che mostrano le metriche delle prestazioni. Fare clic su uno dei grafici per aprire i dati in [Esplora metriche](essentials/metrics-charts.md) nel portale di Azure, che consente di rappresentare in un grafico i valori di più metriche nel tempo.  È possibile visualizzare i grafici in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni.

![Il diagramma mostra il flusso di dati delle metriche in Esplora metriche da usare nelle visualizzazioni.](media/overview/metrics.png)

I dati di log raccolti da Monitoraggio di Azure possono essere analizzati con [query](logs/log-query-overview.md) per recuperare, consolidare e analizzare rapidamente i dati raccolti.  È possibile creare e testare le query usando [Log Analytics](./logs/log-query-overview.md) nel portale di Azure e quindi analizzare direttamente i dati tramite strumenti diversi oppure salvare le query per usarle con [visualizzazioni](visualizations.md) o [regole di avviso](alerts/alerts-overview.md).

Monitoraggio di Azure usa una versione del [linguaggio di query Kusto](/azure/kusto/query/) adatto a query semplici nei log, ma include anche funzionalità avanzate come aggregazioni, join e analisi intelligenti. È possibile apprendere rapidamente il linguaggio di query usando le [numerose lezioni](logs/get-started-queries.md) disponibili.  Indicazioni specifiche sono disponibili per gli utenti che hanno già familiarità con [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![Il diagramma mostra il flusso di dati dei log in Log Analytics per l'analisi.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quali dati vengono raccolti da Monitoraggio di Azure?

Monitoraggio di Azure può raccogliere dati da un'ampia [varietà di origini](monitor-reference.md), che includono l'applicazione, il sistema operativo e i servizi su cui si basa e anche la piattaforma stessa. Monitoraggio di Azure raccoglie i dati da ciascuno dei livelli seguenti:

- **Dati di monitoraggio dell'applicazione**: dati relativi alle prestazioni e alle funzionalità del codice scritto indipendentemente dalla piattaforma.
- **Dati di monitoraggio del sistema operativo guest**: dati relativi al sistema operativo in cui viene eseguita l'applicazione. L'applicazione può essere eseguita in Azure, un altro cloud o in locale. 
- **Dati di monitoraggio delle risorse di Azure**: dati relativi al funzionamento di una risorsa di Azure.
- **Dati di monitoraggio della sottoscrizione di Azure**: dati relativi al funzionamento e alla gestione di una sottoscrizione di Azure e dati relativi all'integrità e al funzionamento di Azure stesso. 
- **Dati di monitoraggio del tenant di Azure:** i dati relativi al funzionamento dei servizi di Azure a livello di tenant, ad esempio Azure Active Directory.

Non appena si crea una sottoscrizione di Azure e si inizia ad aggiungere risorse quali macchine virtuali e app Web, Monitoraggio di Azure avvia la raccolta dei dati.  I [log attività](essentials/platform-logs-overview.md) registrano quando vengono create o modificate le risorse. Le [metriche](/data-platform.md) indicano le prestazioni della risorsa e quali risorse utilizza. 

[Abilitare la diagnostica](essentials/platform-logs-overview.md) per includere nei dati da raccogliere il funzionamento interno delle risorse.  [Aggiungere un agente](agents/agents-overview.md) nelle risorse di calcolo per raccogliere dati di telemetria dai rispettivi sistemi operativi guest. 

Abilitare il monitoraggio per l'applicazione con [Application Insights](app/app-insights-overview.md) per raccogliere informazioni dettagliate come visualizzazioni delle pagine, richieste di applicazioni ed eccezioni. Verificare ulteriormente la disponibilità dell'applicazione configurando un [test di disponibilità](app/monitor-web-app-availability.md) per simulare il traffico utente.

### <a name="custom-sources"></a>Origini personalizzate

Monitoraggio di Azure può raccogliere dati di log da qualsiasi client REST tramite l'[API dell'agente di raccolta dati](logs/data-collector-api.md). In questo modo è possibile creare scenari di monitoraggio personalizzati ed estendere il monitoraggio alle risorse che non espongono dati di telemetria tramite altre origini.

## <a name="insights"></a>Informazioni dettagliate
I dati di monitoraggio sono utili solo se possono aumentare la visibilità del funzionamento dell'ambiente di elaborazione. La risorsa [Insights](monitor-reference.md#insights-and-core-solutions) offre un'esperienza di monitoraggio personalizzata per specifici servizi di Azure. Richiede una configurazione minima e aumenta la visibilità sul funzionamento delle risorse cruciali.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitora disponibilità, prestazioni e utilizzo delle applicazioni Web indipendentemente dal fatto che siano ospitate nel cloud o in locale. Sfrutta la piattaforma avanzata di analisi dei dati di Monitoraggio di Azure per fornire informazioni molto dettagliate sulle operazioni dell'applicazione. Consente di diagnosticare gli errori senza aspettare che vengano segnalati da un utente. Application Insights include punti di connessione a una gamma di strumenti di sviluppo e si integra con Visual Studio per supportare i processi DevOps.

![Informazioni sull'app](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
[Monitoraggio di Azure per contenitori](containers/container-insights-overview.md) monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Offre visibilità sulle prestazioni raccogliendo metriche da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster di Kubernetes, le metriche e i log vengono raccolti automaticamente tramite una versione dell'agente di Log Analytics per Linux inclusa in un contenitore.

![Integrità dei contenitori](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](vm/vminsights-overview.md) monitora le macchine virtuali di Azure su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux e ne identifica i diversi processi e le dipendenze interconnesse da processi esterni. La soluzione include il supporto per il monitoraggio delle prestazioni e delle dipendenze dell'applicazione per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud.  


![Informazioni dettagliate macchina virtuale](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Risposta alle situazioni critiche
Oltre a consentire l'analisi interattiva dei dati di monitoraggio, una soluzione di monitoraggio efficace deve essere in grado di rispondere in modo proattivo alle condizioni critiche identificate nei dati raccolti. La risposta può consistere nell'invio di un SMS o di un messaggio di posta elettronica a un amministratore responsabile dell'analisi di un problema. In alternativa è possibile avviare un processo automatizzato che tenti di risolvere una condizione di errore.


### <a name="alerts"></a>Avvisi
Gli [avvisi di Monitoraggio di Azure](alerts/alerts-overview.md) inviano notifiche proattive sulle condizioni critiche e tentano di eseguire azioni correttive. Le regole di avviso basate su metriche offrono avvisi quasi in tempo reale basati su valori numerici. Le regole basate su log rendono disponibile una logica complessa per i dati di più origini.

Le regole di avviso in Monitoraggio di Azure utilizzano i [gruppi di azioni](alerts/action-groups.md) che contengono set univoci di destinatari e azioni che possono essere condivise tra più regole. In base alle esigenze, i gruppi di azioni possono eseguire azioni come l'uso di webhook per far sì che gli avvisi avviino azioni esterne o si integrino con gli strumenti di gestione dei servizi IT.

![Lo screenshot mostra gli avvisi in Monitoraggio di Azure con gravità, numero di avvisi totali e altre informazioni.](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
Il ridimensionamento automatico offre la possibilità di avere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Creare regole che usano le metriche raccolte da Monitoraggio di Azure per determinare quando aggiungere automaticamente risorse all'aumento del carico. Rimuovere le risorse inattive per risparmiare sui costi. Specificare un numero minimo e massimo di istanze e la logica per determinare quando aumentare o ridurre le risorse.

![Il diagramma mostra la scalabilità automatica, con diversi server in una riga con etichetta Tempo processore > 80% e due server contrassegnati come minimo, tre server come capacità corrente e cinque come massimo.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizzazione dei dati di monitoraggio
Le [visualizzazioni](visualizations.md), ad esempio tabelle e grafici, sono strumenti efficaci per riepilogare i dati di monitoraggio e presentarli a diverse tipologie di destinatari. Monitoraggio di Azure ha funzionalità proprie per visualizzare i dati di monitoraggio e utilizza altri servizi di Azure per la pubblicazione dei dati a diversi destinatari.

### <a name="dashboards"></a>Dashboard
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) consentono di combinare tipi di dati diversi in un singolo riquadro nel [portale di Azure](https://portal.azure.com). È possibile condividere il dashboard con altri utenti di Azure. Aggiungere l'output di eventuali grafici di metriche o query dei log a un dashboard di Azure. È ad esempio possibile creare un dashboard che combina i riquadri che visualizzano un grafico delle metriche, una tabella di log attività, un diagramma utilizzo da Application Insights e l'output di una query di log.

![Lo screenshot mostra un dashboard di Azure, che include i riquadri dell'applicazione e della sicurezza, insieme ad altre informazioni personalizzabili.](media/overview/dashboard.png)

### <a name="workbooks"></a>Workbooks
Le [cartelle di lavoro](visualize/workbooks-overview.md) offrono un'area flessibile per l'analisi dei dati e la creazione di report visivi avanzati nel portale di Azure. Consentono di sfruttare più origini dati in tutto Azure e di combinarle in esperienze interattive unificate. Usare le cartelle di lavoro disponibili in Insights per creare modelli personalizzati da quelli predefiniti.


![Esempio di cartella di lavoro](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) è un servizio di analisi aziendale che fornisce visualizzazioni interattive per un'ampia varietà di origini dati. Si tratta di uno strumento efficace per rendere disponibili i dati ad altre persone interne ed esterne all'organizzazione. È possibile configurare per Power BI per [importare automaticamente i dati di log da Monitoraggio di Azure](./visualize/powerbi.md) per sfruttare i vantaggi di queste visualizzazioni aggiuntive.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrare ed esportare i dati
Spesso si ha l'esigenza di integrare Monitoraggio di Azure con altri sistemi e compilare soluzioni personalizzate che utilizzano i dati di monitoraggio. Altri servizi di Azure funzionano con Monitoraggio di Azure per fornire questa integrazione.

### <a name="event-hub"></a>Hub eventi
[Hub eventi di Azure](../event-hubs/index.yml) è una piattaforma di streaming e un servizio di inserimento di eventi. Consente di trasformare e archiviare i dati usando qualsiasi provider di analisi in tempo reale o adapter di archiviazione/batch. Usare Hub eventi per [lo streaming dei dati di Monitoraggio di Azure](essentials/stream-monitoring-data-event-hubs.md) a strumenti di monitoraggio e informazioni di sicurezza e gestione degli eventi partner.


### <a name="logic-apps"></a>App per la logica
[App per la logica](https://azure.microsoft.com/services/logic-apps) è un servizio che consente di automatizzare le attività e i processi aziendali tramite flussi di lavoro che si integrano con diversi sistemi e servizi. Le attività disponibili consentono di leggere e scrivere metriche e log in Monitoraggio di Azure. È quindi possibile creare flussi di lavoro che si integrano con un'ampia varietà di altri sistemi.


### <a name="api"></a>API
Sono disponibili varie API di lettura e scrittura di metriche e log in e da Monitoraggio di Azure oltre all'accesso agli avvisi generati. È anche possibile configurare e recuperare gli avvisi. Ciò offre possibilità praticamente illimitate per la compilazione di soluzioni personalizzate integrate con Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Metriche e log](/data-platform.md) per i dati raccolti da Monitoraggio di Azure.
* [Origini dati](agents/data-sources.md) per come i diversi componenti dell'applicazione inviano i dati di telemetria.
* [Query di log](logs/log-query-overview.md) per l'analisi dei dati raccolti.
* [Procedure consigliate](/azure/architecture/best-practices/monitoring) per il monitoraggio di applicazioni e servizi cloud.
