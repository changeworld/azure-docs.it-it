---
title: Report sul glossario sui dati con informazioni dettagliate
description: Questa guida dettagliata descrive come visualizzare e usare i report di glossario di Insights per i dati.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095851"
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

Informazioni **dettagliate sul glossario** ti offre un utente aziendale, informazioni utili per mantenere un glossario ben definito per la tua organizzazione.

1. Il report inizia con **indicatori KPI di alto livello** che **mostrano i _termini totali_*_ nell'account di competenza, _*_condizioni approvate senza asset_*_ e _*_termini scaduti con asset_**. Ognuno di questi valori consentirà di identificare l'integrità del glossario.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Visualizza l'indicatore KPI Insights Insights"::: 


2. La sezione **snapshot of terms** (visualizzata sopra) Mostra lo stato del termine come **_bozza_*_, _*_Approved_*_, _*_Alert_*_ e _*_expired_** per i termini con asset e termini senza asset.

3. Fare clic su **Visualizza altro** per visualizzare i nomi dei termini con diversi Stati e altri dettagli sugli esperti di **_amministratori_*_ e _***. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Snapshot dei termini con e senza asset":::  

4. Quando si fa clic su "Visualizza altre" per ***termini approvati con asset** _, le informazioni dettagliate consentono di passare alla pagina di dettaglio _ *Glossario**, da cui è possibile passare all'elenco degli asset con i termini collegati. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Informazioni dettagliate sul glossario"::: 

4. In glossario Insights pagina visualizzare una distribuzione di **termini incompleti** in base al tipo di informazioni mancanti. Il grafico mostra il numero di termini con la **_definizione mancante_*_, _*_Missing Expert_*_, _*_Missing Steward_*_ e _*_Missing più_** campi.

1. Fare clic su ***Visualizza altri** _ da _ * termini incompleti * * per visualizzare i termini con informazioni mancanti. È possibile passare alla pagina dei dettagli del termine del glossario per inserire le informazioni mancanti e verificare che il termine del glossario sia completo.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su come creare un termine di glossario tramite [Glossario](./how-to-create-import-export-glossary.md)