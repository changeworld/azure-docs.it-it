---
title: Tempo di inserimento dei dati di log in Monitoraggio di Azure | Microsoft Docs
description: Illustra i diversi fattori che influiscono sulla latenza nella raccolta dei dati in Monitoraggio di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 3bba9dbf40fe6893a06c21d7f6b5475cfa8552cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176655"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tempo di inserimento dei dati di log in Monitoraggio di Azure
Monitoraggio di Azure è un servizio dati su larga scala che serve migliaia di clienti che inviano terabyte di dati ogni mese a un ritmo crescente. Spesso sono state poste domande sul tempo necessario affinché i dati di log diventino disponibili dopo la raccolta. Questo articolo illustra i diversi fattori che influiscono su questa latenza.

## <a name="typical-latency"></a>Latenza tipica
La latenza si riferisce al tempo in cui i dati vengono creati nel sistema monitorato e al tempo necessario affinché diventino disponibili per l'analisi in Monitoraggio di Azure. La latenza tipica di inserimento dei dati di log è compresa tra 20 e 3 minuti. Tuttavia, la latenza specifica per i dati specifici varierà in base a una serie di fattori illustrati di seguito.


## <a name="factors-affecting-latency"></a>Fattori che influiscono sulla latenza
Il tempo totale di inserimento per un determinato set di dati può essere suddiviso nelle seguenti aree di alto livello. 

- Ora agente: il tempo per individuare un evento, raccoglierlo, quindi inviarlo a monitoraggio di Azure registra il punto di inserimento come record di log. Nella maggior parte dei casi, questo processo viene gestito da un agente. La latenza aggiuntiva potrebbe essere introdotta dalla rete.
- Tempo della pipeline: il tempo necessario alla pipeline di inserimento per elaborare il record di log. Include l'analisi delle proprietà dell'evento e l'aggiunta potenziale di informazioni calcolate.
- Tempo di indicizzazione: il tempo impiegato per inserire un record di log nell'archivio per Big Data di Monitoraggio di Azure.

Di seguito sono riportati i dettagli sui diversi tipi di latenza introdotti in questo processo.

### <a name="agent-collection-latency"></a>Latenza di raccolta degli agenti
Gli agenti e le soluzioni di gestione usano diverse strategie per raccogliere i dati da una macchina virtuale, che possono influire sulla latenza. Ecco alcuni esempi specifici:

- Gli eventi Windows, gli eventi SysLog e le metriche delle prestazioni vengono raccolti immediatamente. Il polling dei contatori delle prestazioni Linux viene eseguito a intervalli di 30 secondi.
- I log di IIS e i log personalizzati vengono raccolti quando viene modificato il relativo timestamp. Per i log di IIS, questa frequenza dipende anche dalla [pianificazione di rollover configurata in IIS](../agents/data-sources-iis-logs.md). 
- La soluzione Replica Active Directory esegue la valutazione ogni cinque giorni, mentre la soluzione Valutazione Active Directory esegue una valutazione settimanale dell'infrastruttura Active Directory. L'agente raccoglierà questi log solo al termine della valutazione.

### <a name="agent-upload-frequency"></a>Frequenza di caricamento dell'agente
Per assicurarsi che l'agente di Log Analytics sia leggero, l'agente memorizza nel buffer i log e li carica periodicamente in Monitoraggio di Azure. La frequenza di caricamento varia tra 30 secondi e 2 minuti a seconda del tipo di dati. La maggior parte dei dati viene caricata in meno di 1 minuto. 

### <a name="network"></a>Rete
Le condizioni della rete possono influire negativamente sulla latenza di questi dati per raggiungere il punto di inserimento dei log di monitoraggio di Azure.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Log attività, log delle risorse e metriche di Azure
I dati di Azure aggiungono ulteriore tempo per diventare disponibile nel punto di inserimento dei log di monitoraggio di Azure per l'elaborazione:

- I log delle risorse in genere aggiungono 30-90 secondi, a seconda del servizio di Azure. Alcuni servizi di Azure, in particolare il database SQL di Azure e la rete virtuale di Azure, attualmente segnalano i log a intervalli di 5 minuti. Il lavoro è in corso per migliorare ulteriormente questa operazione. Vedere la [query più avanti](#checking-ingestion-time) per esaminare questa latenza nell'ambiente
- La metrica della piattaforma Azure richiede altri 3 minuti per l'esportazione nel punto di inserimento dei log di monitoraggio di Azure.
- Se si usa l'integrazione legacy, i dati del log attività potrebbero richiedere altri 10-15 minuti. Si consiglia di usare le impostazioni di diagnostica a livello di sottoscrizione per inserire i log attività nei log di monitoraggio di Azure, che comporta una latenza aggiuntiva di circa 30 secondi.

### <a name="management-solutions-collection"></a>Raccolta delle soluzioni di gestione
Alcune soluzioni non raccolgono i dati da un agente e possono usare un metodo di raccolta che introduce una latenza aggiuntiva. Alcune soluzioni raccolgono dati a intervalli regolari senza tentare di eseguire la raccolta quasi in tempo reale. Ecco alcuni esempi specifici:

- Microsoft 365 soluzione esegue il polling dei log attività usando l'API attività di gestione, che attualmente non fornisce garanzie di latenza quasi in tempo reale.
- I dati delle soluzioni di Windows Analytics, ad esempio Conformità aggiornamenti, vengono raccolti dalla soluzione con frequenza giornaliera.

Per determinare la frequenza di raccolta specifica, consultare la documentazione relativa a ciascuna soluzione.

### <a name="pipeline-process-time"></a>Tempo di elaborazione della pipeline

Una volta disponibile al punto di inserimento, i dati richiedono altri 30-60 secondi per essere disponibili per l'esecuzione di query.

Una volta che i record di log vengono inseriti nella pipeline di monitoraggio di Azure (come indicato nella proprietà [_TimeReceived](./log-standard-columns.md#_timereceived) ), vengono scritti nell'archiviazione temporanea per garantire l'isolamento dei tenant e per assicurarsi che i dati non vadano perduti. Questo processo richiede in genere altri 5-15 secondi. Alcune soluzioni di gestione implementano algoritmi più pesanti per aggregare i dati e derivare informazioni dettagliate mentre i dati sono in streaming. Ad esempio, Monitoraggio prestazioni rete aggrega i dati in ingresso a intervalli di 3 minuti, aggiungendo di fatto una latenza di 3 minuti. Un altro processo che aggiunge latenza è il processo che gestisce i log personalizzati. In alcuni casi, questo processo potrebbe aggiungere alcuni minuti di latenza ai log che vengono raccolti dai file dall'agente.

### <a name="new-custom-data-types-provisioning"></a>Provisioning di nuovi tipi di dati personalizzati
Quando viene creato un nuovo tipo di dati personalizzati da un [log personalizzato](../agents/data-sources-custom-logs.md) o dall' [API dell'agente di raccolta dati](../logs/data-collector-api.md), il sistema crea un contenitore di archiviazione dedicato. Questo sovraccarico è occasionale poiché si verifica solo alla prima occorrenza di questo tipo di dati.

### <a name="surge-protection"></a>Protezione in caso di picchi di dati
La principale priorità di Monitoraggio di Azure è quella di garantire che nessun dato del cliente vada perduto e pertanto il sistema dispone di una protezione predefinita in caso di picchi di dati. La protezione include buffer per assicurare che il sistema continui a funzionare anche in situazioni di carico elevato. In condizioni di carico normale, questi controlli aggiungono meno di un minuto ma, in caso di errori e condizioni eccezionali, possono aggiungere un tempo maggiore assicurando tuttavia la sicurezza dei dati.

### <a name="indexing-time"></a>Tempo di indicizzazione
Ogni piattaforma per Big Data prevede un equilibrio predefinito tra fornire funzionalità di analisi e di ricerca avanzata e fornire l'accesso immediato ai dati. Monitoraggio di Azure consente di eseguire query avanzate su miliardi di record e ottenere risultati in pochi secondi. Questo risultato è possibile in quanto l'infrastruttura trasforma i dati in modo significativo durante l'inserimento e li memorizza in strutture compatte uniche. Il sistema memorizza i dati finché non è disponibile una quantità sufficiente per creare queste strutture. Questa operazione deve essere completata prima che il record di log venga visualizzato nei risultati della ricerca.

Attualmente questo processo richiede circa 5 minuti in caso di un volume ridotto di dati, ma un tempo inferiore a velocità di trasferimento dati più elevate. Questo processo sembra illogico, ma consente l'ottimizzazione della latenza per carichi di lavoro in produzione con volumi elevati.



## <a name="checking-ingestion-time"></a>Controllo del tempo di inserimento dei dati
Il tempo di inserimento può variare a seconda delle risorse e delle circostanze. Per identificare il comportamento specifico dell'ambiente è possibile usare le query di log. La tabella seguente specifica come è possibile determinare le ore diverse per un record quando viene creato e inviato a monitoraggio di Azure.

| Passaggio | Proprietà o funzione | Commenti |
|:---|:---|:---|
| Record creato nell'origine dati | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Se l'origine dati non imposta questo valore, verrà impostato sulla stessa ora _TimeReceived. |
| Record ricevuto dall'endpoint di inserimento di monitoraggio di Azure | [_TimeReceived](./log-standard-columns.md#_timereceived) | |
| Record archiviato nell'area di lavoro e disponibile per le query | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Valori della latenza di inserimento
È possibile misurare la latenza di un record specifico confrontando il risultato della funzione [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) con la proprietà _TimeGenerated_ . Questi dati possono essere usati con varie aggregazioni per individuare il comportamento della latenza di inserimento. Esaminare qualche percentile del tempo di inserimento per ottenere informazioni dettagliate per una grande quantità di dati. 

Ad esempio, la query seguente indicherà i computer con il tempo di inserimento più elevato nelle 8 ore precedenti: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

I controlli percentile precedenti sono ideali per individuare le tendenze generali nella latenza. Per identificare un picco a breve termine nella latenza, l'utilizzo del valore massimo ( `max()` ) potrebbe essere più efficace.

Se si desidera eseguire il drill-down del tempo di inserimento per un computer specifico in un periodo di tempo, utilizzare la query seguente, che visualizza anche i dati del giorno precedente in un grafico: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Usare la query seguente per visualizzare il tempo di inserimento del computer in base al paese/area geografica in cui si trovano, in base all'indirizzo IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Tipi di dati diversi che hanno origine dall'agente potrebbero avere un tempo di latenza di inserimento differente. Le query precedenti possono quindi essere usate con altri tipi. Usare la query seguente per esaminare il tempo di inserimento dei vari servizi di Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Blocco delle risorse 
In alcuni casi, una risorsa potrebbe interrompere l'invio dei dati. Per comprendere se una risorsa sta inviando dati, controllare il record più recente che può essere identificato dal campo _TimeGenerated_ standard.  

Usare la tabella _Heartbeat_ per verificare la disponibilità di una macchina virtuale, poiché l'agente invia un heartbeat ogni minuto. Usare la query seguente per elencare il computer attivi che non hanno segnalato heartbeat di recente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Passaggi successivi
* Leggere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) per Monitoraggio di Azure.
