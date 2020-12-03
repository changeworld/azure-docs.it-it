---
title: Analizza informazioni dettagliate sui dati in Azure
description: Questa guida dettagliata descrive come visualizzare e usare la creazione di report di analisi di informazioni dettagliate sui dati.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ea12bf8a8d93f14c5364864b97d1173fe8602765
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554424"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Analizza informazioni dettagliate sui dati in Azure

Questa guida dettagliata descrive come accedere, visualizzare e filtrare i report di analisi approfondita di Azure per i dati.

In questa guida dettagliata si apprenderà come:

* Visualizza informazioni approfondite dall'account di competenza.
* Ottieni una visione d'occhio delle tue analisi.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

* Configurare le risorse di Azure e popolare l'account con i dati.
* Configurare e completare un'analisi sull'origine dati.

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Usare Insights Scan Insights

In Azure competenza è possibile registrare e analizzare i tipi di origine. È possibile visualizzare lo stato di analisi nel tempo in analisi approfondite. Le informazioni dettagliate indicano il numero di analisi non riuscite, completate o annullate entro un determinato periodo di tempo.

### <a name="view-scan-insights"></a>Visualizza informazioni dettagliate analisi

1. Passare alla schermata dell'istanza di **Azure** per la portale di Azure e selezionare l'account di competenza.

1. Nella sezione attività **iniziali** della pagina **Overview** selezionare il riquadro **Open di competenza Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Avviare la competenza dal portale di Azure":::

1. Nella **Home** page di competenza selezionare il riquadro **Visualizza informazioni dettagliate** per accedere all'area di **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Visualizza le informazioni dettagliate nella portale di Azure":::

1. Nell'area **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: selezionare **analisi** per visualizzare il report **Insights Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Visualizzare gli indicatori KPI di alto livello per visualizzare il conteggio delle analisi in base allo stato
 
Gli indicatori KPI di alto livello mostrano le analisi totali eseguite entro un periodo di tempo. Il periodo di tempo è impostato sul valore predefinito degli ultimi 30 giorni. Tuttavia, è possibile selezionare anche gli ultimi sette giorni. In base al filtro temporale, i valori degli indicatori KPI riflettono il conteggio delle analisi in modo appropriato.


In base al valore del filtro temporale selezionato, è possibile visualizzare la distribuzione delle analisi riuscite, non riuscite e annullate in base alla settimana o al giorno nel grafico.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Visualizza informazioni dettagliate analisi":::

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insights di Azure con [Asset](./asset-insights.md) Insights
