---
title: 'Procedura: esplorare il Data Catalog'
description: Questo articolo fornisce una panoramica su come esplorare il Data Catalog di competenza di Azure in base al tipo di asset.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695060"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Esplorare Azure Purview Data Catalog

Questo articolo descrive come individuare i dati nel Data Catalog di competenza di Azure usando lo spazio dei nomi gerarchico dell'origine dati.

## <a name="browse-experience"></a>Esplora esperienza

Un consumer di dati può individuare i dati utilizzando lo spazio dei nomi gerarchico noto per ogni origine dati tramite una visualizzazione di esplorazione. Una volta che l'origine dati è stata registrata e analizzata, la mappa dei dati estrae le informazioni sulla struttura (spazio dei nomi gerarchico) dell'origine dati. Queste informazioni vengono utilizzate per creare l'esperienza di esplorazione per l'individuazione dei dati.

Ad esempio, è possibile trovare facilmente un set di dati denominato *DateDimension* in una cartella denominata *Dimensions* in Azure Data Lake storage gen 2. È possibile usare l'esperienza "Sfoglia per tipo di asset" per passare all'account di archiviazione ADLS di generazione 2, quindi passare al contenitore del > di servizio > cartelle per raggiungere la cartella *dimensioni* specifiche e quindi vedere la tabella *DateDimension* .

Per ogni origine dati corrispondente viene fornita un'esperienza di esplorazione nativa con spazio dei nomi gerarchico.

## <a name="browse-the-data-catalog-by-asset-type"></a>Esplorare i Data Catalog per tipo di risorsa

1. È possibile visualizzare gli asset di dati selezionando il **tipo di asset Browse by** nella Home page.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="home page di competenza" border="true":::

1. Nella pagina **Sfoglia tipi di asset** i riquadri sono suddivisi in categorie in base alle origini dati. Per esplorare ulteriormente gli asset in ogni origine dati, selezionare il riquadro corrispondente.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Pagina Sfoglia tipi di asset" border="true":::

1. Nella pagina successiva vengono elencate le risorse di livello superiore sotto il tipo di dati scelto. Selezionare una delle risorse per esplorarne ulteriormente il contenuto.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Pagina di esplorazione specifica del tipo di asset. Esempio illustrato è l'account di archiviazione di Azure" border="true":::

1. Viene visualizzata la visualizzazione Esplora. Avviare l'esplorazione selezionando l'asset nel pannello di sinistra. Gli asset figlio verranno elencati nel riquadro destro della pagina.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Visualizzazione di Esplora risorse" border="true":::

1. Per visualizzare i dettagli di un asset, selezionare il nome o il pulsante con i puntini di sospensione all'estrema destra.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Selezionare il pulsante con i puntini di sospensione per visualizzare la pagina dei dettagli dell'asset" border="true":::

## <a name="view-related-data-assets"></a>Visualizzare gli asset di dati correlati

Una volta visualizzata la pagina dei dettagli dell'asset, è possibile visualizzare anche altri asset di dati correlati. Ad esempio, è possibile passare alla cartella padre di *DateDimension* per visualizzare la cartella *Dimensions* o persino passare al contenitore per visualizzare gli asset nella gerarchia specifica.

1. Nella pagina dettagli Asset selezionare la scheda **correlata** per visualizzare altri asset correlati.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Avvia scheda correlata" border="true":::

1. La gerarchia completa dell'asset corrente sarà elencata a sinistra.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Struttura gerarchica" border="true":::

1. Selezionare qualsiasi livello nella gerarchia per elencare le risorse immediate sotto tale livello.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Selezione gerarchia diversa" border="true":::

## <a name="next-steps"></a>Passaggi successivi

- [Come creare, importare ed esportare i termini di glossario](how-to-create-import-export-glossary.md)
- [Come gestire i modelli di termini per il glossario aziendale](how-to-manage-term-templates.md)
