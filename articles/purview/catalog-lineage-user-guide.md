---
title: Guida dell'utente di derivazione di Data Catalog (anteprima)
description: Questo articolo fornisce una panoramica della funzionalità di derivazione del catalogo di Azure.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 47c5ad18f50bd25dd3bf351090ffc9ef5d54f0b2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202910"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Guida dell'utente di Azure per Data Catalog Lineage

Questo articolo fornisce una panoramica delle funzionalità di derivazione dei dati in Azure competenze Data Catalog.

## <a name="background"></a>Sfondo

Una delle funzionalità della piattaforma Azure è la possibilità di visualizzare la derivazione tra i set di dati creati dai processi di dati. Sistemi come Data Factory, condivisione dati e Power BI acquisiscono la derivazione dei dati durante lo spostamento. La creazione di report di derivazione personalizzati è supportata anche tramite hook Atlas e l'API REST.

## <a name="lineage-collection"></a>Raccolta di derivazione 
 I metadati raccolti in Azure per ambito dei sistemi dati aziendali vengono uniti per mostrare una derivazione dei dati end-to-end. I sistemi dati che raccolgono la derivazione in competenza sono suddivisi in categorie nei tre tipi seguenti.

### <a name="data-processing-system"></a>Sistema di elaborazione dati
L'integrazione dei dati e gli strumenti ETL possono eseguire il push della derivazione in Azure in fase di esecuzione. Gli strumenti, ad esempio Data Factory, condivisione dati, sinapsi, Azure Databricks e così via, appartengono a questa categoria di sistemi di dati. I sistemi di elaborazione dati fanno riferimento a DataSet come origine da database e soluzioni di archiviazione diversi per creare set di dati di destinazione. La tabella seguente elenca i sistemi di elaborazione dati attualmente integrati con la competenza per la derivazione.


| Sistema di elaborazione dati | Ambito supportato |
| ---------------------- | ------------|
| Azure Data Factory | [Attività di copia](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [Attività flusso di dati](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Attività Esegui pacchetto SSIS](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Condivisione dati di Azure | [Snapshot di condivisione](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Sistemi di archiviazione dei dati
I database & soluzioni di archiviazione quali SQL Server, Teradata e SAP hanno motori di query per trasformare i dati tramite il linguaggio di scripting. La derivazione dei dati dalle stored procedure viene raccolta nell'ambito di competenza e unite con la derivazione da altri sistemi.

| Sistema di archiviazione dati | Ambito supportato |
| ---------------------- | ------------|
| Teradata | Stored procedure

### <a name="data-analytics--reporting-systems"></a>Data Analytics & sistemi di report
Sistemi di dati come Azure ML e Power BI derivazione del report in Azure. Questi sistemi useranno i set di impostazioni dei sistemi di archiviazione ed elaborano i relativi metadati per creare dashboard di business intelligence, esperimenti ML e così via.

| Sistema di report di analisi dei dati & | Ambito supportato |
| ---------------------- | ------------|
| Power BI | [DataSet, flussi di data, report & dashboard](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Introduzione a derivazione

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE1XVQS]

La derivazione in ambito include set di impostazioni e processi. I set di impostazioni sono detti anche nodi, mentre i processi possono anche essere denominati bordi:

* **DataSet (node)**: set di dati (strutturato o non strutturato) fornito come input per un processo. Ad esempio, una tabella SQL, un BLOB di Azure e file, ad esempio CSV e XML, sono tutti considerati set di dati. Nella sezione relativa alla derivazione di competenza, i set di impostazioni sono rappresentati da caselle rettangolari.

* **Processo (Edge)**: un'attività o una trasformazione eseguita su un set di dati viene chiamata processo. Ad esempio, l'attività di copia ADF, lo snapshot di condivisione dati e così via. Nella sezione relativa alla derivazione di competenza, i processi sono rappresentati da caselle con bordi arrotondati.

Per accedere alle informazioni di derivazione per un asset in ambito, attenersi alla procedura seguente:

1. Nella portale di Azure andare alla [pagina account di Azure](https://aka.ms/purviewportal)per la competenza.

1. Selezionare il proprio account Azure di competenza dall'elenco e quindi selezionare **Avvia account di competenza** dalla pagina **Panoramica** .

1. Nella **Home** page di Azure Azure, cercare il nome di un set di dati o il nome del processo, ad esempio copia ADF o attività flusso di dati. Quindi premere INVIO.

1. Nei risultati della ricerca selezionare l'asset e **selezionare la relativa scheda** derivazione.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Screenshot che illustra come selezionare la scheda derivazione." border="true":::

## <a name="asset-level-lineage"></a>Derivazione a livello di asset

Azure competenza supporta la derivazione a livello di asset per i set di impostazioni e i processi. Per visualizzare la derivazione del livello di asset, passare **alla scheda** derivazione dell'asset corrente nel catalogo. Selezionare il nodo asset del set di dati corrente. Per impostazione predefinita, l'elenco delle colonne appartenenti ai dati viene visualizzato nel riquadro sinistro.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Screenshot che illustra come selezionare le colonne della visualizzazione nella pagina di derivazione" border="true":::

## <a name="dataset-column-lineage"></a>Derivazione colonna DataSet

Per visualizzare la derivazione a livello di colonna di un set di dati, passare **alla scheda** derivazione dell'asset corrente nel catalogo e attenersi alla procedura seguente:

1. Nella scheda derivazione, nel riquadro sinistro, selezionare la casella di controllo accanto a ogni colonna che si desidera visualizzare nella derivazione dei dati.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Screenshot che illustra come selezionare le colonne da visualizzare nella pagina di derivazione." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Passare il mouse su una colonna selezionata nel riquadro sinistro o nel set di dati dell'area di disegno della derivazione per visualizzare il mapping delle colonne. Vengono evidenziate tutte le istanze della colonna.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Screenshot che illustra come passare il puntatore del mouse su un nome di colonna per evidenziare il flusso di colonna in un percorso di derivazione dei dati." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Se il numero di colonne è maggiore di quello che è possibile visualizzare nel riquadro sinistro, utilizzare l'opzione filtro per selezionare una colonna specifica in base al nome. In alternativa, è possibile utilizzare il mouse per scorrere l'elenco.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Screenshot che illustra come filtrare le colonne in base al nome della colonna nella pagina di derivazione." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Se l'area di disegno della derivazione contiene più nodi e bordi, usare il filtro per selezionare l'asset di dati o elaborare i nodi in base al nome. In alternativa, è possibile usare il mouse per scorrere la finestra di derivazione.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Screenshot che mostra i nodi asset di dati in base al nome nella pagina di derivazione." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Usare l'interruttore nel riquadro a sinistra per evidenziare l'elenco di set di elementi nell'area di disegno della derivazione. Se si disattiva l'interruttore, vengono visualizzati tutti gli asset che contengono almeno una delle colonne selezionate. Se si attiva l'interruttore, verranno visualizzati solo i set di elementi che contengono tutte le colonne.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Screenshot che illustra come usare l'interruttore per filtrare l'elenco di nodi nella pagina di derivazione." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Derivazione colonna processo
Il processo dati può richiedere uno o più set di dati di input per produrre uno o più output. In ambito, la derivazione a livello di colonna è disponibile per i nodi di processo. 
1. Passare tra i set di dati di input e di output da un elenco a discesa nel pannello colonne.
2. Selezionare le colonne da una o più tabelle per visualizzare il flusso di derivazione dal set di dati di input al set di dati di output corrispondente.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="Screenshot che mostra la derivazione delle colonne di un nodo di processo." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Sfogliare gli asset in derivazione
1. Selezionare **passa a Asset** in qualsiasi asset per visualizzare i metadati corrispondenti dalla visualizzazione di derivazione. Questa operazione è un modo efficace per passare a un altro asset nel catalogo dalla visualizzazione di derivazione.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Schermata come selezionare passa a asset in un asset di dati di derivazione." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. L'area di disegno della derivazione potrebbe diventare complessa per i set di impostazioni più diffusi Per evitare confusione, nella visualizzazione predefinita vengono visualizzati solo cinque livelli di derivazione per l'asset nello stato attivo. Il resto della derivazione può essere espanso facendo clic sulle bolle nell'area di disegno della derivazione. I consumer di dati possono anche nascondere gli asset nell'area di disegno che non sono di alcun interesse. Per ridurre ulteriormente il disordine, disattivare l'opzione attiva/disattiva **più** derivazione nella parte superiore dell'area di disegno della derivazione. Questa azione nasconderà tutte le bolle nell'area di disegno della derivazione.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Screenshot che illustra come abilitare o disabilitare più derivazione." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Usare i pulsanti smart nell'area di disegno della derivazione per ottenere una visualizzazione ottimale della derivazione. Layout automatico, zoom avanti e indietro, zoom avanti/indietro, schermo intero e mappa di navigazione sono disponibili per un'esperienza di derivazione immersiva nel catalogo.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Screenshot che illustra come selezionare i pulsanti smart derivazione." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Passaggi successivi

* [Collegamento a Azure Data Factory per la derivazione](how-to-link-azure-data-factory.md)
* [Collegamento alla condivisione dati di Azure per la derivazione](how-to-link-azure-data-share.md)