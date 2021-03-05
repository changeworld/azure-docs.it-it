---
title: Asset Insights sui dati in Azure (anteprima)
description: Questa guida dettagliata descrive come visualizzare e usare la creazione di report di asset Insights per i dati.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: b9a207ffa14a18a5f4421fd21cebed28290b5ea6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183081"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Informazioni dettagliate sulle risorse in Azure

Questa guida dettagliata descrive come accedere, visualizzare e filtrare i report di asset Insight per i dati.

In questa guida dettagliata si apprenderà come:

> [!div class="checklist"]
> * Visualizza informazioni approfondite dall'account di competenza.
> * Ottieni una panoramica dei tuoi dati.
> * Eseguire il drill-down per ulteriori dettagli sul numero di asset.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

* Configurare le risorse di Azure e popolare l'account con i dati.

* Configurare e completare un'analisi del tipo di origine.

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Usare asset Insights di competenza

In Azure competenza è possibile registrare e analizzare i tipi di origine. Una volta completata l'analisi, è possibile visualizzare la distribuzione di asset in asset Insights, che indica lo stato dei dati in base alla classificazione e ai set di risorse. Indica inoltre se sono presenti modifiche alle dimensioni dei dati.

> [!NOTE]
> Dopo aver analizzato i tipi di origine, assegnare a Asset Insights 3-8 ore per riflettere i nuovi asset. Il ritardo può essere dovuto a un traffico elevato nell'area di distribuzione o alle dimensioni del carico di lavoro. Per ulteriori informazioni, contattare il team di supporto di Field.

1. Passare alla risorsa di competenza di Azure nella portale di Azure.

1. Nella sezione attività **iniziali** della pagina **Overview** selezionare il riquadro **Open di competenza Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Avviare la competenza dal portale di Azure":::

1. Nella **Home** page di competenza selezionare il riquadro **Visualizza informazioni dettagliate** per accedere all'area di **Insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Visualizza le informazioni dettagliate nella portale di Azure":::

1. Nell'area **Insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: selezionare **Asset** per visualizzare il report di **Asset Insights** di competenza.

### <a name="view-asset-insights"></a>Visualizza asset Insights

1. La pagina principale di **Asset Insights** Visualizza le aree seguenti:

2. Indicatori KPI di alto livello per mostrare i tipi di origine, gli asset classificati e gli asset individuati
 
3. Il primo grafico Mostra gli **asset per ogni tipo di origine**.

4. Visualizzare la distribuzione di asset in base al tipo di origine. Selezionare una classificazione o un'intera categoria di classificazione per visualizzare la distribuzione di asset in base al tipo di origine. 
 
5. Per visualizzare altre informazioni, selezionare **Visualizza altro**, che consente di visualizzare un formato tabulare dei tipi di origine e dei conteggi degli asset. I filtri di classificazione vengono trasferiti a questa pagina.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Visualizzare gli indicatori KPI e il grafo in asset Insights":::
 
6. Selezionare un'origine specifica per cui si vogliono visualizzare le cartelle principali con i conteggi degli asset. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Seleziona tipo di origine":::
 
7. Selezionare le risorse totali nella cartella superiore del tipo di origine selezionato in precedenza.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Visualizza percorsi file":::

8. Visualizzare l'elenco dei file all'interno della cartella. Tornare alle informazioni dettagliate usando le briciole di pane.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Visualizza l'elenco degli asset":::  

### <a name="file-based-source-types"></a>Tipi di origine basati su file
I due grafici successivi in asset Insights mostrano una distribuzione di tipi di origine basati su file. Il primo grafico, denominato **tendenza delle dimensioni (GB) del tipo di file all'interno dei tipi di origine**, Mostra la tendenza delle dimensioni del tipo di file superiore negli ultimi 30 giorni. 
 
1. Selezionare il tipo di origine per visualizzare il tipo di file nell'origine. 
 
1. Selezionare **Visualizza altro** per visualizzare le dimensioni correnti dei dati, modificare le dimensioni, il numero di asset corrente e modificare il numero di asset.
 
   > [!NOTE]
   > Se l'analisi è stata eseguita una sola volta negli ultimi 30 giorni o se qualsiasi modifica al catalogo, ad esempio aggiunta/rimozione della classificazione, si è verificata solo una volta in 30 giorni, le informazioni di modifica sopra riportate vengono

1. Quando si fa clic sul tipo di origine, vedere le prime cartelle con Change Top asset counts.

1. Selezionare il percorso per visualizzare l'elenco di asset.

Il secondo grafico nei tipi di origine basati su file è ***costituito da file non associati a un set di risorse***. Se si prevede che tutti i file debbano essere sottoposti a rollup in un set di risorse, questo grafico può essere utile per comprendere quali asset non sono stati sottoposti a rollup. Gli asset mancanti possono indicare il modello di file errato nella cartella. Seguire gli stessi passaggi di altri grafici per visualizzare altri dettagli sui file.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Visualizza Asset basati su file":::  

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insights di Azure con [analisi approfondite](./scan-insights.md)
