---
title: Riferimento ai dati di Azure Time Series Insights di monitoraggio | Microsoft Docs
description: Documentazione di riferimento per il monitoraggio Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591376"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Riferimento ai dati di monitoraggio Azure Time Series Insights

Informazioni sui dati e sulle risorse raccolti da monitoraggio di Azure dall'ambiente Azure Time Series Insights. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio, vedere [Time Series Insights di monitoraggio]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Metriche

Questa sezione elenca tutte le metriche della piattaforma raccolte automaticamente raccolte per Azure Time Series Insights. Per un elenco di tutte le metriche di supporto di monitoraggio di Azure (incluse Azure Time Series Insights), vedere [metriche supportate di monitoraggio di Azure](../azure-monitor/essentials/metrics-supported.md). Il provider di risorse per queste metriche è [Microsoft. TimeSeriesInsights/environments/EventSources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) e [Microsoft. TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Dati in ingresso
 
|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|

### <a name="storage"></a>Archiviazione

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|

## <a name="resource-logs"></a>Log risorse

Questa sezione elenca i tipi di log delle risorse che è possibile raccogliere per l'ambiente Azure Time Series Insights.

| Category | Nome visualizzato | Descrizione |
|----- |----- |----- |
| Dati in ingresso | TSIIngress | La categoria ingresso tiene traccia degli errori che si verificano nella pipeline di ingresso. Questa categoria include gli errori che si verificano durante la ricezione di eventi (ad esempio errori di connessione a un'origine evento) e l'elaborazione di eventi, ad esempio errori durante l'analisi di un payload dell'evento. |

## <a name="schemas"></a>Schemi
Gli schemi seguenti sono utilizzati da Azure Time Series Insights

### <a name="tsiingress-table"></a>Tabella TSIIngress

| Proprietà | Descrizione |
|----- |----- |
| TimeGenerated | Ora (UTC) in cui viene generato questo evento. |
| Località | Il percorso della risorsa. |
| Category | Categoria dell'evento del log. |
| OperationName | Nome dell'operazione dell'evento. |
| CorrelationId | ID di correlazione della richiesta. |
| Level | Il livello di gravità dell'evento. |
| ResultDescription | Descrizione del risultato dell'operazione, ad esempio "errore non consentito ricevuto". |
| Message | Messaggio associato all'errore. Include informazioni dettagliate sugli errori e su come attenuare l'errore. |
| ErrorCode | Codice associato all'errore. |
| EventSourceType | Tipo di origine evento. Potrebbe trattarsi di hub eventi o dell'hub Internet. |
| EventSourceProperties | Raccolta di proprietà specifiche dell'origine evento. Contiene dettagli quali il gruppo di consumer e il nome della chiave di accesso. |
