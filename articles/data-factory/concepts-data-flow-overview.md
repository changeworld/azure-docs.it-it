---
title: Flussi di dati di mapping
description: Panoramica del mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/14/2021
ms.openlocfilehash: 826183e09f2aa7f3f22ace8b5ce3e16767d49863
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515660"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapping dei flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

I flussi di dati di mapping sono trasformazioni dei dati progettate visivamente in Azure Data Factory. I flussi di dati consentono ai data engineer di sviluppare logica di trasformazione dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno Azure Data Factory pipeline che usano cluster con scalabilità orizzontale Apache Spark cluster. Le attività del flusso di dati possono essere operative usando Azure Data Factory di pianificazione, controllo, flusso e monitoraggio esistenti.

I flussi di dati di mapping offrono un'esperienza completamente visiva senza la necessità di scrivere codice. I flussi di dati vengono eseguiti in cluster di esecuzione gestiti da AdF per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutte le conversioni del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Introduzione

I flussi di dati vengono creati dal riquadro delle risorse della factory, ad esempio pipeline e set di dati. Per creare un flusso di dati, selezionare il segno più accanto a **Risorse** di fabbrica e quindi selezionare **Flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/new-data-flow.png)

Questa azione consente di accedere all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione di origine. Per altre informazioni, vedere [Trasformazione origine](data-flow-source.md).

## <a name="authoring-data-flows&quot;></a>Creazione di flussi di dati

Il flusso di dati di mapping ha un'area di disegno di creazione univoca progettata per semplificare la creazione della logica di trasformazione. L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafo e il pannello di configurazione. 

![Screenshot che mostra l'area di disegno del flusso di dati con la barra superiore, il grafico e il pannello di configurazione etichettati.](media/data-flow/canvas-1.png &quot;Canvas")

### <a name="graph"></a>Grafico

Il grafico visualizza il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine.** Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente. Altre informazioni su come gestire [il grafico del flusso di dati.](concepts-data-flow-manage-graph.md)

![Screenshot che mostra la parte del grafico dell'area di disegno con una casella di testo Cerca.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello di configurazione mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione generale del flusso di dati è possibile aggiungere parametri tramite la **scheda** Parametri. Per altre informazioni, vedere [Mapping dei parametri del flusso di dati.](parameters-data-flow.md)

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda nel riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche di tale trasformazione. Per altre informazioni, vedere la pagina della documentazione della trasformazione.

![Scheda Impostazioni origine](media/data-flow/source1.png "Scheda Impostazioni origine")

#### <a name="optimize"></a>Ottimizzazione

La **scheda Ottimizza** contiene le impostazioni per configurare gli schemi di partizionamento. Per altre informazioni su come ottimizzare i flussi di dati, vedere la guida alle prestazioni [del flusso di dati per mapping.](concepts-data-flow-performance.md)

![Screenshot che mostra la scheda Ottimizza, che include l'opzione Partizione, il tipo di partizione e il numero di partizioni.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Controllare

La **scheda Inspect** (Ispeziona) offre una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti alle colonne. **Inspect** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel **riquadro Ispeziona.**

![Controllare](media/data-flow/inspect1.png "Controllare")

Quando si modifica la forma dei dati tramite trasformazioni, il flusso delle modifiche ai metadati verrà visualizzato nel **riquadro Ispeziona.** Se nella trasformazione di origine non è presente uno schema definito, i metadati non saranno visibili nel **riquadro Ispeziona.** La mancanza di metadati è comune negli scenari di deriva dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attivata, la scheda **Anteprima** dati offre uno snapshot interattivo dei dati in ogni trasformazione. Per altre informazioni, vedere [Anteprima dei dati in modalità di debug.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che influiscono sull'intero flusso di dati, ad esempio il salvataggio e la convalida. È possibile visualizzare anche il codice JSON sottostante e lo script del flusso di dati della logica di trasformazione. Per altre informazioni, vedere lo [script del flusso di dati](data-flow-script.md).

## <a name="available-transformations"></a>Trasformazioni disponibili

Visualizzare la [panoramica della trasformazione Flusso di](data-flow-transformation-overview.md) dati di mapping per ottenere un elenco delle trasformazioni disponibili.

## <a name="data-flow-data-types"></a>Tipi di dati del flusso di dati

array binary boolean decimal date float integer long map short string timestamp

## <a name="data-flow-activity"></a>Attività flusso di dati

I flussi di dati di mapping vengono operazionati all'interno delle pipeline DIF usando [l'attività flusso di dati](control-flow-execute-data-flow-activity.md). Un utente deve solo specificare il runtime di integrazione da usare e passare i valori dei parametri. Per altre informazioni, vedere Runtime di [integrazione di Azure.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Modalità di debug

La modalità di debug consente di visualizzare in modo interattivo i risultati di ogni passaggio di trasformazione durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia in durante la compilazione della logica del flusso di dati che nell'esecuzione di esecuzioni di debug della pipeline con attività del flusso di dati. Per altre informazioni, vedere la [documentazione relativa alla modalità di debug](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitoraggio dei flussi di dati

Il flusso di dati di mapping si integra con le funzionalità di Azure Data Factory esistenti. Per informazioni su come comprendere l'output di monitoraggio del flusso di dati, vedere [Monitoraggio dei flussi di dati di mapping](concepts-data-flow-monitoring.md).

Il Azure Data Factory team ha creato [](concepts-data-flow-performance.md) una guida per l'ottimizzazione delle prestazioni che consente di ottimizzare il tempo di esecuzione dei flussi di dati dopo la compilazione della logica di business.


## <a name="available-regions"></a>Aree disponibili

I flussi di dati di mapping sono disponibili nelle aree seguenti in AdF:

| Area di Azure | Flussi di dati in AdF |
| ------------ | ----------------- |
| Australia centrale | |
| Australia centrale 2 | |
| Australia orientale | ✓ |
| Australia sud-orientale   | ✓ |
| Brasile meridionale  | ✓ |
| Canada centrale | ✓ |
| India centrale | ✓ |
| Stati Uniti centrali    | ✓ |
| Cina orientale |      |
| Cina orientale 2  |   |
| Cina (non a livello di area) | |
| Cina settentrionale | ✓ |
| Cina settentrionale 2 | ✓ |
| Asia orientale | ✓ |
| Stati Uniti orientali   | ✓ |
| Stati Uniti orientali 2 | ✓ |
| Francia centrale | ✓ |
| Francia meridionale  | |
| Germania centrale (sovrana) | |
| Germania (non a livello di area) (sovrano) | |
| Germania settentrionale (pubblico) | |
| Germania nord-orientale (sovrana) | |
| Germania centrale occidentale (pubblico) |  |
| Giappone orientale | ✓ |
| Giappone occidentale |  |
| Corea centrale | ✓ |
| Corea meridionale | |
| Stati Uniti centro-settentrionali  | ✓ |
| Europa settentrionale  | ✓ |
| Norvegia orientale | ✓ |
| Norvegia occidentale | |
| Sudafrica settentrionale    | ✓ |
| Sudafrica occidentale |  |
| Stati Uniti centro-meridionali  | |
| India meridionale | |
| Asia sud-orientale    | ✓ |
| Svizzera settentrionale |   |
| Svizzera occidentale | |
| Emirati Arabi Uniti centrali | |
| Emirati Arabi Uniti settentrionali | ✓ |
| Regno Unito meridionale  | ✓ |
| Regno Unito occidentale |     |
| US DoD (area centrale) | |
| US DoD (area orientale) | |
| US Gov Arizona | ✓ |
| US Gov (non a livello di area) | |
| US Gov Texas | |
| US Gov Virginia | ✓ |
| Stati Uniti centro-occidentali |     |
| Europa occidentale   | ✓ |
| India occidentale | |
| Stati Uniti occidentali   | ✓ |
| Stati Uniti occidentali 2 | ✓ |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una trasformazione [di origine.](data-flow-source.md)
* Informazioni su come compilare i flussi di dati in [modalità di debug](concepts-data-flow-debug-mode.md).
