---
title: Flussi di dati
description: Panoramica dei flussi di dati in Azure sinapsi Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592671"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Flussi di dati in Azure sinapsi Analytics

## <a name="what-are-data-flows"></a>Che cosa sono i flussi di dati?

I flussi di dati sono progettati in modo visivo per le trasformazioni dei dati in Azure sinapsi Analytics. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di pipeline di analisi di Azure sinapsi che usano cluster di Apache Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere operative usando le funzionalità di pianificazione, controllo, flusso e monitoraggio esistenti di Azure sinapsi Analytics.

I flussi di dati forniscono un'esperienza visiva completamente senza la necessità di scrivere codice. I flussi di dati vengono eseguiti in cluster di esecuzione gestiti da sinapsi per l'elaborazione dei dati con scalabilità orizzontale. Azure sinapsi Analytics gestisce tutta la conversione del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Introduzione

I flussi di dati vengono creati dal riquadro sviluppo in sinapsi Studio. Per creare un flusso di dati, selezionare il segno più accanto a **sviluppa**, quindi selezionare **flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/new-data-flow.png)

Questa azione consente di passare all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione origine. Per ulteriori informazioni, vedere [trasformazione origine](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Creazione di flussi di dati

Il flusso di dati include un'area di disegno univoca progettata per semplificare la creazione della logica di trasformazione. L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Screenshot mostra l'area di disegno del flusso di dati con la barra superiore, il grafico e il pannello di configurazione con etichetta.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Grafico

Il grafico mostra il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine**. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente. Altre informazioni su come [gestire il grafico del flusso di dati](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Screenshot mostra la parte del grafico dell'area di disegno con una casella di testo di ricerca.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello configurazione Mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione del flusso di dati complessiva, è possibile aggiungere parametri tramite la scheda **parametri** . Per ulteriori informazioni, vedere [parametri del flusso di dati](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda del riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche della trasformazione. Per ulteriori informazioni, vedere la pagina della documentazione relativa alla trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source-1.png)

#### <a name="optimize"></a>Ottimizzazione

La scheda **ottimizza** contiene le impostazioni per la configurazione degli schemi di partizionamento. Per ulteriori informazioni su come ottimizzare i flussi di dati, vedere la [Guida alle prestazioni del flusso di dati di mapping](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Screenshot che mostra la scheda Ottimizza](media/data-flow/optimize.png)

#### <a name="inspect"></a>Controllare

La scheda **Controlla** fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti alle colonne. **Esaminare** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro di **controllo** .

![Controlla scheda](media/data-flow/inspect.png)

Quando si modifica la forma dei dati tramite le trasformazioni, il flusso delle modifiche dei metadati verrà visualizzato nel riquadro **Controlla** . Se non è presente uno schema definito nella trasformazione origine, i metadati non saranno visibili nel riquadro **ispezione** . La mancanza di metadati è comune negli scenari di drifting dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attiva, la scheda **Anteprima dati** fornisce uno snapshot interattivo dei dati a ogni trasformazione. Per altre informazioni, vedere [anteprima dei dati in modalità debug](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che interessano l'intero flusso di dati, ad esempio le impostazioni di convalida e debug. È possibile visualizzare anche il codice JSON sottostante e lo script del flusso di dati della logica di trasformazione.

## <a name="available-transformations"></a>Trasformazioni disponibili

Per ottenere un elenco delle trasformazioni disponibili, vedere [Cenni preliminari sulla trasformazione del flusso di dati](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="data-flow-activity"></a>Attività flusso di dati

I flussi di dati sono operativi in pipeline di analisi di Azure sinapsi usando l' [attività flusso di dati](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Tutti gli utenti devono specificare quale runtime di integrazione usare e passare i valori dei parametri. Per altre informazioni, vedere il [runtime di integrazione di Azure](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Modalità di debug

La modalità di debug consente di visualizzare in modo interattivo i risultati di ogni passaggio di trasformazione durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia in quando si compila la logica del flusso di dati che nell'esecuzione del debug della pipeline con le attività del flusso di dati. Per ulteriori informazioni, vedere la [documentazione relativa alla modalità di debug](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitoraggio dei flussi di dati

Il flusso di dati si integra con le funzionalità di monitoraggio di Azure sinapsi Analytics esistenti. Per informazioni su come comprendere l'output del monitoraggio del flusso di dati, vedere [monitoraggio dei flussi di dati del mapping](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Il team di analisi delle sinapsi di Azure ha creato una [Guida all'ottimizzazione delle prestazioni](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) che consente di ottimizzare il tempo di esecuzione dei flussi di dati dopo la creazione della logica di business.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una [trasformazione di origine](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Informazioni su come creare flussi di dati in [modalità debug](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
