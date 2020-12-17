---
title: 'Esercitazione: Esplorare gli asset in Azure Purview e visualizzare la relativa derivazione'
description: Questa esercitazione descrive come esplorare gli asset del catalogo e visualizzare la derivazione dei dati.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: ab51e604412e79fb706190fef769ad76c694fd6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399435"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Esercitazione: Esplorare gli asset in Azure Purview (anteprima) e visualizzare la relativa derivazione

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

Questa esercitazione illustra come esplorare gli asset in Azure Purview e visualizzare dettagli importanti come la derivazione.

Questa è la *terza parte di una serie di esercitazioni in cinque parti* contenenti le informazioni di base per gestire la governance dell'intera piattaforma dati usando Azure Purview. Questa esercitazione si basa sull'attività completata nella [Parte 2: Esplorare la home page di Azure Purview (anteprima) e cercare un asset](tutorial-asset-search.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Esplorare gli asset del catalogo.
> * Visualizzare la derivazione degli asset.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione: Esplorare la home page di Azure Purview (anteprima) e cercare un asset](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Esplorare gli asset del catalogo

Nell'esercitazione precedente si è appreso come individuare gli asset del catalogo di Asset Purview tramite ricerca. Un altro modo per individuare tali asset consiste nell'usare l'esperienza di esplorazione del catalogo.

Questa sezione illustra come esplorare il catalogo di Azure Purview.

1. Passare alla risorsa Azure Purview nel portale di Azure e selezionare **Apri in Purview Studio**. Si viene automaticamente reindirizzati nella home page di Purview Studio.

1. Nella pagina **Home** selezionare **Esplora asset**.

   Viene visualizzata la pagina **Esplora asset**, con un riepilogo di tutti i tipi di asset presenti nel catalogo.

1. Per esplorare i vari asset di tipo Azure Data Lake Gen2 disponibili nel catalogo, selezionare il riquadro **Azure Data Lake Gen2**.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Screenshot che mostra la pagina di esplorazione per tipo di asset con il riquadro Azure Data Lake Gen2 selezionato.":::

1. Se sono presenti più asset, è possibile selezionare l'intestazione della colonna **Nome** per ordinarli alfabeticamente. In questa esercitazione è disponibile un unico asset Azure Data Lake Storage Gen2.

1. Selezionare il nome dell'asset.

1. Selezionare il set di risorse **Contoso_GrossProfit_{N}.ssv**. Se questo asset non esiste nel catalogo, sceglierne un altro.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Elenco di risorse di Azure Data Lake Storage Gen2":::

## <a name="view-the-lineage-of-assets"></a>Visualizzare la derivazione degli asset

Nella pagina Dettagli asset esplorare l'origine dei dati.

1. Selezionare la scheda **Derivazione** del set di risorse **Contoso_GrossProfit_{N}.ssv**.

   Viene visualizzato l'asset selezionato. Le informazioni sulla derivazione mostrano che il set di risorse è stato copiato dall'account di archiviazione BLOB di Azure ad Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Screenshot che mostra la visualizzazione Derivazione dell'asset.":::

1. Selezionare l'asset BLOB in questa pagina, quindi selezionare il collegamento **Switch to asset** (Passa all'asset).

   Notare che la finestra è passata al set di risorse BLOB di Azure, che è l'origine dell'asset Azure Data Lake Storage Gen2 originale.

1. Selezionare la scheda **Panoramica** per esaminare l'asset e confermarne i dettagli.

Per informazioni su come creare un'attività del flusso di dati di Azure Data Factory tra i set di risorse BLOB di Azure e Azure Data Lake Storage Gen2 e osservare la derivazione, vedere [Attività del flusso di dati di Azure Data Factory](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Esplorare gli asset del catalogo.
> * Visualizzare la derivazione degli asset.

Passare all'esercitazione successiva per informazioni su set di risorse, dettagli degli asset, schemi e classificazioni.

> [!div class="nextstepaction"]
> [Set di risorse, dettagli degli asset, schemi e classificazioni](tutorial-schemas-and-classifications.md)
