---
title: Creazione di report di classificazione sui dati in Azure, uso di Insights
description: Questa guida dettagliata descrive come visualizzare e usare la creazione di report di classificazione di competenza sui dati.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666515"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Informazioni dettagliate sulla classificazione dei dati di Azure

Questa guida dettagliata descrive come accedere, visualizzare e filtrare i report di analisi della classificazione di competenze per i dati.

Le origini dati supportate includono: archiviazione BLOB di Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (API SQL), Azure sinapsi Analytics (in precedenza SQL DW), database SQL di Azure, Azure SQL Istanza gestita, SQL Server, bucket Amazon S3

In questa guida dettagliata si apprenderà come:

> [!div class="checklist"]
> - Avvia il tuo account di competenza da Azure
> - Visualizza informazioni dettagliate sulla classificazione sui dati
> - Eseguire il drill-down per altri dettagli di classificazione sui dati

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

- Configurare le risorse di Azure e popolare gli account pertinenti con i dati di test

- Configurare e completare un'analisi sui dati di test in ogni origine dati. Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md) e [creare un set di regole di analisi](create-a-scan-rule-set.md).

- Accesso a competenza con un [ruolo di lettore di dati o di data Curator](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Usare le informazioni dettagliate sulla classificazione di competenza

In Azure, le classificazioni sono simili ai tag Subject e vengono usate per contrassegnare e identificare i dati di un tipo specifico che si trova all'interno dell'area dati durante l'analisi.

La competenza usa gli stessi tipi di informazioni riservate Microsoft 365, consentendo di ampliare i criteri di sicurezza e la protezione esistenti nell'intero data.

> [!NOTE]
> Dopo aver analizzato i tipi di origine, assegnare informazioni dettagliate di **classificazione** a un paio di ore per riflettere i nuovi asset.

**Per visualizzare le informazioni dettagliate sulla classificazione:**

1. Passare alla [schermata dell'istanza](https://aka.ms/purviewportal) di **Azure** per la portale di Azure e selezionare l'account di competenza.

1. Nella sezione attività **iniziali** della pagina **Panoramica** selezionare il riquadro **avviare l'account di competenza** .

1. In competenza selezionare la voce di menu **Insights** a :::image type="icon" source="media/insights/ico-insights.png" border="false"::: sinistra per accedere all'area di **Insights** .

1. Nell'area **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: selezionare **Classification (classificazione** ) per visualizzare il report delle **informazioni dettagliate sulla classificazione** di competenza.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Report informazioni dettagliate di classificazione" lightbox="media/insights/select-classification-labeling.png":::

   La pagina principale di **informazioni dettagliate sulla classificazione** Visualizza le aree seguenti:

   |Area  |Descrizione  |
   |---------|---------|
   |**Panoramica delle origini con classificazioni**     |Visualizza i riquadri che forniscono: <br>: Numero di sottoscrizioni trovate nei dati <br>: Numero di classificazioni univoche trovate nei dati <br>: Numero di origini classificate trovate <br>: Numero di file classificati trovati <br>: Numero di tabelle classificate trovate         |
   |**Origini principali con dati classificati (ultimi 30 giorni)**     |Mostra la tendenza negli ultimi 30 giorni del numero di origini trovate con i dati classificati.            |
   |**Principali categorie di classificazione in base alle origini**     |Mostra il numero di origini trovate per categoria di classificazione, ad esempio **Financial** o **Government**.      |
   |**Classificazioni principali per i file**     |Mostra le classificazioni principali applicate ai file nei dati, ad esempio i numeri di carta di credito o i numeri di identificazione nazionale.         |
   |**Classificazioni principali per le tabelle**     | Mostra le classificazioni principali applicate alle tabelle nei dati, ad esempio le informazioni di identificazione personale. |   
   |  **Attività di classificazione** <br>(file e tabelle) |  Visualizza grafici distinti per file e tabelle, ognuno dei quali Mostra il numero di file o tabelle classificati nell'intervallo di tempo selezionato. <br>**Impostazione predefinita**: 30 giorni<br>Selezionare il filtro **temporale** sopra i grafici per selezionare un intervallo di tempo diverso da visualizzare.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Drill-down delle informazioni di classificazione

In uno dei seguenti grafici di informazioni dettagliate di **classificazione** selezionare il collegamento **Visualizza altro** per eseguire il drill-down per altri dettagli:

- **Principali categorie di classificazione in base alle origini**
- **Classificazioni principali per i file**
- **Classificazioni principali per le tabelle**
- **Attività di classificazione > dati di classificazione**

Ad esempio:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Visualizza tutte le classificazioni" lightbox="media/insights/view-classifications.png":::

Per ulteriori informazioni, effettuare una delle operazioni seguenti:

|Opzione  |Descrizione  |
|---------|---------|
|**Filtrare i dati**     |  Utilizzare i filtri sopra la griglia per filtrare i dati visualizzati, inclusi il nome della classificazione, il nome della sottoscrizione o il tipo di origine. <br><br>Se non si è certi del nome esatto della classificazione, è possibile immettere parte o tutto il nome nella casella **Filtra per parola chiave** .       |
|**Ordinare la griglia** |Selezionare un'intestazione di colonna per ordinare la griglia in base a tale colonna. | 
|**Modifica colonne**     |  Per visualizzare un numero maggiore o minore di colonne nella griglia, selezionare **modifica colonne** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , quindi selezionare le colonne che si desidera visualizzare o modificare l'ordine.   |
|**Eseguire ulteriormente il drill-down**     | Per eseguire il drill-down fino a una classificazione specifica, selezionare un nome nella colonna **classificazione** per visualizzare il report **di classificazione in base all'origine** . <br><br>Questo report consente di visualizzare i dati per la classificazione selezionata, inclusi il nome dell'origine, il tipo di origine, l'ID sottoscrizione e il numero di file e tabelle classificati.      |
|**Sfoglia asset**     |  Per esplorare le risorse trovate con una classificazione o un'origine specifica, selezionare una classificazione o un'origine, a seconda del report che si sta visualizzando, quindi selezionare **Sfoglia asset** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: sopra i filtri. <br><br>I risultati della ricerca visualizzano tutte le risorse classificate trovate per il filtro selezionato.  Per ulteriori informazioni, vedere [la pagina relativa alla ricerca nel Data Catalog di competenza di Azure](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insight di Azure
> [!div class="nextstepaction"]
> [Informazioni dettagliate sul glossario](glossary-insights.md)

> [!div class="nextstepaction"]
> [Analizza informazioni dettagliate](scan-insights.md)

> [!div class="nextstepaction"]
> [Informazioni dettagliate sulle etichette di riservatezza](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Informazioni dettagliate sull'estensione di file](file-extension-insights.md)