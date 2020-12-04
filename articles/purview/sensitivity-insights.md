---
title: Segnalazione dell'etichetta di riservatezza sui dati nell'archivio BLOB di Azure
description: Questa guida dettagliata descrive come visualizzare e usare la creazione di report delle etichette di riservatezza di competenza sui dati nell'archivio BLOB di Azure.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 74f4391fcd071ac510fc66b2f8bc36ec4549dc47
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575755"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Informazioni approfondite sull'etichetta di riservatezza sui dati in Azure

Questa guida dettagliata descrive come accedere, visualizzare e filtrare le informazioni di sicurezza fornite dalle etichette di riservatezza applicate ai dati.

Le origini dati supportate includono: archiviazione BLOB di Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, database SQL di Azure, Azure SQL Istanza gestita

In questa guida dettagliata si apprenderà come:

> [!div class="checklist"]
> - Avviare il proprio account di competenza da Azure.
> - Visualizza informazioni dettagliate sull'etichettatura dei dati
> - Esegui il drill-down per ottenere ulteriori informazioni sull'etichettatura dei dati

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

- Configurare le risorse di Azure e popolare gli account pertinenti con i dati di test

- [Estendendo Microsoft 365 le etichette di riservatezza agli asset in Azure](create-sensitivity-label.md)e creando o selezionando le etichette che si vuole applicare ai dati.

- Configurare e completare un'analisi sui dati di test in ogni origine dati

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md) ed [etichettare automaticamente i dati in Azure](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Usare la riservatezza di competenza per le etichette Insights

In ambito, le classificazioni sono simili ai tag Subject e vengono usate per contrassegnare e identificare i dati di un tipo specifico che si trova all'interno dell'area dati durante l'analisi.

Le etichette di riservatezza consentono di indicare quanto sono sensibili determinati dati nell'organizzazione. Ad esempio, un nome di progetto specifico può essere altamente riservato all'interno dell'organizzazione, mentre lo stesso termine non è riservato ad altre organizzazioni. 

Mentre le classificazioni vengono confrontate direttamente (un numero di previdenza sociale ha una classificazione di un **numero di previdenza sociale**), le etichette di riservatezza vengono applicate quando vengono rilevate una o più classificazioni e scenari. 

La competenza usa le stesse classificazioni, note anche come tipi di informazioni riservate, come Microsoft 365. In questo modo è possibile estendere le etichette di riservatezza esistenti tra le risorse di competenza di Azure.

**Per visualizzare le informazioni dettagliate sulle etichette di riservatezza:**

1. Accedere al home page di **competenza di Azure** .

1. Nella sezione attività **iniziali** della pagina **Panoramica** selezionare il riquadro **avviare l'account di competenza** .

1. In competenza selezionare la voce di menu **Insights** a :::image type="icon" source="media/insights/ico-insights.png" border="false"::: sinistra per accedere all'area di **Insights** .

1. Nell'area **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: selezionare **etichette di riservatezza** per visualizzare il report delle **informazioni dettagliate** relative all'etichetta di competenza.

    > [!NOTE]
    > Se il report è vuoto, è possibile che le etichette di riservatezza non siano state estese ad Azure. Per altre informazioni, vedere [etichettare automaticamente i dati in Azure](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Informazioni dettagliate sulle etichette di riservatezza" lightbox="media/insights/sensitivity-labeling-insights.png":::

   La pagina delle **informazioni dettagliate sull'etichetta di riservatezza** principale Visualizza le aree seguenti:

   |Area  |Descrizione  |
   |---------|---------|
   |**Panoramica delle origini con etichette di riservatezza**     |Visualizza i riquadri che forniscono: <br>: Numero di sottoscrizioni trovate nei dati. <br>: Numero di etichette di riservatezza univoche applicate ai dati <br>: Numero di origini con etichette di riservatezza applicate <br>: Numero di file e tabelle trovati con le etichette di riservatezza applicate|
   |**Origini principali con dati con etichetta (ultimi 30 giorni)**     | Mostra la tendenza negli ultimi 30 giorni del numero di origini con le etichette di riservatezza applicate.       |
   |**Etichette principali applicate tra le origini**     |Mostra le etichette principali applicate a tutte le risorse dei dati di competenza. |
   |**Etichette principali applicate ai file**     |Mostra le etichette di riservatezza principali applicate ai file nei dati.          |
   |**Etichette principali applicate alle tabelle**     | Mostra le etichette di riservatezza principali applicate alle tabelle di database nei dati. |   
   |  **Attività di assegnazione di etichette**  |  Visualizza grafici distinti per i file e le tabelle, ognuno dei quali Mostra il numero di file o tabelle etichettati nell'intervallo di tempo selezionato. <br>**Impostazione predefinita**: 30 giorni<br>Selezionare il filtro **temporale** sopra i grafici per selezionare un intervallo di tempo diverso da visualizzare.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Assegnazione di un'etichetta al drill-down di Insights

In una delle seguenti opzioni di contrassegno dei grafici di **Insights** , selezionare il collegamento **Visualizza altro** per eseguire il drill-down per altri dettagli:

- **Etichette principali applicate tra le origini**
- **Etichette principali applicate ai file**
- **Etichette principali applicate alle tabelle**
- **Assegnazione di etichette alle attività > dati con etichetta**

Ad esempio:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Drill-down etichetta di riservatezza" lightbox="media/insights/sensitivity-label-drilldown.png":::

Per ulteriori informazioni, effettuare una delle operazioni seguenti:

|Opzione  |Descrizione  |
|---------|---------|
|**Filtrare i dati**     |  Utilizzare i filtri sopra la griglia per filtrare i dati visualizzati, inclusi il nome dell'etichetta, il nome della sottoscrizione o il tipo di origine. <br><br>Se non si è certi del nome esatto dell'etichetta, è possibile immettere parte o tutto il nome nella casella **Filtra per parola chiave** .       |
|**Ordinare la griglia** |Selezionare un'intestazione di colonna per ordinare la griglia in base a tale colonna. | 
|**Modifica colonne**     |  Per visualizzare un numero maggiore o minore di colonne nella griglia, selezionare **modifica colonne** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , quindi selezionare le colonne che si desidera visualizzare o modificare l'ordine.    <br><br>Selezionare un'intestazione di colonna per ordinare la griglia in base a tale colonna.   |
|**Eseguire ulteriormente il drill-down**     | Per eseguire il drill-down fino a un'etichetta specifica, selezionare un nome nella colonna **etichetta riservatezza** per visualizzare l'etichetta in base al report **di origine** . <br><br>Questo report consente di visualizzare i dati per l'etichetta selezionata, inclusi il nome dell'origine, il tipo di origine, l'ID sottoscrizione e il numero di file e tabelle classificati.      |
|**Sfoglia asset**     |  Per esplorare le risorse trovate con un'etichetta o un'origine specifica, selezionare una o più etichette o origini, a seconda del report che si sta visualizzando, quindi selezionare **Sfoglia asset** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: sopra i filtri. <br><br>Nei risultati della ricerca vengono visualizzati tutti gli asset con etichetta trovati per il filtro selezionato.  Per ulteriori informazioni, vedere [la pagina relativa alla ricerca nel Data Catalog di competenza di Azure](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integrazione dell'etichetta di riservatezza con Microsoft 365 conformità

Una soluzione di integrazione con [Microsoft Information Protection](/microsoft-365/compliance/information-protection) offerta in Microsoft 365 significa che la competenza consente di estendere in modo diretto la visibilità dei dati, nonché di classificare ed etichettare i dati.

Per estendere le etichette di riservatezza Microsoft 365 alle risorse in Azure, è necessario attivare attivamente Information Protection per Azure, nel centro di conformità di Microsoft 365.

Per altre informazioni, vedere [etichettare automaticamente i dati in Azure](create-sensitivity-label.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su questi report Insight di Azure per le competenze:

- [Informazioni dettagliate sul glossario](glossary-insights.md)
- [Analizza informazioni dettagliate](scan-insights.md)
- [Informazioni dettagliate sulla classificazione](./classification-insights.md)
- [Informazioni dettagliate sull'estensione di file](file-extension-insights.md)
