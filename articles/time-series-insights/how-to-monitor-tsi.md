---
title: Time Series Insights di monitoraggio | Microsoft Docs
description: Monitorare Time Series Insights per la disponibilità, le prestazioni e il funzionamento.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 3fc880e238c1c9f45c663975470a6ab57267a648
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879545"
---
# <a name="monitoring-time-series-insights"></a>Time Series Insights di monitoraggio

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da Time Series Insights e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La pagina **Panoramica** nel portale di Azure per ogni ambiente Time Series Insights include una breve visualizzazione dell'utilizzo delle risorse, ad esempio il numero di messaggi ricevuti e il numero di byte archiviati. Queste informazioni sono utili, ma da questo riquadro è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Che cos'è monitoraggio di Azure

Time Series Insights crea dati di monitoraggio tramite [monitoraggio di Azure](../azure-monitor/overview.md), un servizio di monitoraggio dello stack completo in Azure che fornisce un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale.

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md), che descrive i concetti seguenti:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo, descrivendo i dati specifici raccolti per Azure Time Series Insights. Queste sezioni forniscono anche esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

> [!TIP]
> Per comprendere i costi associati a monitoraggio di Azure, vedere [utilizzo e costi stimati](../azure-monitor/platform/usage-estimated-costs.md). Per informazioni sul tempo necessario per la visualizzazione dei dati in monitoraggio di Azure, vedere [tempo di inserimento dei dati di log](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Monitoraggio dei dati da Azure Time Series Insights

Azure Time Series Insights raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Vedere [Azure Time Series Insights riferimento ai dati di monitoraggio](how-to-monitor-tsi-reference.md) per un riferimento dettagliato dei log e delle metriche che è possibile raccogliere.

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma vengono raccolte e archiviate automaticamente, ma possono essere indirizzate ad altre posizioni usando un'impostazione di diagnostica.

I log delle risorse non vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e la si instrada a una o più posizioni.
Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../azure-monitor/platform/diagnostic-settings.md) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell. Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere.

È possibile raccogliere i log dalle seguenti categorie per Azure Time Series Insights:

   | Category | Descrizione |
   |---|---|
   | Dati in ingresso  | La categoria ingresso tiene traccia degli errori che si verificano nella pipeline di ingresso. Questa categoria include gli errori che si verificano durante la ricezione di eventi (ad esempio errori di connessione a un'origine evento) e l'elaborazione di eventi, ad esempio errori durante l'analisi di un payload dell'evento. |

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per Azure Time Series Insights, insieme alle metriche di altri servizi di Azure, aprendo le metriche dal menu monitoraggio di Azure. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../azure-monitor/platform/metrics-getting-started.md).

Per un elenco delle metriche della piattaforma raccolte, vedere [informazioni di riferimento sui dati di monitoraggio Azure Time Series Insights](how-to-monitor-tsi-reference.md#metrics)

Questo esempio mostra il numero di byte ricevuti da tutte le origini eventi nell'ambiente Azure Time Series Insights.

Byte **ricevuti in ingresso** ricevuti da [ ![ Azure Time Series in ingresso](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

Nell'esempio viene illustrato il numero di byte elaborati correttamente e disponibili per le query nell'ambiente Azure Time Series Insights.

Byte archiviati in **ingresso** in byte [ ![ archiviati in ingresso serie temporale di Azure](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Analisi dei log
È possibile accedere ai log delle risorse come BLOB in un account di archiviazione, come dati degli eventi o tramite query di Log Analytics.

I dati nei log di Monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche.

Tutti i log delle risorse in monitoraggio di Azure hanno gli stessi campi seguiti dai campi specifici del servizio. Lo schema comune è illustrato nello schema del [log delle risorse di monitoraggio di Azure](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Per un elenco dei tipi di log delle risorse raccolti per Azure Time Series Insights, vedere [Azure Time Series Insights riferimento ai dati di monitoraggio](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights archivia i dati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
| TSIIngress | Tabella che archivia i dati dalla categoria di ingresso. La categoria ingresso tiene traccia degli errori che si verificano nella pipeline di ingresso. Questa categoria include gli errori che si verificano durante la ricezione di eventi (ad esempio errori di connessione a un'origine evento) e l'elaborazione di eventi, ad esempio errori durante l'analisi di un payload dell'evento.

Per indirizzare i dati ai log di monitoraggio di Azure, è necessario creare un'impostazione di diagnostica per inviare i log delle risorse o le metriche della piattaforma a un'area di lavoro Log Analytics. Per altre informazioni, vedere [raccolta e routing](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate le query che è possibile usare per monitorare l'ambiente di Azure Time Series Insights:

+ Ottenere i dettagli sugli errori di connessione dell'origine eventi negli ultimi cinque giorni:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Ottenere i dettagli sui messaggi non validi ricevuti negli ultimi cinque giorni:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Avvisi

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi su [metriche](../azure-monitor/platform/alerts-metric-overview.md), [log](../azure-monitor/platform/alerts-unified-log.md)e [log attività](../azure-monitor/platform/activity-log-alerts.md). Tipi diversi di avvisi presentano vantaggi e svantaggi.

Quando si crea una regola di avviso basata sulle metriche della piattaforma, tenere presente che, per Time Series Insights metriche della piattaforma raccolte in unità di conteggio, alcune aggregazioni potrebbero non essere disponibili o utilizzabili.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Azure Time Series Insights riferimento ai dati di monitoraggio](how-to-monitor-tsi-reference.md) per un riferimento dei log e delle metriche creati da Azure Time Series Insights.
* Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md).