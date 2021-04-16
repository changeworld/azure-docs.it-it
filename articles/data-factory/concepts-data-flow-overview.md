---
title: Flussi di dati di mapping
description: Panoramica dei flussi di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/11/2021
ms.openlocfilehash: 26d119fb6880f4c539e0064db424699bc5d22f96
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478676"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapping dei flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

I flussi di dati di mapping sono trasformazioni dei dati progettate visivamente in Azure Data Factory. I flussi di dati consentono ai data engineer di sviluppare la logica di trasformazione dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno Azure Data Factory pipeline che usano cluster Apache Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere operative usando Azure Data Factory funzionalità di pianificazione, controllo, flusso e monitoraggio esistenti.

I flussi di dati di mapping offrono un'esperienza completamente visiva senza la necessità di scrivere codice. I flussi di dati vengono eseguiti in cluster di esecuzione gestiti da AdF per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutte le conversioni del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Introduzione

I flussi di dati vengono creati dal riquadro delle risorse della factory, ad esempio pipeline e set di dati. Per creare un flusso di dati, selezionare il segno più accanto a **Risorse factory** e quindi selezionare **Flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/new-data-flow.png)

Questa azione consente di accedere all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione di origine. Per altre informazioni, vedere [Trasformazione Origine.](data-flow-source.md)

## <a name="authoring-data-flows&quot;></a>Creazione di flussi di dati

Il flusso di dati di mapping ha un'area di disegno di creazione univoca progettata per semplificare la creazione della logica di trasformazione. L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Screenshot che mostra l'area di disegno del flusso di dati con la barra superiore, il grafico e il pannello di configurazione etichettati.](media/data-flow/canvas-1.png &quot;Canvas")

### <a name="graph"></a>Grafico

Il grafico visualizza il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine.** Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente. Altre informazioni su come gestire [il grafico del flusso di dati.](concepts-data-flow-manage-graph.md)

![Screenshot che mostra la parte del grafico dell'area di disegno con una casella di testo Cerca.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Pannello Di configurazione

Il pannello di configurazione mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione complessiva del flusso di dati è possibile aggiungere parametri tramite la **scheda** Parametri. Per altre informazioni, vedere [Mapping dei parametri del flusso di dati](parameters-data-flow.md).

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda nel riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche di tale trasformazione. Per altre informazioni, vedere la pagina della documentazione della trasformazione.

![Scheda Impostazioni origine](media/data-flow/source1.png "Scheda Impostazioni origine")

#### <a name="optimize"></a>Ottimizzazione

La **scheda Ottimizza** contiene le impostazioni per configurare gli schemi di partizionamento. Per altre informazioni su come ottimizzare i flussi di dati, vedere la guida alle prestazioni [del flusso di dati di mapping](concepts-data-flow-performance.md).

![Screenshot che mostra la scheda Ottimizza, che include l'opzione Partizione, Tipo di partizione e Numero di partizioni.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Controllare

La **scheda Inspect** (Ispeziona) fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti alle colonne. **Inspect** è una visualizzazione di sola lettura dei metadati. Non è necessario che sia abilitata la modalità di debug per visualizzare i metadati nel **riquadro Inspect (Ispeziona).**

![Controllare](media/data-flow/inspect1.png "Controllare")

Quando si modifica la forma dei dati tramite trasformazioni, il flusso delle modifiche ai metadati verrà visualizzato nel **riquadro Inspect (Ispeziona).** Se non è presente uno schema definito nella trasformazione di origine, i metadati non saranno visibili nel **riquadro Ispeziona.** La mancanza di metadati è comune negli scenari di deriva dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attivata, la scheda **Anteprima** dati offre uno snapshot interattivo dei dati a ogni trasformazione. Per altre informazioni, vedere [Anteprima dei dati in modalità di debug.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che influiscono sull'intero flusso di dati, ad esempio il salvataggio e la convalida. È anche possibile visualizzare il codice JSON sottostante e lo script del flusso di dati della logica di trasformazione. Per altre informazioni, vedere lo [script del flusso di dati](data-flow-script.md).

## <a name="available-transformations"></a>Trasformazioni disponibili

Visualizzare la panoramica [della trasformazione del flusso di dati di mapping](data-flow-transformation-overview.md) per ottenere un elenco delle trasformazioni disponibili.

## <a name="data-flow-data-types"></a>Tipi di dati del flusso di dati

array binary boolean complex decimal date float integer long map short string timestamp

## <a name="data-flow-activity"></a>Attività flusso di dati

I flussi di dati di mapping vengono operativi all'interno delle pipeline di AdF usando [l'attività flusso di dati](control-flow-execute-data-flow-activity.md). Un utente deve solo specificare quale runtime di integrazione usare e passare i valori dei parametri. Per altre informazioni, vedere Runtime di [integrazione di Azure.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Modalità di debug

La modalità di debug consente di visualizzare in modo interattivo i risultati di ogni passaggio di trasformazione durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia in durante la compilazione della logica del flusso di dati che durante l'esecuzione di esecuzioni di debug della pipeline con attività del flusso di dati. Per altre informazioni, vedere la documentazione [sulla modalità di debug](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitoraggio dei flussi di dati

Il flusso di dati di mapping si integra con le funzionalità di Azure Data Factory esistenti. Per informazioni su come comprendere l'output di monitoraggio del flusso di dati, vedere [Monitoraggio dei flussi di dati di mapping](concepts-data-flow-monitoring.md).

Il Azure Data Factory team ha creato [](concepts-data-flow-performance.md) una guida per l'ottimizzazione delle prestazioni che consente di ottimizzare il tempo di esecuzione dei flussi di dati dopo la compilazione della logica di business.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una trasformazione [di origine.](data-flow-source.md)
* Informazioni su come compilare i flussi di dati in [modalità di debug](concepts-data-flow-debug-mode.md).
