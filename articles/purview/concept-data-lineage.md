---
title: Derivazione dei dati in Azure (anteprima)
description: Vengono descritti i concetti per la derivazione dei dati.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200738"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Derivazione dei dati in Azure competenza Data Catalog client

Questo articolo fornisce una panoramica della derivazione dei dati in Azure Data Catalog. Viene inoltre illustrato il modo in cui i sistemi dati possono integrarsi con il catalogo per acquisire la derivazione dei dati. Le competenze possono acquisire la derivazione dei dati in diverse parti del patrimonio di dati dell'organizzazione e a diversi livelli di preparazione, tra cui:

- Dati completamente non elaborati gestiti da diverse piattaforme
- Dati trasformati e preparati
- Dati usati dalle piattaforme di visualizzazione.

## <a name="use-cases"></a>Casi d'uso

La derivazione dei dati viene ampiamente riconosciuta come ciclo di vita che si estende sull'origine dei dati e dove si sposta nel tempo tra i dati. Viene utilizzata per diversi tipi di scenari di aspetto inverso, come la risoluzione dei problemi, la traccia della causa radice nelle pipeline di dati e il debug. La derivazione viene usata anche per l'analisi della qualità dei dati, la conformità e gli scenari di simulazione. La derivazione è rappresentata visivamente per mostrare i dati spostati dall'origine alla destinazione, inclusa la modalità di trasformazione dei dati. Data la complessità della maggior parte degli ambienti dati aziendali, queste visualizzazioni possono essere difficili da comprendere senza eseguire consolidamenti o mascheramento di punti dati periferici.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Esperienza di derivazione in Azure competenze Data Catalog

Il Data Catalog di competenza si connetterà ad altri sistemi di elaborazione, archiviazione e analisi dei dati per estrarre le informazioni di derivazione. Le informazioni vengono combinate per rappresentare un'esperienza di derivazione generica specifica dello scenario nel catalogo.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="derivazione end-end che mostra i dati copiati dall'archivio BLOB fino a Power BI Dashboard":::

I dati possono includere sistemi per l'estrazione dei dati, la trasformazione (sistemi ETL/ELT), l'analisi e i sistemi di visualizzazione. Ogni sistema acquisisce metadati statici e operativi avanzati che descrivono lo stato e la qualità dei dati all'interno del limite di sistemi. L'obiettivo della derivazione in un catalogo dati è estrarre lo spostamento, la trasformazione e i metadati operativi da ogni sistema di dati con il livello di dettaglio più basso possibile.

L'esempio seguente è un caso d'uso tipico di dati spostati tra più sistemi, in cui il Data Catalog si connette a ciascun sistema per la derivazione.

- Data Factory copia i dati da una zona locale o non elaborata a una zona di destinazione nel cloud. 
- I sistemi di elaborazione dati come sinapsi, databricks elaborano e trasformano i dati dall'area di destinazione alla zona curata usando i notebook.
- Ulteriore elaborazione dei dati in modelli analitici per ottimizzare le prestazioni delle query e l'aggregazione. 
- I sistemi di visualizzazione dei dati utilizzeranno i set di dati e vengono elaborati tramite il metamodello per creare un dashboard di business intelligence, gli esperimenti ML e così via.

## <a name="lineage-granularity"></a>Granularità della derivazione

In questa sezione vengono illustrati i dettagli relativi alla granularità della raccolta delle informazioni di derivazione da parte di un catalogo dati. Questa granularità può variare in base ai sistemi dati in corso.

### <a name="entity-level-lineage-sources--process--targets"></a>Derivazione a livello di entità: origine/i > processo > destinazioni 

- La derivazione viene rappresentata come grafico, in genere contiene le entità di origine e di destinazione nei sistemi di archiviazione dei dati connessi da un processo richiamato da un sistema di calcolo. 
- I sistemi dati si connettono al Catalogo dati per generare e segnalare un oggetto univoco che fa riferimento all'oggetto fisico del sistema dati sottostante, ad esempio: stored procedure SQL, notebook e così via.
- La derivazione ad alta fedeltà con metadati aggiuntivi come la proprietà viene acquisita per mostrare la derivazione in un formato leggibile per le entità di destinazione & di origine. ad esempio: derivazione a livello di tabella hive anziché a livello di file o di partizioni.

### <a name="column-or-attribute-level-lineage"></a>Lignaggio a livello di colonna o di attributo

Identificare gli attributi di un'entità di origine utilizzata per creare o derivare gli attributi nell'entità di destinazione. Il nome dell'attributo di origine può essere mantenuto o rinominato in una destinazione. I sistemi come ADF possono eseguire una copia uno-a-uno dall'ambiente locale al cloud. Ad esempio: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Stato di esecuzione del processo

Per supportare l'analisi delle cause principali e gli scenari di qualità dei dati, si acquisisce lo stato di esecuzione dei processi nei sistemi di elaborazione dati. Questo requisito non ha nulla a che fare con la sostituzione delle funzionalità di monitoraggio di altri sistemi di elaborazione dati, né lo scopo di sostituirli. 

## <a name="summary"></a>Riepilogo

La derivazione è una funzionalità fondamentale del Data Catalog di competenza per supportare scenari di qualità, attendibilità e controllo. L'obiettivo di un catalogo dati è creare un Framework affidabile in cui tutti i sistemi di dati all'interno dell'ambiente possono connettersi naturalmente e segnalare la derivazione. Una volta resi disponibili i metadati, il Catalogo dati può riunire i metadati forniti dai sistemi dati per potenziare i casi di utilizzo della governance dei dati.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un account Azure Purview nel portale di Azure](create-catalog-portal.md)
* [Avvio rapido: Creare un account Azure Purview mediante Azure PowerShell o l'interfaccia della riga di comando di Azure](create-catalog-powershell.md)
* [Guida introduttiva: usare ambito studio](use-purview-studio.md)
