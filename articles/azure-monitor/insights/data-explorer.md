---
title: Esplora dati di Azure Insights (anteprima di ADX Insights)| Microsoft Docs
description: Questo articolo descrive Esplora dati di Azure Insights (ADX Insights)
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: a8aae2dc03ba87e9782cdf3952be1bfc4a1aae75
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767042"
---
# <a name="azure-data-explorer-insights-preview"></a>Esplora dati di Azure Insights (anteprima)

Esplora dati di Azure Insights (anteprima) offre un monitoraggio completo dei cluster offrendo una visualizzazione unificata delle prestazioni, delle operazioni, dell'utilizzo e degli errori del cluster.
Questo articolo consente di comprendere come eseguire l'onboarder e usare Esplora dati di Azure Insights (anteprima).

## <a name="introduction-to-azure-data-explorer-insights-preview"></a>Introduzione a Esplora dati di Azure Insights (anteprima)

Prima di approfondire l'esperienza è necessario comprendere il modo in cui le informazioni vengono presentate e visualizzate.
-    **Prospettiva su larga scala** che mostra una visualizzazione snapshot delle metriche primarie dei cluster, per tenere facilmente traccia delle prestazioni delle query, dell'inserimento e delle operazioni di esportazione.
-   **Eseguire il drill-down** di un cluster Esplora dati di Azure specifico per eseguire un'analisi dettagliata.
-    **Personalizzabile** in cui è possibile modificare le metriche da visualizzare, modificare o impostare soglie in linea con i limiti e salvare cartelle di lavoro personalizzate. I grafici della cartella di lavoro possono essere aggiunti ai dashboard di Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Visualizzazione da Monitoraggio di Azure (prospettiva su larga scala)

Da Monitoraggio di Azure è possibile visualizzare le metriche delle prestazioni principali per il cluster, incluse le metriche per le query, l'inserimento e le operazioni di esportazione da più cluster nella sottoscrizione e identificare i problemi di prestazioni.

Per visualizzare le prestazioni dei cluster in tutte le sottoscrizioni, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Selezionare **Monitoraggio** nel riquadro sinistro del portale di Azure e nella sezione Hub di Insights selezionare Esplora dati di Azure **cluster (anteprima).**

![Screenshot dell'esperienza di panoramica con più grafici](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Scheda Panoramica

Nella scheda **Panoramica** della sottoscrizione selezionata la tabella visualizza le metriche interattive per i cluster Esplora dati di Azure raggruppati all'interno della sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate negli elenchi a discesa seguenti:

* Sottoscrizioni: vengono elencate solo le sottoscrizioni Esplora dati di Azure cluster.

* Esplora dati di Azure cluster: per impostazione predefinita, solo un massimo di cinque cluster è prese selezionato. Se si selezionano tutti o più cluster nel selettore di ambito, verranno restituiti fino a 200 cluster.

* Intervallo di tempo: per impostazione predefinita, vengono visualizzate le ultime 24 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore, nell'elenco a discesa, esegue il rollup del numero totale di cluster Esplora dati di Azure nelle sottoscrizioni selezionate e riflette il numero di cluster selezionati. Sono disponibili codifica a colori condizionali per le colonne: Keep alive, CPU, Ingestion Utilization e Cache Utilization. Le celle con codice arancione hanno valori non sostenibile per il cluster. 

Per comprendere meglio il valore rappresentato da ognuna delle metriche, è consigliabile leggere la documentazione [Esplora dati di Azure metriche](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Scheda Prestazioni query

Questa scheda mostra la durata della query, il numero totale di query simultanee e il numero totale di query limitate.

![Screenshot della scheda Prestazioni query](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Scheda Prestazioni inserimento

Questa scheda mostra la latenza di inserimento, i risultati dell'inserimento riusciti, i risultati dell'inserimento non riusciti, il volume di inserimento e gli eventi elaborati per gli hub Eventi/IoT.

[![Screenshot della scheda delle prestazioni di inserimento](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Scheda Streaming Ingest Performance (Prestazioni inserimento streaming)

Questa scheda fornisce informazioni sulla velocità media dei dati, sulla durata media e sulla frequenza delle richieste.

### <a name="export-performance-tab"></a>Scheda Esporta prestazioni

Questa scheda fornisce informazioni su record esportati, latenza, conteggio in sospeso e percentuale di utilizzo per le operazioni di esportazione continua.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Visualizzare da una risorsa cluster Esplora dati di Azure (analisi drill-down)

Per accedere Esplora dati di Azure Insights direttamente da un cluster Esplora dati di Azure:

1. Nel portale di Azure selezionare Esplora dati di Azure **cluster**.

2. Nell'elenco scegliere un cluster Esplora dati di Azure cluster. Nella sezione monitoraggio scegliere Informazioni **dettagliate (anteprima).**

Queste viste sono accessibili anche selezionando il nome della risorsa di un cluster Esplora dati di Azure dall'interno della Monitoraggio di Azure informazioni dettagliate.

Esplora dati di Azure Insights combina log e metriche per fornire una soluzione di monitoraggio globale. L'inclusione di visualizzazioni basate su log richiede agli utenti di abilitare la registrazione diagnostica Esplora dati di Azure cluster e di [inviarle a un'area di lavoro Log Analytics.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) I log di diagnostica che devono essere abilitati sono: **Command**, **Query**, **TableDetails** e **TableUsageStatistics**.

![Screenshot del pulsante blu che visualizza il testo "Abilita log per il monitoraggio"](./media/data-explorer/enable-logs.png)


 La **scheda Panoramica** mostra:

- Riquadri metriche che evidenziano la disponibilità e lo stato complessivo del cluster per valutarne rapidamente l'integrità.

- Riepilogo delle raccomandazioni di [Advisor attive e](/azure/data-explorer/azure-advisor) dello stato di integrità [delle](/azure/data-explorer/monitor-with-resource-health) risorse.

- Grafici che mostrano i principali consumer di CPU e memoria e il numero di utenti univoci nel tempo.


[![Screenshot della visualizzazione da una risorsa Esplora dati di Azure cluster](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

La **scheda** Metriche chiave mostra una visualizzazione unificata di alcune metriche del cluster, raggruppate per: metriche generali, correlate alle query, correlate all'inserimento e metriche correlate all'inserimento di streaming.

[![Screenshot della visualizzazione errori](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

La **scheda Utilizzo** consente agli utenti di approfondire le prestazioni dei comandi e delle query del cluster. In questa pagina è possibile:
 
 - Vedere quali gruppi di carico di lavoro, utenti e applicazioni inviano il maggior numero di query o utilizzano la maggior parte della CPU e della memoria(in modo da poter comprendere quali carichi di lavoro inviano le query più pesanti per il cluster da elaborare).
 - Identificare i principali gruppi di carico di lavoro, utenti e applicazioni in base a query non riuscite.
 - Identificare le modifiche recenti nel numero di query, rispetto alla media giornaliera cronologica (negli ultimi 16 giorni), in base al gruppo di carico di lavoro, all'utente e all'applicazione.
 - Identificare tendenze e picchi nel numero di query, memoria e utilizzo della CPU per gruppo di carico di lavoro, utente, applicazione e tipo di comando.

[![Screenshot della visualizzazione delle operazioni con grafici ad anello dell'applicazione principale in base al numero di comandi e query, principali entità per numero di comandi e query e comandi principali per tipi di comando](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Screenshot della visualizzazione delle operazioni con grafici a linee del numero di query per applicazione, memoria totale per applicazione e CPU totale per applicazione](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

La **scheda tabelle** mostra le proprietà più recenti e cronologiche delle tabelle nel cluster. È possibile visualizzare le tabelle che utilizzano la maggior parte dello spazio, tenere traccia della cronologia di crescita in base alle dimensioni della tabella, ai dati ad accesso elevato e al numero di righe nel tempo.

La **scheda cache** consente agli utenti di analizzare i modelli di ricerca delle query effettive e confrontarli con i criteri di cache configurati (per ogni tabella). È possibile identificare le tabelle usate dalla maggior parte delle query e delle tabelle su cui non viene eseguita una query e adattare di conseguenza i criteri di cache. È possibile ottenere raccomandazioni specifiche per i criteri di cache in tabelle specifiche in Azure Advisor (attualmente le raccomandazioni per la cache sono disponibili solo dal [dashboard principale di Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)), in base alle query effettive negli ultimi 30 giorni e a un criterio di cache non ottimizzato per almeno il 95% delle query. Le raccomandazioni per la riduzione della cache in Azure Advisor sono disponibili per i cluster "vincolati dai dati", vale a dire che il cluster ha un utilizzo ridotto della CPU e un basso utilizzo di inserimento, ma a causa della capacità dei dati elevata, il cluster non è stato in grado di ridurre o ridurre le prestazioni.

[![Screenshot dei dettagli della cache](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

La **scheda limiti del cluster** visualizza i limiti del cluster in base all'utilizzo. In questa scheda è possibile esaminare l'utilizzo della CPU, dell'inserimento e della cache. Queste metriche vengono classificate come "Bassa", "Media" o "Alta". Queste metriche e punteggi sono importanti quando si decide lo SKU ottimale e il numero di istanze per il cluster e vengono presi in considerazione Azure Advisor SKU/dimensioni consigliati. In questa scheda è possibile selezionare un riquadro della metrica e un approfondimento per comprenderne la tendenza e come viene deciso il punteggio. È anche possibile visualizzare la raccomandazione Azure Advisor SKU/dimensioni per il cluster. Ad esempio, nell'immagine seguente è possibile vedere che tutte le metriche vengono classificate come "Low" e quindi il cluster riceve una raccomandazione sui costi che consente di ridurre o ridurre i costi.

> [!div class="mx-imgBorder"]
> [![Screenshot dei limiti del cluster.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard&quot;></a>Aggiungere al dashboard di Azure

È possibile aggiungere qualsiasi sezione della metrica (della prospettiva &quot;su larga scala") a un dashboard di Azure selezionando l'icona della puntina da disegno in alto a destra nella sezione.

![Screenshot dell'icona della puntina da disegno selezionata](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Personalizzare Esplora dati di Azure Insights

Questa sezione illustra gli scenari comuni in cui è utile modificare la cartella di lavoro per personalizzarla a supporto delle proprie esigenze di analisi dei dati:
* Ambito della cartella di lavoro per selezionare sempre una sottoscrizione o Esplora dati di Azure cluster specifici
* Cambiare le metriche nella griglia
* Modificare le soglie o il rendering/codifica dei colori

Per iniziare le personalizzazioni, abilitare la modalità di modifica selezionando il pulsante **Personalizza** sulla barra degli strumenti superiore.

![Screenshot del pulsante personalizza](./media/data-explorer/customize.png)

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per evitare la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, ovvero nella sezione Report personali privata o nella sezione Report condivisi accessibile a tutti gli utenti che hanno accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, per avviarla è necessario passare alla raccolta di cartelle di lavoro.

![Screenshot della raccolta di cartelle di lavoro](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per indicazioni generali sulla risoluzione dei problemi, vedere l'articolo dedicato sulla risoluzione dei problemi delle informazioni dettagliate basate [su cartelle di lavoro.](troubleshoot-workbooks.md)

Questa sezione consente di diagnosticare e risolvere alcuni dei problemi comuni che possono verificarsi quando si usa Esplora dati di Azure Insights (anteprima). Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Perché non è possibile vedere tutte le sottoscrizioni nel selettore di sottoscrizioni?

Vengono mostrate solo le sottoscrizioni che contengono cluster Esplora dati di Azure, scelti dal filtro sottoscrizioni selezionato, che vengono selezionate in "Directory e sottoscrizione" nell'intestazione portale di Azure sottoscrizione.

![Screenshot del filtro di sottoscrizioni](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Perché non vengono visualizzati dati per il cluster Esplora dati di Azure nelle sezioni Utilizzo, Tabelle o Cache?

Per visualizzare i dati basati sui [](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) log, è necessario abilitare i log di diagnostica per ognuno dei cluster Esplora dati di Azure da monitorare. Questa operazione può essere eseguita nelle impostazioni di diagnostica per ogni cluster. Sarà necessario inviare i dati a un'area di lavoro Log Analytics. I log di diagnostica che devono essere abilitati sono: Command, Query, TableDetails e TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Sono già stati abilitati i log per il cluster Esplora dati di Azure, perché non è ancora possibile visualizzare i dati in Comandi e query?

Attualmente, i log di diagnostica non funzionano retroattivamente, quindi i dati inizieranno a essere visualizzati solo dopo che sono state eseguite azioni al Esplora dati di Azure. Di conseguenza, potrebbe essere necessario del tempo, che varia da ore a un giorno, a seconda di quanto è attivo Esplora dati di Azure cluster.


## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare nuovi report e personalizzare report esistenti e altro ancora sono disponibili nell'articolo [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../visualize/workbooks-overview.md).
