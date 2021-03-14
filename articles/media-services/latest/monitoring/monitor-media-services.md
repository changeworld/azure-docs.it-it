---
title: Monitoraggio di servizi multimediali
description: Iniziare da qui per informazioni su come monitorare servizi multimediali
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/12/2021
ms.openlocfilehash: 895c674fdbe5c413b68fe12862e7846f1eb7bd0b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464499"
---
# <a name="monitor-media-services"></a>Monitorare servizi multimediali

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da servizi multimediali e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

## <a name="metrics-are-useful"></a>Le metriche sono utili

Di seguito sono riportati alcuni esempi di come le metriche di monitoraggio di servizi multimediali possano aiutarti a comprendere le prestazioni delle tue app. Di seguito sono riportate alcune domande che possono essere risolte con le metriche di servizi multimediali:

- Ricerca per categorie monitorare l'endpoint di streaming standard per capire quando sono stati superati i limiti?
- Ricerca per categorie sa se si dispone di un numero sufficiente di unità di scala di endpoint di streaming Premium?
- Come è possibile impostare un avviso per stabilire quando aumentare la scalabilità verticale degli endpoint di streaming?
- Ricerca per categorie impostare un avviso per stabilire quando è stato raggiunto il numero massimo di uscite configurato per l'account?
- Come è possibile visualizzare la suddivisione delle richieste in errore e la causa dell'errore?
- Come è possibile vedere il numero di richieste HLS o DASH da estrarre dal pacchetto?
- Ricerca per categorie impostare un avviso per capire quando si raggiunge il valore soglia delle richieste non riuscite?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Servizi multimediali consente di creare dati di monitoraggio tramite [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview), un servizio di monitoraggio completo dello stack in Azure che offre un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale.

Per iniziare, leggere l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), che descrive i concetti seguenti:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

## <a name="monitoring-data"></a>Dati di monitoraggio

Servizi multimediali raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno di due tipi fondamentali: metriche e log. Con questi due tipi è possibile:

- Visualizzare e analizzare i dati di metrica usando Esplora metriche.
- Monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche.
- Con i log è possibile:
  - Inviarli ad archiviazione di Azure
  - Eseguire lo streaming in hub eventi di Azure
  - Esportarli in Log Analytics
  - Usare servizi di terze parti

Per informazioni dettagliate sulle metriche di metrica e log create da servizi multimediali, vedere l'articolo [monitoraggio di riferimento ai dati di servizi multimediali](monitor-media-services-data-reference.md) .

## <a name="collection-and-routing"></a>Raccolta e routing

Le *metriche della piattaforma* e il *log attività* vengono raccolti e archiviati automaticamente, ma possono essere indirizzati ad altri percorsi tramite un'impostazione di diagnostica.  

I *log delle risorse* **non** vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e la si instrada a una o più posizioni.

Vedere l'articolo [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) per il processo dettagliato di creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere. Le categorie per servizi multimediali sono elencate in [informazioni di riferimento sui dati di monitoraggio di servizi multimediali](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per servizi multimediali con le metriche di altri servizi di Azure usando Esplora metriche aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started).

Per un elenco delle metriche raccolte per servizi multimediali, vedere informazioni di [riferimento sui dati di servizi multimediali di monitoraggio](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Analisi dei log

I dati nei log di monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche.  

Tutti i log delle risorse in monitoraggio di Azure hanno gli stessi campi seguiti dai campi specifici del servizio. Lo schema comune è illustrato nello schema del [log delle risorse di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema).

Lo schema per i log delle risorse di servizi multimediali si trova nel [monitoraggio di riferimento ai dati di servizi multimediali](monitor-media-services-data-reference.md).

Il [log attività](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) è un log di piattaforma in Azure che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione. È possibile visualizzarlo in modo indipendente o instradarlo ai log di monitoraggio di Azure, in cui è possibile eseguire query molto più complesse usando Log Analytics.

Per un elenco dei tipi di log delle risorse raccolti per servizi multimediali, vedere [informazioni di riferimento sui dati di servizi multimediali di monitoraggio](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Perché si vogliono usare I log di diagnostica?

Di seguito sono riportate alcune informazioni che è possibile esaminare con i log di diagnostica:

- Il numero di licenze recapitate dal tipo di DRM.
- Il numero di licenze recapitate dai criteri.
- Errori per DRM o tipo di criteri.
- Numero di richieste di licenza non autorizzate dai client.

## <a name="alerts"></a>Avvisi

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi su [metriche](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)e [log attività](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts).

Le metriche di servizi multimediali vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per gli avvisi perché possono essere campionati di frequente. Un avviso può essere generato rapidamente con una logica relativamente semplice.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
