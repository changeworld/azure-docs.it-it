---
title: Log di Monitoraggio di Azure
description: Vengono descritti Monitoraggio di Azure log usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873380"
---
# <a name="azure-monitor-logs-overview"></a>Panoramica dei log di Monitoraggio di Azure
Monitoraggio di Azure Logs è una funzionalità di Monitoraggio di Azure che raccoglie e organizza i dati di log e prestazioni dalle [risorse monitorate.](../monitor-reference.md) I dati provenienti da origini diverse, ad esempio [](../agents/agents-overview.md)i log della piattaforma dei [](../app/app-insights-overview.md) servizi di Azure, i dati di log e prestazioni degli agenti di macchine virtuali e i dati sull'utilizzo e sulle prestazioni delle applicazioni possono essere consolidati in una singola area di lavoro in modo che possano essere analizzati insieme usando un sofisticato linguaggio di query in grado di analizzare rapidamente milioni di record. [](../essentials/platform-logs-overview.md) È possibile eseguire una query semplice che recupera semplicemente un set specifico di record o esegue analisi dei dati sofisticate per identificare i modelli critici nei dati di monitoraggio. Usare le query di log e i relativi risultati in modo interattivo usando Log Analytics, usarle in regole di avviso per ricevere notifiche proattive sui problemi o visualizzare i risultati in una cartella di lavoro o in un dashboard.

> [!NOTE]
> Monitoraggio di Azure Logs è la metà della piattaforma dati che supporta Monitoraggio di Azure. L'altra è [Monitoraggio di Azure metriche che](../essentials/data-platform-metrics.md) archivia i dati numerici in un database time-series. In questo modo questi dati sono più leggeri rispetto ai dati nei log di Monitoraggio di Azure e possono supportare scenari near real-time, rendendoli particolarmente utili per l'avviso e il rilevamento rapido dei problemi. Le metriche possono tuttavia archiviare solo dati numerici in una particolare struttura, mentre i log possono archiviare un'ampia gamma di tipi di dati diversi ognuno con una propria struttura. È anche possibile eseguire analisi complesse sui dati dei log usando query di log che non possono essere usate per l'analisi dei dati delle metriche.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Che cosa è possibile fare con i log di Monitoraggio di Azure?
La tabella seguente descrive alcuni dei diversi modi in cui è possibile usare i log in Monitoraggio di Azure:

|  | Descrizione |
|:---|:---|
| **Analizzare** | Usare [Log Analytics nel](./log-analytics-tutorial.md) portale di Azure per scrivere query di [log](./log-query-overview.md) e analizzare in modo interattivo i dati di log usando un potente motore di analisi |
| **Avviso** | Configurazione di una [regola di avviso per il log](../alerts/alerts-log.md) che invia una notifica o esegue un'[azione automatica](../alerts/action-groups.md) quando i risultati della query corrispondono a un risultato specifico. |
| **Visualizzazione** | Aggiungere i risultati delle query visualizzati come tabelle o grafici in un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).<br>Creare una [cartella di lavoro](../visualize/workbooks-overview.md) per combinare più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](../visualize/powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query in [Grafana](../visualize/grafana-plugin.md) per sfruttarne le capacità di creazione dashboard e combinare i risultati con altre origini dati.|
| **Insights** | Informazioni [dettagliate](../monitor-reference.md#insights-and-core-solutions) sul supporto che offrono un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici.  |
| **Recupero** | Accedere ai risultati delle query di log da una riga di comando usando l'[interfaccia della riga di comando di Azure](/cli/azure/monitor/log-analytics).<br>Accedere ai risultati delle query di log da una riga di comando usando i [cmdlet di PowerShell](/powershell/module/az.operationalinsights).<br>Accedere ai risultati delle query di log da un'applicazione personalizzata usando l'[API REST](https://dev.loganalytics.io/). |
| **Export** | Configurare [l'esportazione automatica dei dati di log nell'account](./logs-data-export.md) di archiviazione di Azure o Hub eventi di Azure.<br>Compilare un flusso di lavoro per recuperare i dati di log e copiarli in una posizione esterna usando [App per la logica](./logicapp-flow-connector.md). |

![Panoramica dei log](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Raccolta dati
Dopo aver creato un'area di lavoro Log Analytics, è necessario configurare origini diverse per l'invio dei dati. Nessun dato viene raccolto automaticamente. Questa configurazione sarà diversa a seconda dell'origine dati. Ad esempio, creare [impostazioni di diagnostica per inviare](../essentials/diagnostic-settings.md) i log delle risorse dalle risorse di Azure all'area di lavoro. [Abilitare le informazioni dettagliate](../vm/vminsights-enable-overview.md) sulle macchine virtuali per raccogliere dati dalle macchine virtuali. Configurare [le origini dati nell'area di lavoro](../agents/data-sources.md) per raccogliere altri eventi e dati sulle prestazioni.

- Per un elenco completo delle origini dati che è possibile configurare per l'invio di dati a Monitoraggio di Azure log, vedere What [is monitored by Monitoraggio di Azure?](../monitor-reference.md) (Che cosa viene monitorato Monitoraggio di Azure? .


## <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics
I dati raccolti Monitoraggio di Azure log vengono archiviati in una o più [aree di lavoro Log Analytics.](./design-logs-deployment.md) L'area di lavoro definisce la posizione geografica dei dati, i diritti di accesso che definiscono quali utenti possono accedere ai dati e le impostazioni di configurazione, ad esempio il piano tariffario e la conservazione dei dati.  

È necessario creare almeno un'area di lavoro per usare Monitoraggio di Azure log. Una singola area di lavoro può essere sufficiente per tutti i dati di monitoraggio oppure può scegliere di creare più aree di lavoro a seconda dei requisiti. Ad esempio, si potrebbe avere un'area di lavoro per i dati di produzione e un'altra per il test. 

- Vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](./quick-create-workspace.md) creare una nuova area di lavoro.
- Per [considerazioni sulla creazione di più aree Monitoraggio di Azure,](design-logs-deployment.md) vedere Progettazione della distribuzione dei log di distribuzione.

## <a name="data-structure"></a>Struttura dei dati
Le query di log recuperano i dati da un'area di lavoro Log Analytics. Ogni area di lavoro contiene più tabelle organizzate in colonne separate con più righe di dati. Ogni tabella è definita da un set univoco di colonne condivise dalle righe di dati fornite dall'origine dati. 

[![Monitoraggio di Azure dei log](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Anche i dati Application Insights log vengono archiviati in log Monitoraggio di Azure, ma vengono archiviati in modo diverso a seconda della configurazione dell'applicazione. Per un'applicazione basata sull'area di lavoro, i dati vengono archiviati in un'area di lavoro Log Analytics in un set standard di tabelle per contenere dati quali richieste di applicazioni, eccezioni e visualizzazioni di pagina. Più applicazioni possono usare la stessa area di lavoro. Per un'applicazione classica, i dati non vengono archiviati in un'area di lavoro Log Analytics. Usa lo stesso linguaggio di query e si creano ed eseguono query usando lo stesso strumento di Log Analytics nel portale di Azure. I dati per le applicazioni classiche, tuttavia, vengono archiviati separatamente l'uno dall'altro. La struttura generale è la stessa delle applicazioni basate sull'area di lavoro, anche se i nomi delle tabelle e delle colonne sono diversi. Per [un confronto dettagliato dello](../app/apm-tables.md) schema per le applicazioni basate su aree di lavoro e classiche, vedere Modifiche alle risorse basate sull'area di lavoro.


> [!NOTE]
> È ancora disponibile la completa compatibilità con le versioni precedenti per le query classiche sulle risorse di Application Insights, le cartelle di lavoro e gli avvisi basati su log all'interno dell'esperienza di Application Insights. Per eseguire query o visualizzare la [nuova struttura di tabella o lo schema basati sull'area di lavoro](../app/apm-tables.md) è prima necessario passare all'area di lavoro Log Analytics. Durante l'anteprima, selezionare **Log** all'interno dei riquadri di Application Insights per accedere all'esperienza di query classica di Application Insights. Per [altri dettagli, vedere Ambito](./scope.md) query.


[![Monitoraggio di Azure di log per Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Query di log
I dati vengono recuperati da un'area di lavoro Log Analytics usando una query di log che è una richiesta di sola lettura per elaborare i dati e restituire risultati. Le query di log vengono scritte in [Kusto Query Language (KQL),](/azure/data-explorer/kusto/query/)che è lo stesso linguaggio di query usato da Esplora dati di Azure. È possibile scrivere query di log in Log Analytics per analizzare in modo interattivo i risultati, usarli nelle regole di avviso per ricevere notifiche proattive sui problemi o includerne i risultati in cartelle di lavoro o dashboard. Le informazioni dettagliate includono query predefinite per supportare le visualizzazioni e le cartelle di lavoro.

- Per [un elenco delle Monitoraggio di Azure](./log-query-overview.md) di utilizzo delle query di log e dei riferimenti alle esercitazioni e ad altre documentazione, vedere Log queries in Monitoraggio di Azure (Query di log in Monitoraggio di Azure per un elenco di dove vengono usate le query di log) e riferimenti alle esercitazioni e ad altre documentazione per iniziare.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Usare Log Analytics, uno strumento nel portale di Azure, per modificare ed eseguire query di log e analizzare in modo interattivo i risultati. È quindi possibile usare le query create per supportare altre funzionalità in Monitoraggio di Azure, ad esempio avvisi di query di log e cartelle di lavoro. Accedere a Log Analytics **dall'opzione** Log nel menu Monitoraggio di Azure o dalla maggior parte degli altri servizi nel portale di Azure.

- Vedere [Panoramica di Log Analytics in Monitoraggio di Azure](./log-analytics-overview.md) per una descrizione di Log Analytics. 
- Vedere [Esercitazione su Log Analytics](./log-analytics-tutorial.md) per informazioni sull'uso delle funzionalità di Log Analytics per creare una query di log semplice e analizzarne i risultati.



## <a name="relationship-to-azure-data-explorer"></a>Relazione con Esplora dati di Azure
Monitoraggio di Azure log si basa su Esplora dati di Azure. Un'area di lavoro Log Analytics è approssimativamente equivalente a un database in Esplora dati di Azure, le tabelle sono strutturate nello stesso modo ed entrambe usano lo stesso linguaggio di query Kusto (KQL). L'esperienza d'uso di Log Analytics per l'uso Monitoraggio di Azure query nel portale di Azure è simile all'uso dell'Esplora dati di Azure'interfaccia utente Web. È anche possibile [includere dati da un'area di lavoro Log Analytics in una query Esplora dati di Azure query](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [query di log per](./log-query-overview.md) recuperare e analizzare i dati da un'area di lavoro Log Analytics.
- Informazioni sulle [metriche in Monitoraggio di Azure](../essentials/data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](../agents/data-sources.md) per diverse risorse in Azure.