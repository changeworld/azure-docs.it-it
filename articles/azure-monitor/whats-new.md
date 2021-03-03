---
title: Novità della documentazione di Monitoraggio di Azure
description: Aggiornamenti importanti alla documentazione di Monitoraggio di Azure, che viene aggiornata ogni mese.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713440"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Novità della documentazione di Monitoraggio di Azure

Questo articolo elenca gli articoli su Monitoraggio di Azure nuovi o aggiornati in modo significativo. L'articolo verrà aggiornato la prima settimana di ogni mese per includere gli aggiornamenti degli articoli del mese precedente.

## <a name="january-2021"></a>Gennaio 2021 

### <a name="general"></a>Generale 
- [Domande frequenti su monitoraggio di Azure](faq.md) : Aggiunta voce per informazioni sul dispositivo per Application Insights.
### <a name="agents"></a>Agenti  
- [Raccolta di eventi di Event Tracing for Windows (ETW) per i log di monitoraggio di Azure di Analysis](./agents/data-sources-event-tracing-windows.md) : nuovo articolo.
- [Regole di raccolta dati in monitoraggio di Azure (anteprima)](./agents/data-collection-rule-overview.md) : sono stati aggiunti collegamenti a esempi di PowerShell e cli.

### <a name="alerts"></a>Avvisi  
- [Configurare Azure per connettere gli strumenti ITSM usando l'esportazione sicura](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) -nuovo articolo.
- [Errori di stato del connettore nel dashboard di connettore](./alerts/itsmc-dashboard-errors.md) -nuovo articolo.
- [Esaminare gli errori usando il dashboard di connettore](./alerts/itsmc-dashboard.md) -nuovo articolo.
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](./alerts/alerts-troubleshoot-metric.md) -sezioni aggiunte per le soglie dinamiche.
- [Risolvere i problemi in it Service Management Connector](./alerts/itsmc-troubleshoot-overview.md) -nuovo articolo.

### <a name="application-insights"></a>Application Insights
- [Applicazione Azure informazioni dettagliate](app/correlation.md) sulla correlazione di dati di telemetria-aggiunta della traccia quando un modulo ne chiama un altro in OpenCensus Python.
- [Application Insights per le pagine Web](app/javascript.md) -nuovo articolo.
- [Fare clic su Analytics auto-Collection plugin per Application Insights JavaScript SDK](app/javascript-click-analytics-plugin.md) -nuovo articolo.
- [Monitora le tue app senza modifiche al codice-strumentazione automatica per monitoraggio di Azure Application Insights](app/codeless-overview.md) colonna Python aggiunta.
- [Plug-in React per Application Insights JavaScript SDK](app/javascript-react-plugin.md) - Nuovo articolo.
- [Processori di telemetria (anteprima): Application Insights di monitoraggio di Azure per Java](app/java-standalone-telemetry-processors.md) -riscritto.
- [Analisi dell'utilizzo con applicazione Azure Insights](app/usage-overview.md) -nuovo articolo.
- [Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per individuare problemi relativi all'app Web](app/change-analysis.md) . errore aggiunto messges.


### <a name="insights"></a>Informazioni dettagliate    
- [Monitoraggio di Azure per Esplora dati di Azure (anteprima)](insights/data-explorer.md) -nuovo articolo.

### <a name="logs"></a>Log    
- [Chiave gestita dal cliente di monitoraggio di Azure](./logs/customer-managed-keys.md) : introduce l'identità gestita assegnata dall'utente.
- [Monitoraggio di Azure registra i cluster dedicati](./logs/logs-dedicated-clusters.md) : codice di risposta aggiornato.
- [Query tra servizi-monitoraggio di Azure e azure Esplora dati (anteprima)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) -nuovo articolo.

### <a name="metrics"></a>Metriche
- [Aggregazione delle metriche di monitoraggio di Azure e visualizzazione illustrata](./essentials/metrics-aggregation-explained.md) -nuovo articolo.

### <a name="platform-logs"></a>Log della piattaforma
- [Servizi e categorie supportati nei log delle risorse di monitoraggio di Azure](./essentials/resource-logs-categories.md) -nuovo articolo.

### <a name="visualizations"></a>Visualizzazioni
- [Origini dati delle cartelle di lavoro di monitoraggio di Azure](./visualize/workbooks-data-sources.md) : aggiunta di merge e analisi delle modifiche.


## <a name="december-2020"></a>Dicembre 2020

### <a name="general"></a>Generale
- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiunti i messaggi di errore.
- [Partner che si integrano con Monitoraggio di Azure](partners.md) - Aggiunta una sezione sull'integrazione di hub eventi.

### <a name="agents"></a>Agenti
- [Eseguire query tra risorse in Esplora dati di Azure con Monitoraggio di Azure](logs/azure-monitor-data-explorer-proxy.md) - Nuovo articolo.
- [Panoramica degli agenti di monitoraggio di Azure](agents/agents-overview.md) - Aggiunto il supporto per Oracle 8.

### <a name="alerts"></a>Avvisi
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](alerts/alerts-troubleshoot-metric.md) - Aggiunta la risoluzione dei problemi relativi a soglie dinamiche.
- [Connettore di Gestione dei servizi IT in Log Analytics](alerts/itsmc-definition.md) - Nuovo articolo.
- [Panoramica di Connettore di Gestione dei servizi IT](alerts/itsmc-overview.md) - Ristrutturate le informazioni sulla risoluzione dei problemi.
- [Connettere Cherwell con Connettore di Gestione dei servizi IT in Azure](alerts/itsmc-connections-cherwell.md) - Nuovo articolo.
- [Connettere Provance con Connettore di Gestione dei servizi IT in Azure](alerts/itsmc-connections-provance.md) - Nuovo articolo.
- [Connettere SCSM con Connettore di Gestione dei servizi IT in Azure](alerts/itsmc-connections-scsm.md) - Nuovo articolo.
- [Connettere ServiceNow con Connettore di Gestione dei servizi IT in Azure](alerts/itsmc-connections-servicenow.md) - Nuovo articolo.
- [Come correggere manualmente i problemi di sincronizzazione di ServiceNow](alerts/itsmc-resync-servicenow.md) - Ristrutturate le informazioni sulla risoluzione dei problemi.




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights per app Web JavaScript](app/javascript.md) - Aggiunta la configurazione delle stringe di connessione.
- [Metriche standard di Azure Application Insights](app/standard-metrics.md) - Nuovo articolo.
- [Application Insights di Monitoraggio di Azure per Java](app/java-in-process-agent.md) - Aggiunte informazioni sull'invio di dati di telemetria personalizzati dall'applicazione.
- [Esportazione continua dei dati di telemetria da Application Insights](app/export-telemetry.md) - Aggiunte informazioni sull'esportazione basata su impostazioni di diagnostica.
- [Abilitare Snapshot Debugger per le app .NET e .NET Core in Funzioni di Azure](app/snapshot-debugger-function-app.md) - Nuovo articolo.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiunti gli indirizzi IP per Azure per enti pubblici.
- [Risolvere i problemi relativi ad Azure Application Insights Profiler](app/profiler-troubleshooting.md) - Aggiunte informazioni sulla pagina di stato dell'estensione del sito Servizi di diagnostica.
- [Risolvere i problemi relativi ai test di disponibilità di Azure Application Insights](app/troubleshoot-availability.md) - Aggiornamenti della risoluzione dei problemi per i test ping.
- [Risoluzione dei problemi relativi ad Application Insights di Monitoraggio di Azure per Java](app/java-standalone-troubleshoot.md) - Nuovo articolo.

### <a name="containers"></a>Contenitori
- [Report in informazioni dettagliate sul contenitore](insights/container-insights-reports.md) -nuovo articolo.

### <a name="logs"></a>Log
- [Cluster dedicati nei log di Monitoraggio di Azure](logs/logs-dedicated-clusters.md) - Aggiunti comandi automatizzati, metodi per annullare e rimuovere i collegamenti e informazioni sulla risoluzione dei problemi.
- [Query tra i servizi Monitoraggio di Azure e Esplora dati di Azure (anteprima)](logs/azure-data-explorer-monitor-cross-service-query.md) - Nuovo articolo.
- [Esportazione dei dati dell'area di lavoro Log Analytics in Monitoraggio di Azure (anteprima)](logs/logs-data-export.md) - Aggiunti i modelli di Azure Resource Manager.

### <a name="metrics"></a>Metriche
- [Funzionalità avanzate di Esplora metriche di Azure](essentials/metrics-charts.md) - Aggiunte informazioni sulla selezione dell'ambito delle risorse.
- [Visualizzazione di più risorse in Esplora metriche](essentials/metrics-dynamic-scope.md) - Nuovo articolo.

### <a name="networks"></a>Reti
- [Soluzione Azure Network Analytics in Monitoraggio di Azure](insights/azure-networking-analytics.md) - Aggiunte informazioni sulla cartella di lavoro Informazioni dettagliate rete.

### <a name="virtual-machines"></a>Macchine virtuali
- [Abilitare Monitoraggio di Azure per un ambiente ibrido](vm/vminsights-enable-hybrid.md) - Nuova versione dell'agente di dipendenza.


### <a name="visualizations"></a>Visualizzazioni
- [Visualizzazioni mappa delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-map-visualizations.md) - Nuovo articolo.
- [Funzionalità BYOS (Bring Your Own Storage) nelle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-bring-your-own-storage.md) - Nuovo articolo.


## <a name="november-2020"></a>Novembre 2020

### <a name="general"></a>Generale
- [Limiti del servizio Monitoraggio di Azure](service-limits.md) - Aggiornato per il supporto di Azure Arc.

### <a name="agents"></a>Agenti
- [Panoramica degli agenti di monitoraggio di Azure](agents/agents-overview.md) - Aggiornato per il supporto di Azure Arc.
- [Installare l'gente di Monitoraggio di Azure](agents/azure-monitor-agent-install.md) - Nuovo articolo.
- [Panoramica dell'agente di Monitoraggio di Azure](agents/azure-monitor-agent-overview.md) - Aggiornato per il supporto di Azure Arc.
- [Esempi di modelli di Resource Manager per agenti](agents/resource-manager-agent.md) - Aggiornato per il supporto di Azure Arc.

### <a name="alerts"></a>Avvisi
- [Creare e gestire gruppi di azioni nel portale di Azure](alerts/action-groups.md) - Aggiunti gli indirizzi IP di origine per i webhook.

### <a name="application-insights"></a>Application Insights
- [Monitoraggio di applicazioni codeless Java con Application Insights di Monitoraggio di Azure](app/java-in-process-agent.md) - Aggiunto un esempio di configurazione.
- [Plug-in React per Application Insights JavaScript SDK](app/javascript-react-plugin.md) - Aggiunta una sezione sull'uso degli hook React.
- [Aggiornamento da Application Insights Java 2.x SDK](app/java-standalone-upgrade-from-2x.md) - Nuovo articolo.
- [Note sulla versione per Microsoft.ApplicationInsights.SnapshotCollector](app/snapshot-collector-release-notes.md) - Nuovo articolo.

### <a name="autoscale"></a>Autoscale
- [Introduzione alla scalabilità automatica in Azure](autoscale/autoscale-get-started.md) - Aggiunta una sezione sullo spostamento della funzionalità di scalabilità automatica in un'altra area.

### <a name="data-collection"></a>Raccolta dati
- [Configurare la raccolta dati per l'agente di Monitoraggio di Azure (anteprima)](agents/data-collection-rule-azure-monitor-agent.md) - Aggiornato per il supporto di Azure Arc.
- [Regole di raccolta dati in Monitoraggio di Azure (anteprima)](agents/data-collection-rule-overview.md) - Aggiornato per il supporto di Azure Arc.
- [Esempi di modelli di Resource Manager per le regole di raccolta dati](agents/resource-manager-data-collection-rules.md) - Nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni
- [Connettere Azure agli strumenti di gestione dei servizi IT tramite l'esportazione sicura](alerts/it-service-management-connector-secure-webhook-connections.md) - Aggiunta la sezione sulla connessione a ServiceNow.

### <a name="logs"></a>Log
- [Integrare Log Analytics ed Excel](logs/log-excel.md) - Nuovo articolo.
- [Sicurezza dei dati di Log Analytics](logs/data-security.md) - Aggiunta la sezione sulle funzionalità di sicurezza aggiuntive.
- [Integrazione di Log Analytics con Power BI](logs/log-powerbi.md) - Nuovo articolo.
- [Colonne standard nei record dei log di Monitoraggio di Azure](logs/log-standard-columns.md) - Aggiunta la colonna _SubscriptionId.

Articoli nuovi e aggiornati dopo la ristrutturazione del contenuto sulle query di log.

- [Esercitazione su Log Analytics](logs/log-analytics-tutorial.md)
- [Query di log in Monitoraggio di Azure](logs/log-query-overview.md)
- [Panoramica di Log Analytics in Monitoraggio di Azure](logs/log-analytics-overview.md)
- [Esempi di query di Esplora dati di Azure e Monitoraggio di Azure](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Esercitazione: Usare le query Kusto in Esplora dati di Azure e Monitoraggio di Azure](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Macchine virtuali

- [Abilitare la Panoramica di VM Insights](vm/vminsights-enable-overview.md) : sono state aggiunte aree supportate.

Nuovi articoli per l'integrità Guest di VM Insights (anteprima)

- [Integrità Guest di VM Insights (anteprima)](vm/vminsights-health-overview.md)
- [Avvisi di integrità Guest di VM Insights (anteprima)](vm/vminsights-health-alerts.md)
- [Configurare il monitoraggio in integrità Guest di VM Insights (anteprima)](vm/vminsights-health-configure.md)
- [Configurare il monitoraggio in integrità Guest di VM Insights usando le regole di raccolta dati (anteprima)](vm/vminsights-health-configure-dcr.md)
- [Abilitare l'integrità Guest di VM Insights (anteprima)](vm/vminsights-health-enable.md)
- [Risolvere i problemi di integrità Guest di VM Insights (anteprima)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Ottobre 2020

### <a name="general"></a>Generale
- [Ritiro dell'API di Monitoraggio di Azure](logs/operationalinsights-api-retirement.md) - Nuovo articolo.

### <a name="agents"></a>Agenti
- [Che cosa viene monitorato da Monitoraggio di Azure](monitor-reference.md) - Aggiunta la sezione sugli agenti.

### <a name="alerts"></a>Avvisi
- [Creare e gestire gruppi di azione nel portale di Azure](alerts/action-groups.md) - Aggiunta la sezione sui tag del servizio.
- [Esempi di modelli di Resource Manager per gli avvisi delle metriche](alerts/resource-manager-alerts-metric.md) - Aggiunti il parametro di corrispondenza del contenuto e i percorsi di test.
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](alerts/alerts-troubleshoot-metric.md) - Aggiunta la procedura consigliata per la configurazione delle regole.

### <a name="application-insights"></a>Application Insights
- [Plug-in Angular per Application Insights JavaScript SDK](app/javascript-angular-plugin.md) - Nuovo articolo.
- [Azure Application Insights per applicazioni ASP.NET Core](app/asp-net-core.md) - Aggiunta la sezione delle domande frequenti sui log di ILogger.
- [Configurare il monitoraggio per ASP.NET con Azure Application Insights](app/asp-net.md) - Articolo riscritto.
- [Metriche basate su log e metriche preaggregate in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) - Aggiunte le tabelle con le metriche preaggregate.
- [Monitorare la disponibilità e la velocità di risposta dei siti Web](app/monitor-web-app-availability.md) - Aggiunta la sezione sui tag di popolamento dei percorsi.
- [Monitorare le applicazioni Java ovunque - Application Insights per Monitoraggio di Azure](app/java-standalone-config.md) - Aggiunto l'esempio di configurazione.
- [Monitorare le applicazioni Java ovunque - Application Insights per Monitoraggio di Azure](app/java-standalone-telemetry-processors.md) - Nuovo articolo.
- [Usare Analisi delle modifiche alle applicazioni in Monitoraggio di Azure per trovare i problemi delle app Web](app/change-analysis.md) - Aggiunte le sezioni relative a macchine virtuali e log attività.
  
### <a name="autoscale"></a>Autoscale
- [Introduzione alla scalabilità automatica in Azure](autoscale/autoscale-get-started.md) - Aggiunta la sezione sullo spostamento di Scalabilità automatica in un'altra area.

### <a name="containers"></a>Contenitori
- [Configurare il monitoraggio PV con container Insights](containers/container-insights-persistent-volumes.md) -nuovo articolo.
- [Come gestire il supporto aggiunto dall'agente di container Insights per il](containers/container-insights-manage-agent.md) cluster Kubernetes abilitato per Azure Arc.
- [Avvisi relativi alle metriche di container Insights](containers/container-insights-metric-alerts.md) : è stato aggiunto il supporto per il cluster Kubernetes abilitato per Azure Arc.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni
- [Connettore di Gestione dei servizi IT - Esportazione sicura in Monitoraggio di Azure](alerts/it-service-management-connector-secure-webhook-connections.md) - Aggiunta la sezione su ServiceNow.

### <a name="logs"></a>Log
- [Archiviare dati dall'area di lavoro Log Analytics nell'archiviazione di Azure con App per la logica](logs/logs-export-logic-app.md) - Nuovo articolo.
- [Esportazione dei dati dell'area di lavoro Log Analytics in Monitoraggio di Azure (anteprima)](logs/logs-data-export.md) - Aggiunto il corpo dell'esempio per la richiesta REST relativa all'hub eventi.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Aggiunte le informazioni sulla relazione tra log di Monitoraggio di Azure e fatturazione di Centro sicurezza di Azure. Aggiunta la query per i conteggi dei nodi in caso di utilizzo del piano tariffario Per nodo. 
- [Monitorare l'integrità dell'area di lavoro Log Analytics in Monitoraggio di Azure](logs/monitor-workspace.md) - Nuovo articolo.
- [Eseguire query sui dati in Monitoraggio di Azure con Esplora dati di Azure (anteprima)](logs/azure-data-explorer-monitor-proxy.md) - Nuovo articolo.
- [Eseguire query sui dati esportati da Monitoraggio di Azure con Esplora dati di Azure (anteprima)](logs/azure-data-explorer-query-storage.md) - Nuovo articolo.

### <a name="networks"></a>Reti
- [Monitoraggio di Azure per reti (anteprima)](insights/network-insights-overview.md) - Aggiunta la sezione relativa alla risoluzione dei problemi. Aggiunta la sezione relativa alla connettività.

### <a name="platform-logs"></a>Log della piattaforma
- [Schema degli eventi del log attività di Azure](essentials/activity-log-schema.md) - Aggiunta la descrizione dei livelli di gravità.

### <a name="virtual-machines"></a>Macchine virtuali
- [Analisi delle modifiche in Monitoraggio di Azure per le macchine virtuali](vm/vminsights-change-analysis.md) - Nuovo articolo.
- [Abilitare Monitoraggio di Azure per le macchine virtuali](vm/vminsights-enable-overview.md) - Aggiunte le aree supportate.
- [Come aggiornare il contenitore Insights per le metriche](containers/container-insights-update-metrics.md) : è stato aggiunto il supporto per il cluster Kubernetes abilitato per Azure Arc.



## <a name="september-2020"></a>Settembre 2020

### <a name="general"></a>Generale
- [Domande frequenti su Monitoraggio di Azure](faq.md) - Aggiunta la sezione su OpenTelemetry.

### <a name="agents"></a>Agenti
- [Panoramica dell'agente di Monitoraggio di Azure](agents/azure-monitor-agent-overview.md) - Aggiunti i fattori decisionali per passare al nuovo agente.
- [Panoramica degli agenti di monitoraggio di Azure](agents/agents-overview.md) - Aggiunto il supporto per Windows 10.

### <a name="alerts"></a>Avvisi
- [Creare un avviso del log con un modello di Azure Resource Manager](alerts/alerts-log-create-templates.md) - Nuovo articolo.
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](alerts/alerts-troubleshoot-metric.md) - Aggiunta una sezione sull'esportazione di un modello di Resource Manager per una regola di avviso delle metriche.

### <a name="application-insights"></a>Application Insights
- [Creare una nuova risorsa di Application Insights basata sull'area di lavoro in Monitoraggio di Azure](app/create-workspace-resource.md) - Rimossa la designazione di anteprima.
- [Conservazione e archiviazione dei dati in Azure Application Insights](app/data-retention-privacy.md) - Aggiunti i dettagli per il nuovo supporto per la protezione dalla perdita dei dati in Mac e Linux.
- [Contatori di eventi in Application Insights](app/eventcounters.md) - Aggiunta una nota sui contatori raccolti per impostazione predefinita.
- [Metriche basate su log e metriche preaggregate in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) - Rimossa la designazione di anteprima.
- [Eseguire la migrazione di una risorsa classica di Application Insights in Monitoraggio di Azure a una risorsa basata su area di lavoro](app/convert-classic-resource.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in qualsiasi ambiente: Application Insights in Monitoraggio di Azure](app/java-in-process-agent.md) - Articolo aggiornato per includere la nuova versione di anteprima dell'agente.
- [Configurare l'analisi di app Web per ASP.NET con Azure Application Insights](app/asp-net.md) - Articolo riscritto.
- [Canali di telemetria in Azure Application Insights](app/telemetry-channels.md) - Aggiunti i dettagli per il nuovo supporto per la protezione dalla perdita dei dati in Mac e Linux.
- [Risolvere i problemi relativi a Snapshot Debugger in Azure Application Insights](app/snapshot-debugger-troubleshoot.md) - Aggiunta la sezione su SSL nella risoluzione dei problemi di Snapshot Debugger.
- [Usare Analisi delle modifiche alle applicazioni in Monitoraggio di Azure per trovare i problemi delle app Web](app/change-analysis.md) - Aggiunte le sezioni relative a macchine virtuali e log attività.


### <a name="containers"></a>Contenitori
- [Configurare il cluster Kubernetes abilitato per Azure Arc con](containers/container-insights-enable-arc-enabled-clusters.md) informazioni dettagliate sul contenitore-aggiunta di istruzioni per l'abilitazione del monitoraggio con l'entità servizio.
- [Distribuzione della metrica & hPa con informazioni dettagliate sul contenitore](containers/container-insights-deployment-hpa-metrics.md) -nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni
- [Monitoraggio di Azure per la cache di Azure per Redis](insights/redis-cache-insights-overview.md) - Rimossa la designazione di anteprima.
- [Monitoraggio di Azure per reti (anteprima)](insights/network-insights-overview.md) - Aggiunte le sezioni relative a connettività e traffico.
- [Connettore di Gestione dei servizi IT - Esportazione sicura in Monitoraggio di Azure](alerts/it-service-management-connector-secure-webhook-connections.md) - Nuovo articolo.
- [Connettore di Gestione dei servizi IT in Monitoraggio di Azure](alerts/itsmc-connections.md) - Nota sulle integrazioni di Cherwell e Provance ITSM.
- [Monitorare Key Vault con Monitoraggio di Azure per Key Vault](insights/key-vault-insights-overview.md) - Rimossa la designazione di anteprima.

### <a name="logs"></a>Log
- [Controllare le query sui log in Monitoraggio di Azure](logs/query-audit.md) - Nuovo articolo.
- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiunta la sezione relativa a Customer Lockbox.
- [Cluster dedicati per i log di Monitoraggio di Azure](logs/logs-dedicated-clusters.md) - Nuovo articolo.
- [Progettazione della distribuzione dei log di Monitoraggio di Azure](logs/design-logs-deployment.md) - Aggiornata la sezione sui limiti di velocità del volume di inserimento dati e scalabilità.
- [Ambito delle query sui log in Log Analytics di Monitoraggio di Azure](logs/scope.md) - Aggiornato l'articolo per includere le applicazioni basate su area di lavoro.
- [Log in Monitoraggio di Azure](logs/data-platform-logs.md) - Aggiornato l'articolo per includere le applicazioni basate su area di lavoro.
- [Colonne standard in Monitoraggio di Azure](logs/log-standard-columns.md) - Aggiornato l'articolo per includere le applicazioni basate su area di lavoro.
- [Limiti del servizio Monitoraggio di Azure](service-limits.md) - Aggiornati i limiti per le query utente.
- [Uso di account di archiviazione gestiti dal cliente in Log Analytics di Monitoraggio di Azure](logs/private-storage.md) - Articolo riscritto.
- [Visualizzazione e analisi dei dati in Azure Log Analytics](./logs/data-platform-logs.md) - Aggiornato l'articolo per includere le applicazioni basate su area di lavoro.


### <a name="platform-logs"></a>Log della piattaforma
- [Schema degli eventi del log attività di Azure - Monitoraggio di Azure](essentials/activity-log-schema.md) - Aggiunti i livelli di gravità.
- [Esempi di modelli di Resource Manager per le impostazioni di diagnostica](essentials/resource-manager-diagnostic-settings.md) - Aggiunto un esempio per l'account di archiviazione di Azure.

### <a name="visualizations"></a>Visualizzazioni
- [Visualizzazioni grafico delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-chart-visualizations.md) - Nuovo articolo.
- [Renderer di barre composite delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-composite-bar.md) - Nuovo articolo.
- [Visualizzazioni grafo delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-graph-visualizations.md) - Nuovo articolo.
- [Visualizzazioni griglia delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-grid-visualizations.md) - Nuovo articolo.
- [Visualizzazioni a nido d'ape delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-honey-comb.md) - Nuovo articolo.
- [Visualizzazioni di testo delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-text-visualizations.md) - Nuovo articolo.
- [Visualizzazioni riquadro delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-tile-visualizations.md) - Nuovo articolo.
- [Visualizzazioni ad albero delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-tree-visualizations.md) - Nuovo articolo.




## <a name="august-2020"></a>Agosto 2020

### <a name="general"></a>Generale

- [Che cosa viene monitorato da Monitoraggio di Azure](monitor-reference.md) - Aggiornato per includere l'agente di Monitoraggio di Azure.


### <a name="agents"></a>Agenti
- [Panoramica dell'agente di Monitoraggio di Azure](agents/azure-monitor-agent-overview.md) - Nuovo articolo.
- [Abilitare Monitoraggio di Azure per un ambiente ibrido](vm/vminsights-enable-hybrid.md) - Aggiornata la versione dell'agente di dipendenza.
- [Panoramica degli agenti di Monitoraggio di Azure](agents/agents-overview.md) - Aggiunto l'agente di Monitoraggio di Azure e consolidata la tabella dei sistemi operativi supportati.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Articoli nuovi e aggiornati dopo la ristrutturazione del contenuto sull'agente
- [Abilitare la Panoramica di VM Insights](vm/vminsights-enable-overview.md)
- [Installare l'agente di Log Analytics in computer Linux](agents/agent-linux.md)
- [Installare l'agente di Log Analytics in computer Windows](agents/agent-windows.md)
- [Panoramica dell'agente di Log Analytics](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights per app Web JavaScript](app/javascript.md) - Aggiunta una sezione di chiarimenti sulla correlazione client/server e sulla configurazione per la correlazione CORS.
- [Creare una nuova risorsa di Application Insights basata sull'area di lavoro in Monitoraggio di Azure](app/create-workspace-resource.md) - Aggiunte le funzionalità fornite dalle applicazioni basate su area di lavoro.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiornati gli indirizzi IP per Live Metrics Stream.
- [Monitorare le applicazioni Java in qualsiasi ambiente: Application Insights in Monitoraggio di Azure](app/java-in-process-agent.md) - Aggiunta una tabella per i dati di telemetria personalizzati supportati.
- [Plug-in React nativo per Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) - Nuovo articolo.
- [Plug-in React per Application Insights JavaScript SDK](app/javascript-react-plugin.md) - Nuovo articolo.
- [Esempio di modello di Resource Manager per la creazione di app per le funzioni di Azure con monitoraggio di Application Insights](app/resource-manager-function-app.md) - Nuovo articolo.
- [Esempio di modello di Resource Manager per la creazione di app Web di Servizi app di Azure con il monitoraggio di Application Insights](app/resource-manager-web-app.md) - Nuovo articolo.
- [Analisi dell'utilizzo con Azure Application Insights](app/usage-overview.md) - Aggiunto un video.

### <a name="autoscale"></a>Autoscale
- [Introduzione alla scalabilità automatica in Azure](autoscale/autoscale-get-started.md) - Aggiunta una sezione sul routing a istanze integre per il servizio app.

### <a name="data-collection"></a>Raccolta dati
- [Configurare la raccolta dei dati per l'agente di Monitoraggio di Azure (anteprima)](agents/data-collection-rule-azure-monitor-agent.md) - Nuovo articolo.
- [Regole di raccolta dati in Monitoraggio di Azure (anteprima)](agents/data-collection-rule-overview.md) - Nuovo articolo.


### <a name="containers"></a>Contenitori
- [Distribuzione della metrica & hPa con informazioni dettagliate sul contenitore](containers/container-insights-deployment-hpa-metrics.md) -nuovo articolo.

### <a name="insights"></a>Informazioni dettagliate
- [Soluzioni di monitoraggio in Monitoraggio di Azure](insights/solutions.md) - Aggiornato per la nuova interfaccia utente.
- [Soluzione Monitoraggio prestazioni rete in Azure](insights/network-performance-monitor.md) - Aggiunte le aree supportate per l'area di lavoro.


### <a name="logs"></a>Log
- [Domande frequenti su Monitoraggio di Azure](faq.md) - Aggiunta una voce per l'eliminazione di dati da un'area di lavoro. Aggiunta una voce sulle risposte 502 e 503.
  - [Progettazione della distribuzione dei log di Monitoraggio di Azure](logs/design-logs-deployment.md) - Aggiornamenti nella sezione sui limiti di velocità del volume di inserimento dati.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Aggiornate le query sull'utilizzo a un formato più efficiente.
- [Ottimizzare le query sui log di Monitoraggio di Azure](logs/query-optimization.md) - Aggiunti valori specifici per gli indicatori delle prestazioni.
- [Esempi di modelli di Resource Manager per le impostazioni di diagnostica in Monitoraggio di Azure](essentials/resource-manager-diagnostic-settings.md) - Aggiunto un esempio per i log di controllo delle query sui log.


### <a name="platform-logs"></a>Log della piattaforma
- [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](essentials/diagnostic-settings.md) - Aggiunto un requisito a livello di area per le impostazioni di diagnostica.

### <a name="visualizations"></a>Visualizzazioni
- [Panoramica di Cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-overview.md) - Aggiunto un video.
- [Spostare un modello di cartella di lavoro di Azure in un'altra area](visualize/workbook-templates-move-region.md) - Nuovo articolo.
- [Spostare una cartella di lavoro di Azure in un'altra area](visualize/workbooks-move-region.md) - Nuovo articolo.



## <a name="july-2020"></a>Luglio 2020

### <a name="general"></a>Generale
- [Distribuire monitoraggio di Azure](deploy-scale.md) : ristrutturare il contenuto di onboarding di VM Insights.
- [Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure](logs/private-link-security.md) - Aggiunta la sezione sui limiti.

### <a name="alerts"></a>Avvisi
- [Regole di azione per gli avvisi di Monitoraggio di Azure](alerts/alerts-action-rules.md) - Aggiunta dei processi dell'interfaccia della riga di comando.
- [Creare e gestire gruppi di azione nel portale di Azure](alerts/action-groups.md) - Aggiornamento per riflettere le modifiche apportate all'interfaccia utente.
- [Query di esempio di Log Analytics in Monitoraggio di Azure](logs/example-queries.md) - Nuovo articolo.
- [Risolvere i problemi relativi agli avvisi dei log in Monitoraggio di Azure](alerts/alerts-troubleshoot-log.md) - Aggiunta la sezione relativa alla quota delle regole di avviso.
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](alerts/alerts-troubleshoot-metric.md) - Aggiunta la sezione relativa alla regola di avviso per una metrica personalizzata che non è ancora stata emessa.
- [Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure](alerts/alerts-metric-overview.md) - È stata aggiunta una raccomandazione per la selezione della granularità dell'aggregazione.

### <a name="application-insights"></a>Application Insights
- [Note sulla versione per l'estensione App Web di Azure - Application Insights](app/web-app-extension-release-notes.md) - Nuovo articolo.
- [Esempi di modello di Resource Manager per risorse di Application Insights](app/resource-manager-app-resource.md) - Nuovo articolo.
- [Risolvere i problemi relativi ad Azure Application Insights Profiler](app/profiler-troubleshooting.md) - Aggiunta la nota su bug del Profiler per le app ASP.NET Core nel servizio app di Azure. 

### <a name="containers"></a>Contenitori
- [Registrare gli avvisi da contenitore Insights](containers/container-insights-log-alerts.md) -nuovo articolo.
- [Avvisi relativi alle metriche di container Insights](containers/container-insights-metric-alerts.md) -nuovo articolo.

### <a name="logs"></a>Log
- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiunta del messaggio di errore e della sezione configurazione della chiave gestita dal cliente per le query.
- [API di raccolta dati HTTP di Monitoraggio di Azure](logs/data-collector-api.md) - Aggiunto l'esempio per Python 3.
- [Ottimizzare le query su log in Monitoraggio di Azure](logs/query-optimization.md) - Aggiunta la sezione che illustra come evitare più analisi dei dati durante l'uso di sottoquery.
- [Esercitazione: Introduzione alle query di Log Analytics](./logs/log-analytics-tutorial.md) - Aggiunto un video.

### <a name="platform-logs"></a>Log della piattaforma
- [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](essentials/diagnostic-settings.md) - Aggiunto un video.
- [Esempi di modelli di Resource Manager per Monitoraggio di Azure](/resource-manager-samples.md) - Aggiunto l'esempio di Resource Manager con il tipo di destinazione Log. 

### <a name="solutions"></a>Soluzioni
- [Soluzioni di monitoraggio in Monitoraggio di Azure](insights/solutions.md) - Aggiunta dei processi dell'interfaccia della riga di comando.
- [Soluzione Gestione di Office 365 in Azure ](insights/solution-office-365.md) - Modificata la data di ritiro.

### <a name="virtual-machines"></a>Macchine virtuali

Articoli nuovi e aggiornati da ristrutturare il contenuto di VM Insights

- [Informazioni su VM Insights](vm/vminsights-overview.md)
- [Configurare Log Analytics area di lavoro per VM Insights](vm/vminsights-configure-workspace.md)
- [Connettere computer Linux a Monitoraggio di Azure](agents/agent-linux.md)
- [Abilitare Monitoraggio di Azure per un ambiente ibrido](vm/vminsights-enable-hybrid.md)
- [Abilitare Monitoraggio di Azure per una singola macchina virtuale o un set di scalabilità di macchine virtuali nel portale di Azure](vm/vminsights-enable-portal.md)
- [Abilitare VM Insights tramite criteri di Azure](./vm/vminsights-enable-policy.md)
- [Abilitare la Panoramica di VM Insights](vm/vminsights-enable-overview.md)
- [Abilitare VM Insights con PowerShell](vm/vminsights-enable-powershell.md)
- [Abilitare VM Insights usando modelli di Gestione risorse](vm/vminsights-enable-resource-manager.md)
- [Abilitare VM Insights con PowerShell o i modelli](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualizzazioni
- [Aggiornamento delle visualizzazioni del dashboard di Log Analytics](logs/dashboard-upgrade.md) - Aggiornata la frequenza di aggiornamento.
- [Visualizzazione dei dati da Monitoraggio di Azure](visualizations.md) - Aggiunto un video.


## <a name="june-2020"></a>Giugno 2020

### <a name="general"></a>Generale
- [Distribuire Monitoraggio di Azure](deploy-scale.md) - Nuovo articolo.
- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiornata la proprietà billingtype. Aggiunti i comandi di PowerShell.

### <a name="agents"></a>Agenti
- [Panoramica dell'agente Log Analytics](agents/log-analytics-agent.md) - Aggiunto il requisito di Python 2.

### <a name="alerts"></a>Avvisi
- [Come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione si sposta in un'altra area di Azure](alerts/alerts-resource-move.md) - Nuovo articolo.
- [Risoluzione dei problemi relativi agli avvisi delle metriche di Azure](alerts/alerts-troubleshoot-metric.md) - Nuovo articolo.
- [Risoluzione dei problemi relativi agli avvisi dei log di Monitoraggio di Azure](alerts/alerts-troubleshoot-metric.md) - Nuovo articolo.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights per app Web JavaScript](app/javascript.md) - Aggiornata la sezione s JavaScript SDK. Aggiornato il frammento per segnalare errori di caricamento.
- [Configurare BYOS (Bring your own Storage) per Profiler e Snapshot Debugger](app/profiler-bring-your-own-storage.md) - Nuovo articolo.
- [Rilevamento delle richieste in ingresso in Azure Application Insights con OpenCensus Python](app/opencensus-python-request.md) - Aggiornate le procedure di registrazione e configurazione per OpenCensus.
- [Monitorare un'app Web ASP.NET live con Azure Application Insights](app/monitor-performance-live-website-now.md) - Aggiornata la data di deprecazione per Status Monitor v1.
- [Monitorare i servizi di Node.js con Azure Application Insights](app/nodejs.md) - Diversi aggiornamenti, tra cui sulla migrazione da versioni e configurazioni precedenti dell'SDK
- [Monitorare le applicazioni Python con Monitoraggio di Azure (anteprima)](app/opencensus-python.md) - Aggiunta una sezione sulla configurazione delle utilità di esportazione di Monitoraggio di Azure.
- [Monitorare le app senza modifiche al codice: strumentazione automatica per Application Insights d Monitoraggio di Azure](app/codeless-overview.md) - Nuovo articolo.
- [Risoluzione degli errori di caricamento dell'SDK per le applicazioni Web JavaScript](app/javascript-sdk-load-failure.md) - Nuovo articolo.

### <a name="containers"></a>Contenitori
- [Come arrestare il monitoraggio del cluster Kubernetes ibrido](containers/container-insights-optout-hybrid.md) - Aggiunta una sezione per Kubernetes con abilitazione di Arc.
- [Configurare il cluster Kubernetes abilitato per Azure Arc con container Insights](containers/container-insights-enable-arc-enabled-clusters.md) -nuovo articolo.
- [Configurare Azure Red Hat OpenShift V4. x con container Insights](containers/container-insights-azure-redhat4-setup.md) -prerequisiti aggiornati.
- [Configurare i dati in tempo reale di container Insights (anteprima)](containers/container-insights-livedata-setup.md) : è stata rimossa la funzionalità che non è disponibile in Azure per enti pubblici degli Stati Uniti.

### <a name="insights"></a>Informazioni dettagliate
- [Domande frequenti: soluzione Monitoraggio prestazioni rete in Azure](insights/network-performance-monitor-faq.md) - Aggiunte le domande frequenti per ExpressRoute.

### <a name="logs"></a>Log
- [Eliminare e ripristinare l'area di lavoro Azure Log Analytics](logs/delete-workspace.md) - Aggiunto un comando di PowerShell. Aggiornate le procedure di risoluzione dei problemi.
- [Gestire le aree di lavoro Log Analytics in Monitoraggio di Azure](logs/manage-access.md) - Aggiunto un esempio per le tabelle non consentite nella sezione sul Controllo degli accessi in base al ruolo di Azure.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Dettagli aggiuntivi sul calcolo delle dimensioni dei dati. Aggiornata la procedura di configurazione degli avvisi sui volumi di dati. Dettagli sulla sicurezza dei dati raccolti da Azure Sentinel. Chiarimenti sul limite dei dati.
- [Usare i log di Monitoraggio di Azure con App per la logica di Azure e Power Automate](logs/logicapp-flow-connector.md) - Aggiunta una sezione sui limiti dei connettori.

### <a name="metrics"></a>Metriche
- [Metriche supportate da Monitoraggio di Azure per tipo di risorsa](essentials/metrics-supported.md) - Aggiornate le metriche per SQL Server.


### <a name="platform-logs"></a>Log della piattaforma

- [Esempi di modelli di Resource Manager per le impostazioni di diagnostica](essentials/resource-manager-diagnostic-settings.md) - Correzione per l'impostazione di diagnostica dei log attività.
- [Inviare il log attività di Azure all'area di lavoro Log Analytics con il portale di Azure](essentials/quick-collect-activity-log-portal.md) - Nuovo articolo.
- [Inviare il log attività di Azure all'area di lavoro Log Analytics con il modello di Azure Resource Manager](essentials/quick-collect-activity-log-arm.md) - Nuovo articolo.

Articoli nuovi e aggiornati dopo la ristrutturazione e il consolidamento del contenuto sui log della piattaforma

- [Archiviare i log delle risorse di Azure nell'account di archiviazione](./essentials/resource-logs.md#send-to-azure-storage)
- [Schema degli eventi del log attività di Azure](essentials/activity-log-schema.md)
- [Log attività di Azure](essentials/activity-log.md)
- [Esempi dell'interfaccia della riga di comando per Monitoraggio di Azure](/cli-samples.md)
- [Esempi di PowerShell in Monitoraggio di Azure](/powershell-samples.md)
- [Procedura dettagliata sull'API REST di Monitoraggio di Azure](essentials/rest-api-walkthrough.md)
- [Servizi e schemi supportati per i log delle risorse di Azure](./essentials/resource-logs-schema.md)
- [Log delle risorse di Azure](essentials/resource-logs.md)
- [Raccogliere e analizzare i log attività di Azure in Monitoraggio di Azure](./essentials/activity-log.md)
- [Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](essentials/diagnostic-settings.md)
- [Esportare il log attività di Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Panoramica dei log della piattaforma di Azure](essentials/platform-logs-overview.md)
- [Trasmettere log della piattaforma di Azure a un hub eventi](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Visualizzare gli eventi dei log attività di Azure in Monitoraggio di Azure](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Macchine virtuali
- [Abilitare VM Insights nella portale di Azure](./vm/vminsights-enable-portal.md) aggiornata per includere Azure Arc.
- [Abilitare Panoramica di VM Insights](vm/vminsights-enable-overview.md) : aggiornato per includere Azure Arc.
- [Informazioni su VM Insights](vm/vminsights-overview.md) - Aggiornato per includere Azure Arc.


### <a name="visualizations"></a>Visualizzazioni
- [Origini dati delle cartelle di lavoro di Monitoraggio di Azure](visualize/workbooks-data-sources.md) - Aggiunta la sezione si avvisi ed endpoint personalizzati.
- [Risoluzione dei problemi relativi alle informazioni dettagliate basate su cartelle di lavoro di Monitoraggio di Azure](insights/troubleshoot-workbooks.md) - Nuovo articolo.
- [Aggiornamento delle visualizzazioni del dashboard di Log Analytics](logs/dashboard-upgrade.md) - Nuovo articolo.



## <a name="may-2020"></a>Maggio 2020

### <a name="general"></a>Generale

- [Domande frequenti su Monitoraggio di Azure](faq.md) - Aggiunta di una sezione per le metriche.
- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Varie modifiche in previsione della disponibilità generale.
- [Definizioni di criteri predefiniti per Monitoraggio di Azure](./policy-reference.md) - Nuovo articolo.
- [Account di archiviazione di proprietà del cliente per l'inserimento dei log](logs/private-storage.md) - Nuovo articolo.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Aggiunta della fatturazione proporzionale per i cluster.
- [Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure](logs/private-link-security.md) - Nuovo articolo.


#### <a name="new-resource-manager-template-samples"></a>Nuovi esempi di modelli di Resource Manager 
- [Esempi di modelli di Resource Manager per Monitoraggio di Azure](/resource-manager-samples.md)
- [Esempi di modelli di Azure Resource Manager per gruppi di azioni](alerts/resource-manager-action-groups.md)
- [Esempi di modelli di Azure Resource Manager per gli agenti](agents/resource-manager-agent.md)
- [Esempi di modelli di Gestione risorse per informazioni dettagliate sul contenitore](containers/resource-manager-container-insights.md)
- [Esempi di modelli di Gestione risorse per VM Insights](vm/resource-manager-vminsights.md)
- [Esempi di modelli di Resource Manager per le impostazioni di diagnostica](essentials/resource-manager-diagnostic-settings.md)
- [Esempi di modelli di Resource Manager per le aree di lavoro Log Analytics](logs/resource-manager-workspace.md)
- [Esempi di modelli di Resource Manager per le query su log](logs/resource-manager-log-queries.md)
- [Esempi di modelli di Resource Manager per le regole di avviso delle query su log](alerts/resource-manager-alerts-log.md)
- [Esempi di modelli di Resource Manager per le regole di avviso delle metriche](alerts/resource-manager-alerts-metric.md)
- [Esempi di modelli di Azure Resource Manager per le cartelle di lavoro](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agenti
- [Installare e configurare l'estensione di Diagnostica di Microsoft Azure](agents/diagnostics-extension-windows-install.md) - Aggiunta di dettagli sulla configurazione della diagnostica.
- [Panoramica dell'agente di Log Analytics](agents/log-analytics-agent.md) - Aggiunta delle versioni di Linux supportate.

### <a name="application-insights"></a>Application Insights

- [Monitorare le applicazioni in esecuzione su Funzioni di Azure con Application Insights - Monitoraggio di Azure](app/monitor-functions.md) - Nuovo articolo.
- [Monitorare i servizi Node.js con Azure Application Insights](app/nodejs.md) - Aggiornamenti generali, inclusa una nuova sezione sulla migrazione da versioni precedenti.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiunta di indirizzi IP per webhook e US Government.
- [Monitorare applicazioni nel servizio Azure Kubernetes (AKS) con Application Insights - Monitoraggio di Azure](app/kubernetes-codeless.md) - Nuovo articolo.
- [Risoluzione dei problemi relativi a dati non disponibili in Application Insights per .NET](app/asp-net-troubleshoot-no-data.md) - Aggiunta di una sezione sulla raccolta di log con dotnet-trace.
- [Usare Analisi delle modifiche alle applicazioni in Monitoraggio di Azure per trovare i problemi delle app Web](app/change-analysis.md) - Diversi aggiornamenti nella funzionalità Analisi delle modifiche.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Articoli nuovi e aggiornati per la versione di anteprima delle applicazioni basate sull'area di lavoro
- [Schema delle risorse di Application Insights basate sull'area di lavoro in Monitoraggio di Azure](app/apm-tables.md)
- [Creare una nuova risorsa di Application Insights basata sull'area di lavoro in Monitoraggio di Azure](app/create-workspace-resource.md)
- [Espressione app() nelle query di log di Monitoraggio di Azure](logs/app-expression.md)
- [Ambito delle query su log in Log Analytics di Monitoraggio di Azure](logs/scope.md)
- [Eseguire query su più risorse con Monitoraggio di Azure](logs/cross-workspace-query.md)
- [Proprietà standard nei record di log di Monitoraggio di Azure](./logs/log-standard-columns.md)
- [Struttura dei log di Monitoraggio di Azure](./logs/data-platform-logs.md)





### <a name="containers"></a>Contenitori
- [Come abilitare il contenitore Insights](containers/container-insights-onboard.md) -aggiornamento della tabella di configurazione del firewall.
- [Come aggiornare informazioni dettagliate sul contenitore per le metriche](containers/container-insights-update-metrics.md) : aggiornamento per l'uso delle identità gestite per la raccolta delle metriche.
- [Costo del monitoraggio di container Insights](containers/container-insights-cost.md) -nuovo articolo.
- [Configurare i dati in tempo reale di container Insights (anteprima)](containers/container-insights-livedata-setup.md) : supporto per la nuova associazione di ruoli del cluster.

### <a name="insights"></a>Informazioni dettagliate
- [Monitoraggio di Azure per la cache di Azure per Redis (anteprima)](insights/redis-cache-insights-overview.md) - Nuovo articolo.
- [Monitorare Key Vault con Monitoraggio di Azure per Key Vault (anteprima)](./insights/key-vault-insights-overview.md) - Nuovo articolo.

### <a name="logs"></a>Log
- [Creare e configurare Log Analytics con PowerShell](logs/powershell-workspace-configuration.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Creare un'area di lavoro Log Analytics nel portale di Azure](logs/quick-create-workspace.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Creare un'area di lavoro Log Analytics usando l'interfaccia della riga di comando di Azure](logs/quick-create-workspace-cli.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](logs/delete-workspace.md) - Aggiornamento delle informazioni sul ripristino di un'area di lavoro eliminata.
- [Funzioni nelle query di log di Monitoraggio di Azure](logs/functions.md) - Rimozione di una nota sulle funzioni che non contengono altre funzioni.
- [Struttura dei log di Monitoraggio di Azure](./logs/data-platform-logs.md) - Chiarimento delle descrizioni delle proprietà per la tabella di Application Insights.
- [Usare i log di Monitoraggio di Azure con App per la logica di Azure e Power Automate](logs/logicapp-flow-connector.md) - Aggiunta di una sezione sui limiti.
- [Usare PowerShell per creare e configurare un'area di lavoro Log Analytics](logs/powershell-workspace-configuration.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.


### <a name="metrics"></a>Metriche
- [Metriche supportate dal Monitoraggio di Azure per tipo di risorsa](essentials/metrics-supported.md) - Chiarimento delle metriche guest e del routing delle metriche. 

### <a name="solutions"></a>Soluzioni
- [Ottimizzare l'ambiente Active Directory con Monitoraggio di Azure](insights/ad-assessment.md) - Aggiunta di Windows Server 2019 alle versioni supportate.
- [Ottimizzare l'ambiente SQL Server con Monitoraggio di Azure](insights/sql-assessment.md) - Aggiunta di SQL Server alle versioni supportate.


### <a name="virtual-machines"></a>Macchine virtuali
- [Abilitare Panoramica di VM Insights](vm/vminsights-enable-overview.md) : aggiunta alle versioni supportate di Ubuntu Server. Sono state aggiunte le aree supportate per l'area di lavoro di Log Analytics.
- [Come eseguire la creazione di grafici delle prestazioni con le informazioni di VM Insights](vm/vminsights-performance.md) -sezione limitazioni aggiunte per le metriche non disponibili.

### <a name="visualizations"></a>Visualizzazioni
- [Cartelle di lavoro di Monitoraggio di Azure e modelli di Azure Resource Manager](visualize/workbooks-automate.md) - Aggiunta di un aggiornamento di Resource Manager per la distribuzione di un modello di cartella di lavoro.
- [Gruppi di cartelle di lavoro di Monitoraggio di Azure](./visualize/workbooks-groups.md) - Nuovo articolo.
- [Cartelle di lavoro di Monitoraggio di Azure - Trasformare i dati JSON con JSONPath](visualize/workbooks-jsonpath.md) - Nuovo articolo.


## <a name="april-2020"></a>Aprile 2020

### <a name="general"></a>Generale

- [Chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiunta di una sezione sulle operazioni asincrone
- [Gestire le aree di lavoro Log Analytics in Monitoraggio di Azure](logs/manage-access.md) - Aggiornamento delle sezioni sui log personalizzati.

### <a name="alerts"></a>Avvisi

- [Regole di azione per gli avvisi di Monitoraggio di Azure](alerts/alerts-action-rules.md) - Aggiunta di un video.
- [Panoramica degli avvisi e del monitoraggio delle notifiche in Azure](alerts/alerts-overview.md) - Aggiunta di un video.

### <a name="application-insights"></a>Application Insights

- [Mappa delle applicazioni in Azure Application Insights](app/app-map.md) - Aggiunta della configurazione dei nomi dei ruoli cloud per l'agente Java.
- [Informazioni di riferimento sull'API .NET Agent di Azure Application Insights](app/status-monitor-v2-api-reference.md) - Informazioni di riferimento consolidate sull'API.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiornamento degli indirizzi IP per API App Insights e Log Analytics, webhook del gruppo di azioni, Azure US Government.
- [Monitorare le applicazioni Java ovunque](app/java-standalone-config.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in qualsiasi ambiente](app/java-in-process-agent.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in esecuzione in qualsiasi ambiente](app/java-standalone-arguments.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in esecuzione in locale](app/java-on-premises.md) - Nuovo articolo.
- [Rimuovere Application Insights in Visual Studio](app/remove-application-insights.md) - Nuovo articolo.
- [Campionamento dei dati di telemetria in Azure Application Insights](app/sampling.md) - Correzione del campionamento a frequenza fissa in Python.

### <a name="containers"></a>Contenitori

- [Configurare Azure Red Hat OpenShift V4. x con container Insights](containers/container-insights-azure-redhat4-setup.md) -nuovo articolo.
- [Come correggere manualmente i problemi di sincronizzazione di ServiceNow](alerts/itsmc-resync-servicenow.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Azure e Red Hat OpenShift v4](containers/container-insights-optout-openshift-v4.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Azure Red Hat OpenShift v3](containers/container-insights-optout-openshift-v3.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Kubernetes ibrido](containers/container-insights-optout-hybrid.md) - Nuovo articolo.

### <a name="insights"></a>Informazioni dettagliate

- [Monitorare Azure Key Vault con Monitoraggio di Azure per Key Vault (anteprima)](insights/key-vault-insights-overview.md) - Nuovo articolo.

### <a name="logs"></a>Log

- [Limiti del servizio Monitoraggio di Azure](service-limits.md) - Aggiunta della limitazione delle query utente.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Aggiunta della fatturazione per i cluster di log. Aggiunta della query Kusto per consentire ai clienti con piano tariffario legacy per nodo di determinare se è necessario passare a un livello di prenotazione per GB o capacità.

### <a name="metrics"></a>Metriche

- [Funzionalità avanzate di Esplora metriche di Azure](essentials/metrics-charts.md) - Aggiunta della sezione sull'aggregazione.

### <a name="workbooks"></a>Workbooks

- [Cartelle di lavoro di Monitoraggio di Azure e modelli di Azure Resource Manager](visualize/workbooks-automate.md) - Aggiunta di un modello di Resource Manager per la distribuzione di un modello di cartella di lavoro.

## <a name="march-2020"></a>Marzo 2020

### <a name="general"></a>Generale

- [Panoramica di Monitoraggio di Azure](overview.md) - Aggiunta del video della panoramica di Monitoraggio di Azure.
- [Configurazione della chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Aggiornamenti di carattere generale.
- [Informazioni di riferimento sui dati di Monitoraggio di Azure](/azure/azure-monitor/reference/) - Nuovo sito.

### <a name="alerts"></a>Avvisi

- [Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure](alerts/alerts-activity-log.md) - Spiegazione aggiuntiva sul modello di Resource Manager.
- [Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure](alerts/alerts-metric-overview.md) - Aggiornato per includere il supporto per enti governativi.
- [Risoluzione dei problemi relativi ad avvisi e notifiche di Monitoraggio di Azure](alerts/alerts-troubleshoot.md) - Nuovo articolo.

### <a name="application-insights"></a>Application Insights

- [Automatizzare Azure Application Insights con PowerShell](app/powershell.md) - Aggiunta di esempi di ARMClient.
- [Esportazione continua dei dati di telemetria da Application Insights](app/export-telemetry.md) - Aggiunta di una tabella contenente i dettagli della struttura di esportazione.
- [Abilitare Snapshot Debugger per le app .NET nel servizio app di Azure](app/snapshot-debugger-appservice.md) - Aggiunta dell'esempio di modello di Resource Manager.
- [Gestire l'utilizzo e i costi per Azure Application Insights](app/pricing.md) - Aggiunta di informazioni sull'avviso relativo al limite di utilizzo dati.
- [Monitorare le applicazioni Python con Monitoraggio di Azure (anteprima)](app/opencensus-python.md) - Aggiunta delle metriche standard.
- [Supporto dei mapping di origine per le applicazioni JavaScript - Application Insights in Monitoraggio di Azure](app/source-map-support.md) - Nuovo articolo.

### <a name="containers"></a>Contenitori

- [Domande frequenti su monitoraggio di Azure](faq.md) : aggiornamento per informazioni dettagliate sul contenitore.
- [Configurare il monitoraggio GPU con container Insights](containers/container-insights-gpu-monitoring.md) -nuovo articolo.

### <a name="insights"></a>Informazioni dettagliate

- [Soluzione di gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiornamento della data di deprecazione.

### <a name="logs"></a>Log

- [Ottimizzare le query di log in Monitoraggio di Azure](logs/query-optimization.md) - Aggiunta della condizione della CPU per l'analisi XML e JSON.
- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](logs/delete-workspace.md) - Aggiunta della sezione relativa alla risoluzione dei problemi.
- [Usare i log di Monitoraggio di Azure con App per la logica di Azure e Power Automate](logs/logicapp-flow-connector.md) - Aggiornato per il nuovo connettore di Monitoraggio di Azure.

### <a name="metrics"></a>Metriche

- [Metriche del disco deprecate nel portale di Azure](essentials/portal-disk-metrics-deprecation.md) - Nuovo articolo.
- [Esercitazione - Creare un grafico delle metriche in Monitoraggio di Azure](essentials/tutorial-metrics-explorer.md) - Aggiunta di un video.

### <a name="platform-logs"></a>Log della piattaforma

- [Raccogliere e analizzare il log attività di Azure in Monitoraggio di Azure](./essentials/activity-log.md) - Articolo riscritto per illustrare meglio la raccolta del log attività con le impostazioni di diagnostica.

### <a name="virtual-machines"></a>Macchine virtuali

- [Monitorare macchine virtuali di Azure con Monitoraggio di Azure](vm/monitor-vm-azure.md) - Nuovo articolo.
- [Guida introduttiva: monitorare le macchine virtuali di Azure con monitoraggio di Azure:](vm/quick-monitor-azure-vm.md) aggiornamento per aggiungere le informazioni di VM.
- [Avvisi da VM Insights](vm/vminsights-alerts.md) : nuovo articolo.
- [Abilitare Panoramica di VM Insights](vm/vminsights-enable-overview.md) : collegamenti di download dell'agente aggiornati.

Aggiornamenti generali per la disponibilità generale di VM Insights

- [Informazioni su VM Insights](vm/vminsights-overview.md)
- [Domande frequenti su VM Insights (GA)](vm/vminsights-ga-release-faq.md) 
- [Abilitare VM Insights tramite criteri di Azure](./vm/vminsights-enable-policy.md) 
- [Come eseguire il grafico delle prestazioni con le informazioni dettagliate di VM](vm/vminsights-performance.md)
- [Come eseguire query sui log da VM Insights](vm/vminsights-log-search.md)
- [Visualizzare le dipendenze dell'app con VM Insights](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Visualizzazioni

- [Visualizzazione dei dati da Monitoraggio di Azure](visualizations.md) - Aggiornato per segnalare la deprecazione pianificata di Progettazione visualizzazioni.

## <a name="february-2020"></a>Febbraio 2020

### <a name="agents"></a>Agenti

Diversi aggiornamenti con la riscrittura del contenuto sull'estensione di diagnostica.

- [Panoramica degli agenti di monitoraggio di Azure](agents/agents-overview.md) - Tabelle ristrutturate per chiarire meglio le funzionalità specifiche di ogni agente.
- [Panoramica dell'estensione di Diagnostica di Azure](agents/diagnostics-extension-overview.md) - Riscrittura completa.
- [Usare l'archiviazione BLOB per l'archiviazione di tabelle e IIS per gli eventi in Monitoraggio di Azure](essentials/diagnostics-extension-logs.md) - Riscrittura generale con aggiornamenti e chiarimenti.
- [Installare e configurare l'estensione di Diagnostica di Microsoft Azure](agents/diagnostics-extension-windows-install.md) - Nuovo articolo. 
- [Schema dell'estensione di Diagnostica Microsoft](agents/diagnostics-extension-schema-windows.md) - Riorganizzazione del contenuto.
- [Inviare dati dall'estensione di Diagnostica di Microsoft Azure a Hub eventi di Azure](agents/diagnostics-extension-stream-event-hubs.md) - Riscrittura completa e aggiornamento.
- [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](../cloud-services/diagnostics-extension-to-storage.md) - Riscrittura completa e aggiornamento.
- [Estensione macchina virtuale Log Analytics per Windows](../virtual-machines/extensions/oms-windows.md) - Chiarimenti sulla relazione con l'agente di Log Analytics.
- [Estensione macchina virtuale Monitoraggio di Azure per Linux](../virtual-machines/extensions/oms-linux.md) - Chiarimenti sulla relazione con l'agente di Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Stringhe di connessione in Azure Application Insights](app/sdk-connection-string.md) - Nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni

#### <a name="container-insights"></a>Informazioni dettagliate sul contenitore

- [Integrare Azure Active Directory con il servizio Azure Kubernetes](../aks/azure-ad-integration-cli.md) : è stata aggiunta una nota per la creazione di un'applicazione client per supportare il cluster Kubernetes abilitato per RBAC per supportare le informazioni dettagliate sul contenitore.

#### <a name="vm-insights"></a>Informazioni dettagliate sulle macchine virtuali

- [Domande frequenti su VM Insights (GA)](vm/vminsights-ga-release-faq.md) : modificare il modo in cui vengono archiviati i dati sulle prestazioni.

#### <a name="office-365"></a>Office 365

- [Soluzione di gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiornamento della data di deprecazione.


### <a name="logs"></a>Log

- [Ottimizzare le query sui log in Monitoraggio di Azure](logs/query-optimization.md) - Nuovo articolo.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Query di esempio migliorate per illustrarne l'utilizzo.

### <a name="metrics"></a>Metriche

- [Metriche della piattaforma di Monitoraggio di Azure esportabili tramite le impostazioni di diagnostica](essentials/metrics-supported-export-diagnostic-settings.md) - Aggiunta di una sezione sul comportamento cambiato per valori Null e zero.

### <a name="visualizations"></a>Visualizzazioni

Diversi nuovi articoli di guida alla conversione da Progettazione visualizzazioni a Cartelle di lavoro.

- [Guida alla transizione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-overview.md) - Nuovo articolo.
- [Opzioni per la conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-options.md) - Nuovo articolo.
- [Conversioni dei riquadri da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-tiles.md) - Nuovo articolo.
- [Riepilogo e accesso alla conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-access.md) - Nuovo articolo.
- [Attività comuni della conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-tasks.md) - Nuovo articolo.
- [Esempi di conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](visualize/view-designer-conversion-examples.md) - Nuovo articolo.

## <a name="january-2020"></a>Gennaio 2020

### <a name="general"></a>Generale

- [Che cosa viene monitorato da Monitoraggio di Azure?](monitor-reference.md) - Nuovo articolo.

### <a name="agents"></a>Agenti

- [Raccogliere i dati di log con l'agente di Log Analytics](agents/log-analytics-agent.md) - Tabella dei requisiti del firewall di rete aggiornata.

### <a name="alerts"></a>Avvisi

- [Creare e gestire gruppi di azione nel portale di Azure](alerts/action-groups.md) - Impostazione non più necessaria rimossa per le funzioni della versione 2.
- [Creare un avviso metrica con un modello di Resource Manager](alerts/alerts-metric-create-templates.md) - Aggiunto un esempio per il parametro *ignoreDataBefore*.  Aggiunti vincoli relativi alle regole con più criteri.
- [Uso dell'API REST degli avvisi di Log Analytics](alerts/api-alerts.md) - Coretto un esempio JSON.

### <a name="application-insights"></a>Application Insights

- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Sezione Test di disponibilità aggiornata con la procedura per aggiungere una regola di porta in ingresso per consentire il traffico tramite i gruppi di sicurezza di rete di Azure.
- [Risolvere i problemi con Azure Application Insights Profiler](app/profiler-troubleshooting.md) - Informazioni generali sulla risoluzione dei problemi aggiornate.
- [Campionamento in Application Insights](app/sampling.md) - Contenuto aggiornato e ristrutturato per migliorare la leggibilità in base al feedback dei clienti.

### <a name="data-security"></a>Sicurezza dei dati

- [Configurazione della chiave gestita dal cliente di Monitoraggio di Azure](logs/customer-managed-keys.md) - Nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni

#### <a name="container-insights"></a>Informazioni dettagliate sul contenitore

- [Configurare la raccolta dati degli agenti di container Insights](containers/container-insights-agent-config.md) : sono stati aggiunti dettagli per l'aggiornamento dell'agente in Azure Red Hat OpenShift e sono state aggiunte altre informazioni per distinguere i metodi per l'aggiornamento dell'agente.
- [Creare avvisi sulle prestazioni per il contenitore Insights](./containers/container-insights-log-alerts.md) : informazioni rivedute e passaggi aggiornati per la creazione di un avviso sui dati sulle prestazioni archiviati nell'area di lavoro usando gli avvisi del contesto dell'area di lavoro.
- [Kubernetes Monitoring with container Insights](containers/container-insights-analyze.md) : aggiornamento di entrambi gli articoli introduttivi e analisi relativi al supporto dei cluster Kubernetes di Windows.
- [Configurare i cluster OpenShift di Azure Red Hat con il contenitore Insights](containers/container-insights-azure-redhat-setup.md) -sono stati aggiunti dettagli per l'aggiornamento dell'agente in Azure Red Hat OpenShift e sono state aggiunte altre informazioni per distinguere i metodi per l'aggiornamento dell'agente.
- [Configurare cluster Kubernetes ibridi con informazioni dettagliate sul contenitore](containers/container-insights-hybrid-setup.md) : aggiornato per riflettere il supporto aggiunto per la porta sicura: 10250 con il CAdvisor di Kubelet.
- [Come gestire l'agente di Application Insights](containers/container-insights-manage-agent.md) : dettagli aggiornati relativi al comportamento e alla configurazione della frammentazione delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.
- [Configurare l'integrazione di Prometeo per il contenitore Insights](containers/container-insights-prometheus-integration.md) : dettagli aggiornati relativi al comportamento e alla configurazione della frammentazione delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.
- [Come aggiornare](containers/container-insights-update-metrics.md) le informazioni dettagliate sul contenitore per le metriche: dettagli aggiornati relativi al comportamento e alla configurazione della frammentazione delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.

#### <a name="vm-insights"></a>Informazioni dettagliate sulle macchine virtuali

- [Domande frequenti su VM Insights (GA)](vm/vminsights-ga-release-faq.md) : sono state aggiunte informazioni sull'aggiornamento dell'area di lavoro e degli agenti alla nuova versione.

#### <a name="office-365"></a>Office 365

- [Soluzione Gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiunti dettagli e domande frequenti sulla migrazione alla soluzione Office 365 in Azure Sentinel. Sezione di onboarding rimossa.

### <a name="logs"></a>Log

- [Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure](logs/manage-access.md) - Aggiornamenti alle azioni da non eseguire
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](logs/manage-cost-storage.md) - Aggiunto un chiarimento sul calcolo del volume di dati nella sezione Modello di prezzi.
- [Usare i modelli di Azure Resource Manager per creare e configurare un'area di lavoro Log Analytics](./logs/resource-manager-workspace.md) - Modello aggiornato con nuovi piani tariffari.

### <a name="platform-logs"></a>Log della piattaforma

- [Raccogliere il log attività di Azure con le impostazioni di diagnostica- Monitoraggio di Azure](./essentials/activity-log.md) - Informazioni aggiuntive sulle proprietà modificate.
- [Esportare il log attività di Azure](./essentials/activity-log.md#legacy-collection-methods) - Contenuto aggiornato con le modifiche all'interfaccia utente. 

## <a name="december-2019"></a>Dicembre 2019

### <a name="agents"></a>Agenti

- [Connettere computer Linux a Monitoraggio di Azure](agents/agent-linux.md) - Nuovo articolo.

### <a name="alerts"></a>Avvisi

- [Creare un avviso metrica con un modello di Resource Manager](alerts/alerts-metric-create-templates.md) - Aggiunto un esempio per la metrica personalizzata.
- [Creazione di avvisi con soglie dinamiche in Monitoraggio di Azure](alerts/alerts-dynamic-thresholds.md) - Aggiunta una sezione per l'interpretazione dei grafici con soglie dinamiche.
- [Panoramica degli avvisi e del monitoraggio delle notifiche in Azure](alerts/alerts-overview.md) - Query di Resource Graph aggiornata
- [Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure](alerts/alerts-metric-near-real-time.md) - Aggiornamento delle metriche e delle dimensioni supportate.
- [Passare dall'API legacy degli avvisi di Log Analytics alla nuova API degli avvisi di Azure](alerts/alerts-log-api-switch.md) - Nota aggiunta sul nome dell'avviso modificato.
- [Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure](alerts/alerts-metric-overview.md) - Aggiunti i tipi di risorsa supportati per il monitoraggio su larga scala.

### <a name="application-insights"></a>Application Insights

- [Application Insights per le app Worker Service (app non HTTP)](app/worker-service.md) - Aggiunto un livello di registrazione predefinito al codice C#. Versione di riferimento del pacchetto aggiornata.
- [Informazioni di riferimento su ApplicationInsights.config: Azure](app/configuration-with-applicationinsights-config.md) - Codice di esempio aggiornato.
- [Automatizzare Azure Application Insights con PowerShell](app/powershell.md) - Modello di Resource Manager aggiornato.
- [Creare una nuova risorsa di Azure Application Insights](app/create-new-resource.md) - Aggiunta una nota sul nome univoco a livello globale.
- [Diagnosticare con Live Metrics Stream - Azure Application Insights](app/live-stream.md) - Requisito della versione di ASP.NET Core SDK aggiornato.
- [Contatori di eventi in Application Insights](app/eventcounters.md) - Categoria e tabella aggiornate in customMetrics.
- [Esplorare i log di traccia Java in Azure Application Insights](app/java-trace-logs.md) - Aggiunta la configurazione per la soglia di registrazione dell'agente Java.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Indirizzi IP per Live Metrics Stream aggiornati.
- [Monitorare le prestazioni dei servizi app di Azure](app/azure-web-apps.md) - Aggiunto il supporto per ASP.NET Core 3.0. 
- [Monitorare le applicazioni Python con monitoraggio di Azure (anteprima)](app/opencensus-python.md) - Aggiunto un chiarimento sul mapping dello schema di OpenCensus Python allo schema di Monitoraggio di Azure.
- [Note sulla versione per Azure Application Insights](app/release-notes.md) - Aggiunte note sulle versioni precedenti.
- [Canali di telemetria in Azure Application Insights](app/telemetry-channels.md) - Aggiornata la durata dei dati rimossi durante un periodo prolungato di assenza di connessione.
- [Campionamento della telemetria in Azure Application Insights](app/sampling.md) - Corretto un frammento di codice per il parametro personalizzato TelemetryInitializer.
- [Risoluzione dei problemi di Application Insights in un progetto Web Java](app/java-troubleshoot.md) - Rimossa una frase relativa alla raccolta di dipendenze non supportata in JDK 9.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni

- [Domande frequenti su container Insights](./faq.md) : aggiunta di una domanda nei campi Image e Name.
- [Soluzione Analisi SQL di Azure in Monitoraggio di Azure](insights/azure-sql.md) - Aggiornato il supporto di Istanza gestita per le attese del database.
- [Configurare l'impostazione raccolta dati agente di container Insights](containers/container-insights-agent-config.md) -aggiunta per enrich_container_logs.
- [Configurare i cluster Kubernetes ibridi con contenitore Insights](containers/container-insights-hybrid-setup.md) -aggiunta della sezione relativa alla risoluzione dei problemi.
- [Monitorare lo stato della replica di Active Directory con Monitoraggio di Azure](insights/ad-replication-status.md) - Aggiornati i prerequisiti per .NET Framework.
- [Soluzione Monitoraggio prestazioni rete in Azure](insights/network-performance-monitor.md) - Aggiunte le aree supportate.
- [Ottimizzare l'ambiente Active Directory con Monitoraggio di Azure](insights/ad-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Ottimizzare l'ambiente SQL Server con Monitoraggio di Azure](insights/sql-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Ottimizzare l'ambiente System Center Operations Manager con Azure Log Analytics](insights/scom-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Connessioni supportate con il Connettore di Gestione dei servizi IT in Azure Log Analytics](alerts/itsmc-connections.md) - Aggiunta la voce New York ai prerequisiti per ID client e segreto client.

### <a name="logs"></a>Log

- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](logs/delete-workspace.md) - Aggiunto un metodo PowerShell.
- [Progettazione della distribuzione dei log di Monitoraggio di Azure](logs/design-logs-deployment.md) - Velocità di inserimento dati per un'area di lavoro aumentata.

### <a name="metrics"></a>Metriche

- [Metriche della piattaforma di Monitoraggio di Azure esportabili tramite Impostazioni di diagnostica](essentials/metrics-supported-export-diagnostic-settings.md) - Nuovo articolo.

### <a name="platform-logs"></a>Log della piattaforma

Più articoli aggiornati nell'ambito della ristrutturazione del contenuto per i log della piattaforma in base a una nuova funzionalità per la configurazione del log attività con le impostazioni di diagnostica.

- [Archiviare i log delle risorse di Azure nell'account di archiviazione](./essentials/resource-logs.md#send-to-azure-storage)
- [Schema degli eventi del log attività di Azure](essentials/activity-log-schema.md)
- [Limiti del servizio Monitoraggio di Azure](service-limits.md)
- [Raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics](./essentials/activity-log.md)
- [Raccogliere il log attività di Azure con le impostazioni di diagnostica (anteprima) - Monitoraggio di Azure](./essentials/activity-log.md)
- [Raccogliere i log attività di Azure in un'area di lavoro Log Analytics tra diversi tenant di Azure](./essentials/activity-log.md)
- [Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Creare un'impostazione di diagnostica in Azure usando un modello di Resource Manager](./essentials/resource-manager-diagnostic-settings.md)
- [Creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](essentials/diagnostic-settings.md)
- [Esportare il log attività di Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Panoramica dei log della piattaforma di Azure](essentials/platform-logs-overview.md)
- [Trasmettere i dati di monitoraggio di Azure a un hub eventi](essentials/stream-monitoring-data-event-hubs.md)
- [Trasmettere log della piattaforma di Azure a un hub eventi](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Guide introduttive ed esercitazioni

- [Creare un grafico delle metriche in Monitoraggio di Azure](essentials/tutorial-metrics-explorer.md) - Nuovo articolo.
- [Raccogliere i log delle risorse da una risorsa di Azure e analizzarli con Monitoraggio di Azure](essentials/tutorial-resource-logs.md) - Nuovo articolo.
- [Monitorare una risorsa di Azure con Monitoraggio di Azure](essentials/quick-monitor-azure-resource.md) - Nuovo articolo.
   
## <a name="next-steps"></a>Passaggi successivi

- Se si vuole contribuire alla documentazione di Monitoraggio di Azure, vedere la [guida per i collaboratori di Microsoft Docs](/contribute/).