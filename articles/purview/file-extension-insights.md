---
title: Creazione di report sull'estensione di file sui dati con informazioni dettagliate
description: Questa guida dettagliata descrive come visualizzare e usare la creazione di report per l'estensione di file di Insights per i dati.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 222106f5838a1eb237987afaa93924f1efe4a1f4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746285"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Approfondimenti sui dati di Azure per l'estensione di file 

Questa guida dettagliata descrive come accedere, visualizzare e filtrare informazioni dettagliate sulle estensioni di file o sui tipi di file trovati nei dati.

Le origini dati supportate includono: archiviazione BLOB di Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2

In questa guida dettagliata si apprenderà come:
> [!div class="checklist"]
> * Avviare il proprio account di competenza da Azure. 
> - Visualizzare informazioni dettagliate sull'estensione di file sui dati
> - Eseguire il drill-down per ulteriori dettagli sull'estensione di file sui dati

## <a name="prerequisites"></a>Prerequisiti 

Prima di iniziare a usare informazioni dettagliate, assicurarsi di aver completato i passaggi seguenti:

- Configurare le risorse di Azure e popolare gli account pertinenti con i dati di test

- Configurare e completare un'analisi sui dati di test in ogni origine dati

Per altre informazioni, vedere [gestire le origini dati in Azure (anteprima)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Usare le informazioni dettagliate sull'estensione del file di competenza

Quando si esegue l'analisi degli asset, Azure competenza è in grado di rilevare i tipi di file trovati nel proprio data e fornire altri dettagli su ogni tipo di file. I dettagli includono il numero di file di ogni tipo, in cui si trovano i file e se sono analizzabili per i dati sensibili.

> [!NOTE]
> Dopo aver analizzato i tipi di origine, fornire informazioni dettagliate sull' **estensione di file** in un paio di ore per riflettere i nuovi asset.

**Per visualizzare informazioni dettagliate sull'estensione di file:**

1. Passare alla [schermata dell'istanza](https://aka.ms/purviewportal) di **Azure** per la portale di Azure e selezionare l'account di competenza.

1. Nella sezione attività **iniziali** della pagina **Panoramica** selezionare il riquadro **avviare l'account di competenza** .

1. In competenza selezionare la voce di menu **Insights** a :::image type="icon" source="media/insights/ico-insights.png" border="false"::: sinistra per accedere all'area di **Insights** .
    
1. In **Insights** selezionare la scheda **estensioni di file** .

    Il report visualizza un riepilogo del numero di estensioni di file univoche trovate, nonché un grafico delle prime 10 estensioni trovate, nell'intervallo di tempo selezionato (valore predefinito: 30 giorni).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Report estensione file-Panoramica" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Per ulteriori informazioni, effettuare una delle operazioni seguenti:

    - Selezionare il selettore di **tempo** nella parte superiore del report per modificare l'intervallo di tempo per cui sono state trovate le estensioni di file.
    
    - Selezionare **Visualizza più** sotto il grafico per visualizzare un elenco completo delle estensioni di file trovate. Per altre informazioni, vedere [drill-down di file Extension Insights](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Drill-down delle informazioni dettagliate sull'estensione di file

Una volta visualizzate le informazioni di alto livello sui tipi di file presenti nei dati, eseguire il drill-down per altri dettagli sulla posizione in cui si trovano e se è possibile eseguirne l'analisi per i dati sensibili.

Ad esempio:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Report estensione file-drill-down" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

La griglia Mostra i dettagli per ogni estensione di file trovata, tra cui:

- **Numero di file**. Numero di file con l'estensione specificata.
- **Analisi del contenuto**. Indica se l'estensione di file è supportata per l'analisi di dati sensibili.
- **Sottoscrizioni**. Numero di sottoscrizioni in cui è stata trovata l'estensione specificata.
- **Origini**. Numero di origini trovate con l'estensione di file specificata.



Usare i filtri sopra la griglia per filtrare i dati visualizzati:

|Opzione  |Descrizione  |
|---------|---------|
|**Filtra per parola chiave**     |    Immettere il testo nella casella **Filtra per parola chiave**  per visualizzare il filtro dei tipi di file in base al nome. Ad esempio, per visualizzare solo i file PDF, immettere `PDF` .     |
|**Time**        | Selezionare questa impostazione per filtrare in base a un intervallo di tempo specifico per la creazione dei dati. <br>**Impostazione predefinita:** 30 giorni  |
|**Estensione file**     |Selezionare questa casella per filtrare la griglia in base a uno o più tipi di file.        |
|**recenti**    |Selezionare questa casella per filtrare la griglia in base alle origini dati specifiche. |
|**Analisi dei contenuti**     |Selezionare questa scelta per scegliere **supportata** o **non supportata** per visualizzare solo i tipi di file che possono essere analizzati ulteriormente per i dati sensibili o i dati che non possono essere analizzati, ad esempio i file con **estensione CERT** o **jpg** . |
| | |

Sopra i filtri selezionare **modifica colonne** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: per visualizzare più o meno colonne nella griglia o per modificare l'ordine. 

Per ordinare la griglia, selezionare un'intestazione di colonna per eseguire l'ordinamento in base a tale colonna.
## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui report Insight di Azure
> [!div class="nextstepaction"]
> [Informazioni dettagliate sul glossario](glossary-insights.md)

> [!div class="nextstepaction"]
> [Analizza informazioni dettagliate](scan-insights.md)

> [!div class="nextstepaction"]
> [Informazioni dettagliate sulla classificazione](./classification-insights.md)

> [!div class="nextstepaction"]
> [Insights label Sensitivity](sensitivity-insights.md)
