---
title: 'Esercitazione: Esplorare set di risorse, dettagli, schemi e classificazioni in Azure Purview (anteprima)'
description: Questa esercitazione descrive come usare set di risorse, dettagli degli asset, schemi e classificazioni.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693322"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Esercitazione: Esplorare set di risorse, dettagli, schemi e classificazioni in Azure Purview (anteprima)

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

In questa esercitazione si esplorano i componenti chiave del catalogo, ovvero set di risorse, dettagli degli asset, schemi e classificazioni.

Questa è la *quarta parte di una serie di esercitazioni in cinque parti* contenenti le informazioni di base per gestire la governance dell'intera piattaforma dati usando Azure Purview. Questa esercitazione si basa sull'attività completata nella [Parte 3: Esplorare gli asset in Azure Purview (anteprima) e visualizzare la relativa derivazione](tutorial-browse-and-view-lineage.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Visualizzare i set di risorse.
> * Visualizzare i dettagli degli asset.
> * Modificare lo schema e aggiungere classificazioni.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione:  Esplorare gli asset in Azure Purview (anteprima) e visualizzare la relativa derivazione](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="view-resource-sets"></a>Visualizzare i set di risorse

Un set di risorse è un singolo oggetto del catalogo che rappresenta molti oggetti fisici presenti nell'archiviazione. Gli oggetti in genere condividono uno schema comune e, nella maggior parte dei casi, una convenzione di denominazione o una struttura di cartelle. Ad esempio, il formato della data è *aaaa/mm/gg*. Per altre informazioni sui set di risorse, vedere [Informazioni sui set di risorse](concept-resource-sets.md).

1. Passare alla risorsa Azure Purview nel portale di Azure e selezionare **Apri in Purview Studio**. Si viene automaticamente reindirizzati nella home page di Purview Studio.

2. Immettere **contoso_staging_positivecashflow_ n** nella casella **Cerca asset** e quindi selezionare **Contoso_Staging_PositiveCashFlow_{N}.csv** nei risultati della ricerca.

## <a name="view-asset-details"></a>Visualizzare i dettagli degli asset

1. La scheda **Panoramica** visualizza i campi **Descrizione**, **Termini di glossario** e **Proprietà**, ad esempio **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Screenshot che mostra la scheda Panoramica della pagina di asset di un set di risorse.":::

1. In **Proprietà** notare i due campi seguenti:

   * **partitionCount**: indica il numero di file fisici associati al set di risorse.
   * **schemaCount**: indica il numero di varianti dello schema trovati all'interno di questo set di risorse.

   Azure Purview popola queste proprietà entro 24 ore dopo il completamento dell'analisi descritta nella [parte 1 di questa serie di esercitazioni](tutorial-scan-data.md).

1. Selezionare la scheda **Contatti** per esaminare i valori di **Esperti** e **Proprietari**.

## <a name="edit-the-schema-and-add-classifications"></a>Modificare lo schema e aggiungere classificazioni

1. Selezionare la scheda **Schema**. Osservare i nomi delle colonne e le classificazioni associate. Notare che l'analisi ha popolato automaticamente le proprietà.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Screenshot della scheda Schema.":::

1. Per modificare l'asset, selezionare il pulsante **Modifica** in alto a sinistra. Quindi selezionare la scheda **Schema**.

1. Aggiungere una **Descrizione** per una colonna o assegnare alla colonna un nome più descrittivo.

1. Aggiungere una classificazione a livello di asset selezionando nell'elenco a discesa **Column level classification** (Classificazione a livello di colonna) un nome di colonna per cui non è impostata una classificazione.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Pagina di modifica dello schema":::

1. Dopo aver apportato tutte le modifiche, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Visualizzare i set di risorse.
> * Visualizzare i dettagli degli asset.
> * Modificare lo schema e aggiungere classificazioni.

Passare all'esercitazione successiva per informazioni sul glossario e su come definire e importare nuovi termini per gli asset.

> [!div class="nextstepaction"]
> [Creare e importare termini di glossario](tutorial-import-create-glossary-terms.md)