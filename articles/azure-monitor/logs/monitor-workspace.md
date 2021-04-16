---
title: Monitorare l'integrità dell'area di lavoro Log Analytics in Monitoraggio di Azure
description: Descrive come monitorare l'integrità dell'area di lavoro Log Analytics usando i dati nella tabella Operazione.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538851"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorare l'integrità dell'area di lavoro Log Analytics in Monitoraggio di Azure
Per mantenere le prestazioni e la disponibilità dell'area di lavoro Log Analytics in Monitoraggio di Azure, è necessario essere in grado di rilevare in modo proattivo eventuali problemi che si verificano. Questo articolo descrive come monitorare l'integrità dell'area di lavoro Log Analytics usando i dati nella [tabella](/azure/azure-monitor/reference/tables/operation) Operazione. Questa tabella è inclusa in ogni area di lavoro Log Analytics e contiene gli errori e gli avvisi che si verificano nell'area di lavoro. È consigliabile esaminare regolarmente questi dati e creare avvisi per ricevere notifiche proattive in caso di eventi imprevisti importanti nell'area di lavoro.

## <a name="_logoperation-function"></a>_LogOperation funzione

Monitoraggio di Azure log invia informazioni dettagliate su eventuali problemi alla tabella [Operazione](/azure/azure-monitor/reference/tables/operation) nell'area di lavoro in cui si è verificato il problema. La **_LogOperation** di sistema è basata sulla tabella **Operation** e fornisce un set semplificato di informazioni per l'analisi e gli avvisi.

## <a name="columns"></a>Colonne

La **_LogOperation** restituisce le colonne nella tabella seguente.

| Colonna | Descrizione |
|:---|:---|
| TimeGenerated | Ora in cui si è verificato l'evento imprevisto in formato UTC. |
| Category  | Gruppo di categorie dell'operazione. Può essere usato per filtrare in base ai tipi di operazioni e creare avvisi e controllo del sistema più precisi. Vedere la sezione seguente per un elenco di categorie. |
| Operazione  | Descrizione del tipo di operazione. Ciò può indicare uno dei limiti di Log Analytics, il tipo di operazione o parte di un processo. |
| Level | Livello di gravità del problema:<br>- Info: non è necessaria alcuna attenzione specifica.<br>- Avviso: il processo non è stato completato come previsto ed è necessaria attenzione.<br>- Errore: il processo non è riuscito ed è necessaria attenzione urgente. 
| Dettaglio | La descrizione dettagliata dell'operazione include un messaggio di errore specifico, se esistente. |
| _ResourceId | ID risorsa della risorsa di Azure correlata all'operazione.  |
| Computer | Nome computer se l'operazione è correlata a un Monitoraggio di Azure agente. |
| CorrelationId | Consente di raggruppare operazioni correlate consecutive. |


## <a name="categories"></a>Categorie

Nella tabella seguente vengono descritte le categorie della _LogOperation funzione . 

| Category | Descrizione |
|:---|:---|
| Ingestion           | Operazioni che fanno parte del processo di inserimento dati. Vedere di seguito per altri dettagli. |
| Agente               | Indica un problema relativo all'installazione dell'agente. |
| Raccolta dati     | Operazioni correlate ai processi di raccolta dati. |
| Destinazione della soluzione  | L'operazione di *tipo ConfigurationScope* è stata elaborata. |
| Soluzione di valutazione | È stato eseguito un processo di valutazione. |


### <a name="ingestion"></a>Ingestion
Le operazioni di inserimento sono problemi che si sono verificati durante l'inserimento dei dati, inclusa la notifica sul raggiungimento dei limiti dell'area di lavoro Log Analytics di Azure. Le condizioni di errore in questa categoria potrebbero suggerire la perdita di dati, quindi sono particolarmente importanti da monitorare. La tabella seguente fornisce informazioni dettagliate su queste operazioni. Vedere [Monitoraggio di Azure dei servizi per](../service-limits.md#log-analytics-workspaces) i limiti del servizio per le aree di lavoro Log Analytics.


| Operazione | Level | Dettaglio | Articolo correlato |
|:---|:---|:---|:---|
| Log personalizzato | Errore   | Raggiunto il limite di colonne dei campi personalizzati. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Log personalizzato | Errore   | L'inserimento di log personalizzati non è riuscito. | |
| Metadata. | Errore | Errore di configurazione rilevato. | |
| Raccolta dati | Errore   | I dati sono stati eliminati perché la richiesta è stata creata prima del numero di giorni impostati. | [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Raccolta dati | Info    | È stata rilevata la configurazione del computer di raccolta.| |
| Raccolta dati | Info    | La raccolta dei dati è stata avviata a causa di un nuovo giorno. | [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Raccolta dati | Avviso | La raccolta dati è stata arrestata a causa del limite giornaliero raggiunto.| [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Elaborazione dati | Errore   | Formato JSON non valido. | [Inviare dati di log a Monitoraggio di Azure con l'API di raccolta dati HTTP (anteprima pubblica)](../logs/data-collector-api.md#request-body) | 
| Elaborazione dati | Avviso | Il valore è stato tagliato alle dimensioni massime consentite. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Elaborazione dati | Avviso | Valore del campo tagliato come limite di dimensioni raggiunto. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) | 
| Frequenza di inserimento | Info | Limite della frequenza di inserimento che si avvicina al 70%. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Frequenza di inserimento | Avviso | Limite della frequenza di inserimento che si avvicina al limite. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Frequenza di inserimento | Errore   | Limite di frequenza raggiunto. | [Limiti del servizio Monitoraggio di Azure](../service-limits.md#log-analytics-workspaces) |
| Archiviazione | Errore   | Impossibile accedere all'account di archiviazione poiché le credenziali usate non sono valide.  |



   

## <a name="alert-rules"></a>Regole di avviso
Usare [gli avvisi delle query](../alerts/alerts-log-query.md) di log Monitoraggio di Azure ricevere notifiche proattive quando viene rilevato un problema nell'area di lavoro Log Analytics. È consigliabile usare una strategia che consenta di rispondere in modo opportuno ai problemi riducendo al minimo i costi. La sottoscrizione viene addebitata per ogni regola di avviso con un costo a seconda della frequenza con cui viene valutata.

Una strategia consigliata consiste nell'iniziare con due regole di avviso in base al livello del problema. Usare una frequenza breve, ad esempio ogni 5 minuti per Errori e una frequenza più lunga, ad esempio 24 ore per Gli avvisi. Poiché gli errori indicano una potenziale perdita di dati, è necessario rispondere rapidamente per ridurre al minimo eventuali perdite. Gli avvisi indicano in genere un problema che non richiede attenzione immediata, pertanto è possibile esaminarli ogni giorno.

Usare il processo in [Creare, visualizzare e gestire gli](../alerts/alerts-log.md) avvisi del log usando Monitoraggio di Azure creare le regole di avviso del log. Le sezioni seguenti descrivono i dettagli per ogni regola.


| Query | Valore soglia | Periodo | Frequenza |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Queste regole di avviso risponderanno allo stesso modo a tutte le operazioni con errore o avviso. Quando si ha familiarità con le operazioni che generano avvisi, è possibile rispondere in modo diverso per operazioni specifiche. Ad esempio, è possibile inviare notifiche a persone diverse per operazioni specifiche. 

Per creare una regola di avviso per un'operazione specifica, usare una query che includa le **colonne Categoria** **e** Operazione . 

L'esempio seguente crea un avviso quando la frequenza del volume di inserimento raggiunge l'80% del limite.

- Destinazione: selezionare l'area di lavoro Log Analytics
- Criteri:
  - Nome segnale: Ricerca log personalizzata
  - Query di ricerca: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - In base a: Numero di risultati
  - Condizione: Maggiore di
  - Soglia: 0
  - Periodo: 5 (minuti)
  - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)


Nell'esempio seguente viene creato un avviso quando la raccolta di dati ha raggiunto il limite giornaliero. 

- Destinazione: selezionare l'area di lavoro Log Analytics
- Criteri:
  - Nome segnale: Ricerca log personalizzata
  - Query di ricerca: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - In base a: Numero di risultati
  - Condizione: Maggiore di
  - Soglia: 0
  - Periodo: 5 (minuti)
  - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [avvisi di log.](../alerts/alerts-log.md)
- [Raccogliere i dati di controllo delle query per](./query-audit.md) l'area di lavoro.
