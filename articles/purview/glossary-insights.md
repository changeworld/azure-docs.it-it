---
title: Report sul glossario sui dati con informazioni dettagliate
description: Questa guida dettagliata descrive come visualizzare e usare i report di glossario di Insights per i dati.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576775"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Informazioni dettagliate sul glossario sui dati in Azure

Questa guida dettagliata descrive come accedere, visualizzare e filtrare i report approfondimenti sul glossario di competenza per i dati.

In questa guida dettagliata si apprenderà come:

> [!div class="checklist"]
> - Passa a Insights dall'account di competenza
> - Ottieni una panoramica dei dati

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

- Configurare le risorse di Azure e popolare l'account con i dati

- Configurare e completare un'analisi nel tipo di origine

- Configura un glossario e alleghi le risorse ai termini del glossario

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Usare informazioni dettagliate sul glossario di competenza

In Azure competenza è possibile creare termini di glossario e collegarli agli asset. In seguito, è possibile visualizzare la distribuzione del glossario in informazioni dettagliate sul glossario. In questo modo viene indicato lo stato del glossario per termini collegati alle risorse. Indica inoltre le condizioni in base allo stato e alla distribuzione dei ruoli in base al numero di utenti.

**Per visualizzare informazioni dettagliate sul glossario:**

1. Passare alla [schermata dell'istanza](https://aka.ms/purviewportal) di **Azure** per la portale di Azure e selezionare l'account di competenza.

1. Nella sezione attività **iniziali** della pagina **Panoramica** selezionare il riquadro avviare l'account di **competenza** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Avviare la competenza dal portale di Azure":::

1. Nella **Home** page di competenza selezionare il riquadro **Visualizza informazioni dettagliate** per accedere all'area di **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Visualizza le informazioni dettagliate nella portale di Azure":::

1. Nell'area **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: selezionare **Glossario** per visualizzare il report **informazioni dettagliate sul glossario** di competenza.

La pagina **informazioni dettagliate sul glossario** Visualizza le aree seguenti:
1. **Indicatori KPI di alto livello** per mostrare gli utenti del catalogo e i termini del glossario

2. Il **numero massimo di termini di glossario e il numero di asset** Mostra i 5 termini di glossario con asset collegati. Tutti gli altri asset sono considerati nella categoria "altro" nel grafico.

3. **Lo stato dei termini del glossario** Mostra la distribuzione dei termini di glossario in base allo stato, ad esempio "bozza", "approvato", "avviso" e "scaduto". 

1. Passare il puntatore del mouse sulla sezione del grafo con uno stato e prendere nota del numero di termini con lo stato.

1. **La distribuzione dei ruoli per numero di utenti** Mostra la distribuzione dei ruoli per numero di utenti per ogni ruolo in ambito.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Visualizza informazioni dettagliate sul glossario":::

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insights di Azure con [Asset](./asset-insights.md) Insights