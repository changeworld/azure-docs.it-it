---
title: Monitoraggio di Azure per i clienti esistenti di Operations Manager
description: Linee guida per gli utenti esistenti di Operations Manager per la transizione del monitoraggio di determinati carichi di lavoro a monitoraggio di Azure nell'ambito di una transizione al cloud.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 85172e2430a3e65edb0c5ec119c920e2c7d20217
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234855"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Monitoraggio di Azure per i clienti esistenti di Operations Manager
Questo articolo fornisce indicazioni per i clienti che attualmente usano [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) e pianificano una transizione a [monitoraggio di Azure](overview.md) durante la migrazione di applicazioni aziendali e altre risorse in Azure. Si presuppone che l'obiettivo finale sia una transizione completa nel cloud, sostituendo il maggior Operations Manager possibile le funzionalità con monitoraggio di Azure, senza compromettere i requisiti operativi aziendali e IT. 

Le raccomandazioni specifiche riportate in questo articolo cambieranno come monitoraggio di Azure e Operations Manager aggiungono funzionalità. La strategia fondamentale, tuttavia, rimarrà coerente.

> [!IMPORTANT]
> Per l'implementazione di diverse funzionalità di monitoraggio di Azure descritte di seguito, è necessario valutare il relativo valore prima della distribuzione nell'intero ambiente.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente usi già [Operations Manager](https://docs.microsoft.com/system-center/scom) e abbia almeno una conoscenza di base di [monitoraggio di Azure](overview.md). Per un confronto completo tra i due, vedere [Guida al monitoraggio cloud: Panoramica delle piattaforme di monitoraggio](/azure/cloud-adoption-framework/manage/monitor/platform-overview). In questo articolo vengono illustrate le differenze di funzionalità specifiche tra i due per comprendere alcune delle raccomandazioni fornite qui. 


## <a name="general-strategy"></a>Strategia generale
Non sono disponibili strumenti di migrazione per convertire asset da Operations Manager a monitoraggio di Azure poiché le piattaforme sono fondamentalmente differenti. La migrazione costituirà invece un' [implementazione di monitoraggio di Azure standard](deploy.md) mentre si continua a usare Operations Manager. Quando si Personalizza monitoraggio di Azure per soddisfare i requisiti per applicazioni e componenti diversi e si ottengono più funzionalità, è possibile iniziare a ritirare diversi Management Pack e agenti in Operations Manager.

La strategia generale consigliata in questo articolo è identica a quella di [Cloud Monitoring Guide](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/), che consiglia una strategia di [monitoraggio cloud ibrido](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) che consente di eseguire una transizione graduale al cloud. Sebbene alcune funzionalità possano sovrapporsi, questa strategia consentirà di mantenere i processi aziendali esistenti Man mano che si acquisisce familiarità con la nuova piattaforma. Allontanarsi da Operations Manager funzionalità perché è possibile sostituirla con monitoraggio di Azure. L'uso di più strumenti di monitoraggio consente di aggiungere complessità, ma consente di sfruttare i vantaggi della capacità di monitoraggio di Azure di monitorare i carichi di lavoro cloud di nuova generazione, mantenendo al tempo stesso la capacità Operations Manager di monitorare i componenti del software e dell'infrastruttura del server che possono essere in locale o in altri cloud. 


## <a name="components-to-monitor"></a>Componenti da monitorare
Consente di suddividere in categorie i diversi tipi di carichi di lavoro che è necessario monitorare per determinare una strategia di monitoraggio distinta. [Guida al monitoraggio del cloud: formulare una strategia di monitoraggio](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) fornisce una suddivisione dettagliata dei diversi livelli dell'ambiente che necessitano di monitoraggio quando si avanza da applicazioni aziendali legacy a applicazioni moderne nel cloud.

Prima del cloud è stato usato Operations Manager per monitorare tutti i livelli. Quando si avvia la transizione con l'infrastruttura distribuita come servizio (IaaS), si continua a usare Operations Manager per le macchine virtuali, ma si inizia a usare monitoraggio di Azure per le risorse cloud. Quando si esegue ulteriormente la transizione alle applicazioni moderne usando la piattaforma distribuita come servizio (PaaS), è possibile concentrarsi maggiormente sul monitoraggio di Azure e iniziare a ritirare Operations Manager funzionalità.


![Modelli cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Questi livelli possono essere semplificati nelle categorie seguenti, descritti in modo più dettagliato nella parte restante di questo articolo. Sebbene tutti i carichi di lavoro di monitoraggio nell'ambiente in uso non rientrino in una di queste categorie, ognuno deve essere sufficientemente vicino a una determinata categoria in modo da applicare le indicazioni generali.

**Applicazioni aziendali.** Applicazioni che forniscono funzionalità specifiche per l'azienda. Possono essere interne o esterne e spesso sviluppate internamente tramite codice personalizzato. Le applicazioni legacy vengono in genere ospitate in computer fisici o virtuali che eseguono Windows o Linux, mentre le applicazioni più recenti saranno basate sui servizi delle applicazioni in Azure, ad esempio app Web di Azure e funzioni di Azure.

**Servizi di Azure.** Risorse in Azure che supportano le applicazioni aziendali migrate nel cloud. Sono inclusi servizi quali archiviazione di Azure, Azure SQL e Azure. Sono incluse anche le macchine virtuali di Azure poiché vengono monitorate come altri servizi di Azure, ma le applicazioni e il software in esecuzione nel sistema operativo guest di tali macchine virtuali richiedono un maggiore monitoraggio oltre l'host.

**Software server.** Il software in esecuzione in computer fisici e virtuali che supportano le applicazioni aziendali o le applicazioni in pacchetto che forniscono funzionalità generali alla propria azienda. Gli esempi includono Internet Information Server (IIS), SQL Server, Exchange e SharePoint. Questo include anche il sistema operativo Windows o Linux nei computer virtuali e fisici.

**Infrastruttura locale.** Componenti specifici dell'ambiente locale che richiedono il monitoraggio. Sono incluse risorse quali server fisici, archiviazione e componenti di rete. Questi sono i componenti virtualizzati quando si passa al cloud.

## <a name="sample-walkthrough"></a>Procedura dettagliata di esempio
Di seguito è riportata una procedura dettagliata ipotetica di una migrazione da Operations Manager a monitoraggio di Azure. Questa operazione non è destinata a rappresentare la complessità completa di una migrazione effettiva, ma almeno fornisce i passaggi e la sequenza di base. Le sezioni seguenti descrivono ognuno di questi passaggi in modo più dettagliato.

L'ambiente prima di trasferire i componenti in Azure si basa su macchine virtuali e fisiche situate in locale o con un provider di servizi gestiti. Si basa su Operations Manager per monitorare le applicazioni aziendali, il software server e altri componenti dell'infrastruttura nell'ambiente, ad esempio i server fisici e le reti. Si utilizzano Management Pack standard per il software server, ad esempio IIS, SQL Server e vari software del fornitore, e si ottimizzano tali Management Pack in base ai requisiti specifici. È possibile creare Management Pack personalizzati per le applicazioni aziendali e altri componenti che non possono essere monitorati con i Management Pack esistenti e configurare Operations Manager per supportare i processi aziendali.

La migrazione ad Azure inizia con IaaS, spostando le macchine virtuali che supportano le applicazioni aziendali in Azure. I requisiti di monitoraggio per queste applicazioni e il software server da cui dipendono non cambiano e si continua a utilizzare Operations Manager su questi server con i Management Pack esistenti. 

Monitoraggio di Azure è abilitato per i servizi di Azure non appena si crea una sottoscrizione di Azure. Raccoglie automaticamente le metriche della piattaforma e il log attività e configura i log delle risorse da raccogliere per poter analizzare in modo interattivo tutti i dati di telemetria disponibili usando le query di log. È possibile abilitare Monitoraggio di Azure per le macchine virtuali sulle macchine virtuali per analizzare i dati di monitoraggio nell'intero ambiente insieme e per individuare le relazioni tra computer e processi. È possibile estendere l'uso di monitoraggio di Azure alle macchine virtuali e fisiche locali abilitando i server abilitati per Azure Arc. 

Per ogni applicazione aziendale si Abilita Application Insights. Identifica i diversi componenti di ogni applicazione, inizia a raccogliere i dati di utilizzo e delle prestazioni e identifica gli eventuali errori che si verificano nel codice. È possibile creare test di disponibilità per testare in modo proattivo le applicazioni esterne e segnalare eventuali problemi di prestazioni o disponibilità. Sebbene Application Insights disponga di potenti funzionalità che non sono disponibili in Operations Manager, è possibile continuare a utilizzare i Management Pack personalizzati sviluppati per le applicazioni aziendali poiché includono scenari di monitoraggio non ancora coperti da monitoraggio di Azure. 

Man mano che si acquisisce familiarità con monitoraggio di Azure, si inizia a creare regole di avviso in grado di sostituire alcune funzionalità di Management Pack e iniziare a sviluppare i processi aziendali per l'uso della nuova piattaforma di monitoraggio. In questo modo è possibile iniziare a rimuovere computer e Management Pack dal gruppo di gestione Operations Manager. Si continuerà a utilizzare i Management Pack per il software server critico e l'infrastruttura locale, ma si continuerà a osservare le nuove funzionalità di monitoraggio di Azure che consentono di ritirare le funzionalità aggiuntive.

## <a name="monitor-azure-services"></a>Monitorare i servizi di Azure
I servizi di Azure richiedono effettivamente monitoraggio di Azure per raccogliere i dati di telemetria e sono abilitati al momento della creazione di una sottoscrizione di Azure. Il [log attività](platform/activity-log.md) viene raccolto automaticamente per la sottoscrizione e le [metriche della piattaforma](platform/data-platform-metrics.md) vengono raccolte automaticamente da tutte le risorse di Azure create. È possibile iniziare immediatamente a usare [Esplora metriche](platform/metrics-getting-started.md), che è simile alle visualizzazioni prestazioni nella console operatore, ma fornisce analisi interattive e [aggregazioni avanzate](platform/metrics-charts.md) di dati. [Creare un avviso di metrica](platform/alerts-metric.md) per ricevere una notifica quando un valore supera una soglia o [aggiungere un grafico a un dashboard di Azure](platform/metrics-charts.md#pin-charts-to-dashboards) per la visibilità.

[![Esplora metriche](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Creare un'impostazione di diagnostica](platform/diagnostic-settings.md) per ogni risorsa di Azure per inviare le metriche e i [log delle risorse](platform/resource-logs.md), che forniscono informazioni dettagliate sul funzionamento interno di ogni risorsa, a un'area di lavoro log Analytics. Ciò offre tutti i dati di telemetria disponibili per le risorse e consente di usare [log Analytics](log-query/log-analytics-overview.md) per analizzare in modo interattivo i dati di log e delle prestazioni usando un linguaggio di query avanzato che non ha un equivalente in Operations Manager. È anche possibile creare [avvisi di query di log](platform/alerts-log-query.md), che possono usare la logica complessa per determinare le condizioni di avviso e correlare i dati tra più risorse.

[![Analisi dei log](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Le [informazioni dettagliate](monitor-reference.md) in monitoraggio di Azure sono simili ai Management Pack in quanto forniscono un monitoraggio univoco per un servizio di Azure specifico. Le informazioni dettagliate sono attualmente disponibili per diversi servizi, tra cui la rete, l'archiviazione e i contenitori, e altri vengono continuamente aggiunti.

[![Esempio di analisi](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Le informazioni dettagliate sono basate sulle [cartelle di lavoro](platform/workbooks-overview.md) di monitoraggio di Azure, che combinano le metriche e le query di log in report interattivi avanzati. Creare cartelle di lavoro personalizzate per combinare i dati di più servizi in modo analogo a come è possibile creare visualizzazioni e report personalizzati nella console operatore.

### <a name="azure-management-pack"></a>Azure Management Pack
Il [Management Pack di Azure](https://www.microsoft.com/download/details.aspx?id=50013) consente Operations Manager di individuare le risorse di Azure e monitorarne l'integrità in base a un particolare set di scenari di monitoraggio. Questo Management Pack richiede di eseguire una configurazione aggiuntiva per ogni risorsa in Azure, ma può essere utile fornire una certa visibilità delle risorse di Azure nella console operatore fino a quando non si sviluppano i processi aziendali per concentrarsi su monitoraggio di Azure.

[![Azure Management Pack](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 È possibile scegliere di usare il Management Pack di Azure se si vuole ottenere visibilità per alcune risorse di Azure nella console operatore e integrare alcuni avvisi di base con i processi esistenti. USA effettivamente i dati raccolti da monitoraggio di Azure. È consigliabile esaminare monitoraggio di Azure per il monitoraggio completo a lungo termine delle risorse di Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Monitorare il software server e l'infrastruttura locale
Quando si spostano i computer nel cloud, i requisiti di monitoraggio per il software non cambiano. Non è più necessario monitorare i componenti fisici perché sono virtualizzati, ma il sistema operativo guest e i relativi carichi di lavoro hanno gli stessi requisiti indipendentemente dall'ambiente.

[Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md) è la funzionalità principale di monitoraggio di Azure per il monitoraggio delle macchine virtuali e dei relativi carichi di lavoro e del sistema operativo guest. Analogamente a Operations Manager, Monitoraggio di Azure per le macchine virtuali utilizza un agente per raccogliere dati dal sistema operativo guest delle macchine virtuali. Si tratta degli stessi dati delle prestazioni e degli eventi utilizzati in genere dai Management Pack per l'analisi e l'invio di avvisi. Tuttavia, non esistono regole preesistenti per identificare e segnalare i problemi per le applicazioni aziendali e il software server in esecuzione in tali computer. È necessario creare regole di avviso personalizzate per ricevere notifiche proattive di eventuali problemi rilevati.

[![Prestazioni Monitoraggio di Azure per le macchine virtuali](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Monitoraggio di Azure non misura inoltre lo stato di integrità di applicazioni e servizi diversi in esecuzione su una macchina virtuale. Gli avvisi delle metriche possono essere risolti automaticamente quando un valore scende al di sotto di una soglia, ma monitoraggio di Azure non è attualmente in grado di definire criteri di integrità per le applicazioni e i servizi in esecuzione nel computer, né il rollup dello stato per raggruppare l'integrità dei componenti correlati.

> [!NOTE]
> Una nuova [funzionalità di integrità Guest per monitoraggio di Azure per le macchine virtuali](insights/vminsights-health-overview.md) è ora disponibile in anteprima pubblica e invia un avviso in base allo stato di integrità di un set di metriche delle prestazioni. Inizialmente è limitato a un set specifico di contatori delle prestazioni correlati al sistema operativo guest e non alle applicazioni o ad altri carichi di lavoro in esecuzione nella macchina virtuale.
> 
> [![Monitoraggio di Azure per le macchine virtuali integrità Guest](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Il monitoraggio del software nei computer in un ambiente ibrido utilizzerà in genere una combinazione di Monitoraggio di Azure per le macchine virtuali e Operations Manager, in base ai requisiti di ogni computer e alla maturità che sviluppa processi operativi in monitoraggio di Azure. Microsoft Management Agent (noto come agente di Log Analytics in monitoraggio di Azure) viene usato da entrambe le piattaforme, in modo che sia possibile monitorare contemporaneamente un singolo computer.

> [!NOTE]
> In futuro, Monitoraggio di Azure per le macchine virtuali eseguirà la transizione all' [agente di monitoraggio di Azure](platform/azure-monitor-agent-overview.md), attualmente disponibile in anteprima pubblica. Sarà compatibile con il Microsoft Monitoring Agent in modo che la stessa macchina virtuale continui a essere monitorata da entrambe le piattaforme.

Continuare a usare Operations Manager per le funzionalità che non possono essere ancora fornite da monitoraggio di Azure. Sono inclusi i Management Pack per software server critico come IIS, SQL Server o Exchange. È anche possibile avere Management Pack personalizzati sviluppati per l'infrastruttura locale che non possono essere raggiunti con monitoraggio di Azure. Continuare anche a usare Operations Manager se è strettamente integrato nei processi operativi fino a quando non è possibile passare alla modernizzazione delle operazioni di servizio in cui monitoraggio di Azure e altri servizi di Azure possono aumentare o sostituire. 

USA monitoraggio di Azure per le macchine virtuali per migliorare il monitoraggio corrente anche se non sostituisce immediatamente Operations Manager. Di seguito sono riportati alcuni esempi di funzionalità univoche di monitoraggio di Azure:

- Individuare e monitorare le relazioni tra le macchine virtuali e le relative dipendenze esterne.
- Visualizzazione dei dati sulle prestazioni aggregati tra più macchine virtuali in grafici interattivi e cartelle di lavoro.
- Usare le [query di log](log-query/log-query-overview.md) per analizzare in modo interattivo i dati di telemetria dalle macchine virtuali con i dati delle altre risorse di Azure.
- Creare [regole di avviso del log](platform/alerts-log-query.md) in base a una logica complessa tra più macchine virtuali.

[![Mappa Monitoraggio di Azure per le macchine virtuali](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Oltre alle macchine virtuali di Azure, Monitoraggio di Azure per le macchine virtuali possibile monitorare i computer in locale e in altri cloud usando i [server abilitati per Azure Arc](../azure-arc/servers/overview.md). I server abilitati per Arc consentono di gestire i computer Windows e Linux ospitati all'esterno di Azure, nella rete aziendale o in un altro provider di servizi cloud coerente con la modalità di gestione delle macchine virtuali di Azure native.



## <a name="monitor-business-applications"></a>Monitorare le applicazioni aziendali
In genere è necessario un Management Pack personalizzato per monitorare le applicazioni aziendali con Operations Manager, sfruttando gli agenti installati in ogni macchina virtuale. Application Insights in monitoraggio di Azure monitora le applicazioni basate sul Web, che si trovino in Azure, in altri cloud o in locale, quindi possono essere usate per tutte le applicazioni, indipendentemente dal fatto che siano state migrate in Azure.

Se il monitoraggio di un'applicazione aziendale è limitato alle funzionalità fornite dal [modello di prestazioni dell'app .NET]() in Operations Manager, è possibile eseguire la migrazione a Application Insights senza alcuna perdita di funzionalità. Application Insights includerà infatti un numero significativo di funzionalità aggiuntive, incluse le seguenti:

- Individuare e monitorare automaticamente i componenti dell'applicazione.
- Raccolta di dati dettagliati sull'utilizzo e sulle prestazioni delle applicazioni, ad esempio tempi di risposta, frequenze di errori e frequenza delle richieste.
- Raccogliere i dati del browser, ad esempio le visualizzazioni pagina e le prestazioni di caricamento.
- Rilevare le eccezioni ed esaminare l'analisi dello stack e le richieste correlate.
- Eseguire analisi avanzate utilizzando funzionalità quali la [traccia distribuita](app/distributed-tracing.md) e il [rilevamento intelligente](app/proactive-diagnostics.md).
- Usare [Esplora metriche](platform/metrics-getting-started.md) per analizzare in modo interattivo i dati sulle prestazioni.
- Usare [query di log](log-query/log-query-overview.md) per analizzare in modo interattivo i dati di telemetria raccolti insieme ai dati raccolti per i servizi di Azure e monitoraggio di Azure per le macchine virtuali.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Esistono tuttavia alcuni scenari in cui potrebbe essere necessario continuare a usare Operations Manager oltre a Application Insights fino a quando non si è in grado di ottenere le funzionalità necessarie. Esempi in cui potrebbe essere necessario continuare con Operations Manager includono quanto segue:

-  I [test di disponibilità](app/monitor-web-app-availability.md), che consentono di monitorare e avvisare la disponibilità e la velocità di risposta delle applicazioni, richiedono richieste in ingresso dagli indirizzi IP degli agenti di test Web. Se i criteri non consentono tale accesso, potrebbe essere necessario usare i [monitoraggi della disponibilità dell'applicazione Web](/system-center/scom/web-application-availability-monitoring-template) in Operations Manager.
- In Operations Manager è possibile impostare qualsiasi intervallo di polling per i test di disponibilità, con molti clienti che controllano ogni 60-120 secondi. Application Insights ha un intervallo di polling minimo di 5 minuti, che può essere troppo lungo per alcuni clienti.
- Una quantità significativa di monitoraggio in Operations Manager viene eseguita raccogliendo gli eventi generati dalle applicazioni e eseguendo gli script nell'agente locale. Queste opzioni non sono standard in Application Insights, quindi è possibile richiedere un lavoro personalizzato per soddisfare i requisiti aziendali. Questo potrebbe includere regole di avviso personalizzate usando i dati degli eventi archiviati in un'area di lavoro di Log Analytics e gli script avviati in un guest di macchine virtuali con il ruolo di [lavoro ibrido](../automation/automation-hybrid-runbook-worker.md)
- A seconda della lingua in cui è stata scritta l'applicazione, è possibile che l'utente sia limitato alla [strumentazione utilizzabile con Application Insights](app/platforms.md).

Seguendo la strategia di base nelle altre sezioni di questa guida, continuare a usare Operations Manager per le applicazioni aziendali, ma sfruttare le funzionalità aggiuntive offerte da Application Insights. Poiché si è in grado di sostituire le funzionalità critiche con monitoraggio di Azure, è possibile iniziare a ritirare i Management Pack personalizzati.


## <a name="next-steps"></a>Passaggi successivi

- Per un confronto dettagliato tra monitoraggio di Azure e System Center Operations Manager e altre informazioni sulla progettazione e l'implementazione di un ambiente di monitoraggio ibrido, vedere la [Guida al monitoraggio del cloud](/azure/cloud-adoption-framework/manage/monitor/) .
- Scopri di più sul [monitoraggio delle risorse di Azure in monitoraggio di Azure](insights/monitor-azure-resource.md).
- Scopri di più sul [monitoraggio di macchine virtuali di Azure in monitoraggio di Azure](insights/monitor-vm-azure.md).
- Scopri di più su [monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md).
- Scopri di più su [Application Insights](app/app-insights-overview.md).
