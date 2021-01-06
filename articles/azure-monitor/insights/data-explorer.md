---
title: Monitoraggio di Azure per Esplora dati di Azure (anteprima) | Microsoft Docs
description: Questo articolo descrive Azure monitor Insights per i cluster Esplora dati di Azure.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6f25ff02dee16812898d77d0e801f927b354dc78
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918209"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Monitoraggio di Azure per Esplora dati di Azure (anteprima)

Monitoraggio di Azure per Azure Esplora dati (anteprima) offre un monitoraggio completo dei cluster grazie a una visualizzazione unificata delle prestazioni, delle operazioni, dell'utilizzo e degli errori del cluster.
Questo articolo consente di comprendere come caricare e usare monitoraggio di Azure per Azure Esplora dati (anteprima).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Introduzione a monitoraggio di Azure per Esplora dati di Azure (anteprima)

Prima di approfondire l'esperienza è necessario comprendere il modo in cui le informazioni vengono presentate e visualizzate.
-    **In una prospettiva con scalabilità** che mostra una visualizzazione snapshot delle metriche primarie del cluster, per tenere traccia delle prestazioni delle query, dell'inserimento e delle operazioni di esportazione.
-   Eseguire il **drill-down dell'analisi** di un cluster di Esplora dati di Azure specifico per consentire l'analisi dettagliata.
-    **Personalizzabile** , in cui è possibile modificare la metrica che si desidera visualizzare, modificare o impostare le soglie che si allineano ai limiti e salvare le cartelle di lavoro personalizzate. I grafici della cartella di lavoro possono essere aggiunti ai dashboard di Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Visualizzazione da monitoraggio di Azure (in scala)

Da monitoraggio di Azure è possibile visualizzare le metriche delle prestazioni principali per il cluster, incluse le metriche per le query, le operazioni di inserimento ed esportazione da più cluster nella sottoscrizione e consentono di identificare i problemi di prestazioni.

Per visualizzare le prestazioni dei cluster in tutte le sottoscrizioni, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Selezionare **monitoraggio** nel riquadro a sinistra nel portale di Azure e nella sezione Hub Insights selezionare **Azure Esplora dati Clusters (anteprima)**.

![Screenshot dell'esperienza di panoramica con più grafici](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Scheda Panoramica

Nella scheda **Panoramica** per la sottoscrizione selezionata la tabella Visualizza le metriche interattive per i cluster di Azure Esplora dati raggruppati nella sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate negli elenchi a discesa seguenti:

* Sottoscrizioni: sono elencate solo le sottoscrizioni con cluster Esplora dati di Azure.

* Cluster di Azure Esplora dati: per impostazione predefinita, solo fino a cinque cluster sono pre-selezionati. Se si selezionano tutti o più cluster nel selettore ambito, verranno restituiti fino a 200 cluster.

* Intervallo di tempo: per impostazione predefinita, vengono visualizzate le ultime 24 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro contatore, sotto l'elenco a discesa, esegue il rollup del numero totale di cluster Esplora dati di Azure nelle sottoscrizioni selezionate e riflette il numero di selezioni selezionate. Per le colonne sono disponibili codifiche dei colori condizionali: Mantieni attivo, CPU, utilizzo di inserimenti e utilizzo della cache. Le celle con codifica arancione hanno valori che non sono sostenibili per il cluster. 

Per comprendere meglio la rappresentazione di ogni metrica, è consigliabile leggere la documentazione relativa alle metriche di [Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Scheda prestazioni query

Questa scheda Mostra la durata della query, il numero totale di query simultanee e il numero totale di query limitate.

![Screenshot della scheda prestazioni query](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Scheda prestazioni inserimento

Questa scheda Mostra la latenza di inserimento, i risultati di inserimento riusciti, i risultati di inserimento non riusciti, il volume di inserimento e gli eventi elaborati per gli hub eventi/Internet.

[![Screenshot della scheda prestazioni inserimento](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Scheda prestazioni di inserimento flusso

In questa scheda vengono fornite informazioni sulla velocità media dei dati, sulla durata media e sulla frequenza delle richieste.

### <a name="export-performance-tab"></a>Scheda Esporta prestazioni

Questa scheda fornisce informazioni sui record esportati, la latenza, il conteggio in sospeso e la percentuale di utilizzo per le operazioni di esportazione continua.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Visualizzare da una risorsa cluster di Azure Esplora dati (drill-down Analysis)

Per accedere a monitoraggio di Azure per i cluster di Azure Esplora dati direttamente da un cluster di Azure Esplora dati:

1. Nella portale di Azure selezionare **cluster Esplora dati di Azure**.

2. Dall'elenco scegliere un cluster di Azure Esplora dati. Nella sezione monitoraggio scegliere **Insights (anteprima)**.

È anche possibile accedere a queste viste selezionando il nome della risorsa di un cluster di Azure Esplora dati nella visualizzazione di Azure monitor Insights.

Monitoraggio di Azure per Azure Esplora dati combina i log e le metriche per offrire una soluzione di monitoraggio globale. L'inclusione delle visualizzazioni basate su log richiede agli utenti di [abilitare la registrazione diagnostica del cluster Esplora dati di Azure e di inviarli a un'area di lavoro di log Analytics.](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) I log di diagnostica che devono essere abilitati sono: **Command**, **query**, **TableDetails** e **TableUsageStatistics**.

![Screenshot del pulsante blu che Visualizza il testo "Abilita log per il monitoraggio"](./media/data-explorer/enable-logs.png)


 La scheda **Panoramica** Mostra:

- I riquadri di metrica evidenziano la disponibilità e lo stato complessivo del cluster per valutarne rapidamente l'integrità.

- Riepilogo delle raccomandazioni di Active [Advisor](https://docs.microsoft.com/azure/data-explorer/azure-advisor) e dello stato di [integrità delle risorse](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health) .

- Grafici che mostrano i principali consumer di CPU e memoria e il numero di utenti univoci nel tempo.


[![Screenshot della visualizzazione da una risorsa cluster di Azure Esplora dati](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

La scheda **metrica chiave** Mostra una visualizzazione unificata di alcune metriche del cluster, raggruppate per: metriche generali, correlate alle query, correlate all'inserimento e metriche relative all'inserimento di flussi.

[![Screenshot della visualizzazione errori](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

La scheda **utilizzo** consente agli utenti di approfondire le prestazioni dei comandi e delle query del cluster. In questa pagina è possibile:
 
 - Vedere quali utenti e applicazioni inviano la maggior parte delle query o utilizzano la maggior parte della CPU e della memoria (per poter comprendere quali utenti inviano le query più pesanti per l'elaborazione del cluster).
 - Identificare gli utenti e le applicazioni principali in base alle query non riuscite.
 - Identificare le ultime modifiche apportate al numero di query rispetto alla media giornaliera cronologica (negli ultimi 16 giorni), in base all'utente e all'applicazione.
 - Identificare tendenze e picchi nel numero di query, memoria e utilizzo della CPU per utente, applicazione e tipo di comando.

[![Screenshot della visualizzazione delle operazioni con grafici ad anello dell'applicazione principale per numero di comandi e query, principali entità per comando e conteggio di query e comandi principali in base ai tipi di comando](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Screenshot della visualizzazione operazioni con grafici a linee del numero di query per applicazione, memoria totale per applicazione e CPU totale per applicazione](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

La scheda **tabelle** Mostra le proprietà più recenti e cronologiche delle tabelle nel cluster. È possibile vedere quali tabelle utilizzano la maggior parte dello spazio, tenere traccia della cronologia di crescita in base alle dimensioni della tabella, ai dati sensibili e al numero di righe nel tempo.

La scheda **cache** consente agli utenti di analizzare i modelli di ricerca di query effettivi e di confrontarli con i criteri di cache configurati (per ogni tabella). È possibile identificare le tabelle utilizzate dalla maggior parte delle query e tabelle per le quali non vengono eseguite query e adattare di conseguenza i criteri della cache. È possibile ottenere raccomandazioni specifiche sui criteri di cache per tabelle specifiche in Azure Advisor (attualmente, le raccomandazioni relative alla cache sono disponibili solo dal [dashboard Azure Advisor principale](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), in base alle query effettive riesaminate negli ultimi 30 giorni e un criterio di cache non ottimizzato per almeno il 95% delle query. Le raccomandazioni per la riduzione della cache in Azure Advisor sono disponibili per i cluster "delimitati da dati" (ovvero il cluster ha una CPU ridotta e un basso utilizzo di inserimento, ma a causa della capacità elevata dei dati, il cluster non è stato in grado di aumentare o ridurre il volume).

[![Schermata dei dettagli della cache](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Aggiungi al dashboard di Azure

È possibile aggiungere a un dashboard di Azure una delle sezioni della metrica, ovvero la prospettiva "scalabile", selezionando l'icona a puntina da disegno in alto a destra nella sezione.

![Screenshot dell'icona della puntina da disegno selezionata](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Personalizzare monitoraggio di Azure per il cluster di Esplora dati di Azure

Questa sezione illustra gli scenari comuni in cui è utile modificare la cartella di lavoro per personalizzarla a supporto delle proprie esigenze di analisi dei dati:
* Definire l'ambito della cartella di lavoro in modo da selezionare sempre una sottoscrizione specifica o un cluster di Azure Esplora dati
* Cambiare le metriche nella griglia
* Modificare le soglie o il rendering/codifica del colore

Per iniziare le personalizzazioni, abilitare la modalità di modifica selezionando il pulsante **Personalizza** sulla barra degli strumenti superiore.

![Screenshot del pulsante personalizza](./media/data-explorer/customize.png)

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per evitare la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, ovvero nella sezione Report personali privata o nella sezione Report condivisi accessibile a tutti gli utenti che hanno accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, per avviarla è necessario passare alla raccolta di cartelle di lavoro.

![Screenshot della raccolta di cartelle di lavoro](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per indicazioni generali sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei](troubleshoot-workbooks.md)problemi di Insights basato sulla cartella di lavoro.

Questa sezione illustra la diagnosi e la risoluzione dei problemi comuni che possono verificarsi quando si usa monitoraggio di Azure per Azure Esplora dati cluster (anteprima). Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Perché non è possibile vedere tutte le sottoscrizioni nel selettore di sottoscrizioni?

Vengono mostrate solo le sottoscrizioni che contengono cluster Esplora dati di Azure, scelti dal filtro della sottoscrizione selezionato, selezionati nella "directory + sottoscrizione" nell'intestazione portale di Azure.

![Screenshot del filtro di sottoscrizioni](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Perché non vengono visualizzati dati per il cluster di Azure Esplora dati nelle sezioni utilizzo, tabelle o cache?

Per visualizzare i dati basati su log, è necessario abilitare i [log di diagnostica](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) per ogni cluster di Azure Esplora dati che si vuole monitorare. Questa operazione può essere eseguita con le impostazioni di diagnostica per ogni cluster. Sarà necessario inviare i dati a un'area di lavoro Log Analytics. I log di diagnostica che devono essere abilitati sono: Command, query, TableDetails e TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Sono già stati abilitati i log per il cluster Azure Esplora dati, perché I dati non sono ancora visibili in comandi e query?

Attualmente, i log di diagnostica non funzionano in modo retroattivo, quindi i dati verranno visualizzati solo dopo che sono state intraprese le azioni per la Esplora dati di Azure. Per questo motivo, l'intervallo di tempo può richiedere alcuni minuti, a seconda della modalità di attivazione del cluster di Azure Esplora dati.


## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare nuovi report e personalizzare report esistenti e altro ancora sono disponibili nell'articolo [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../platform/workbooks-overview.md).
