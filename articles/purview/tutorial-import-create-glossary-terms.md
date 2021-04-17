---
title: 'Esercitazione: Creare e importare termini di glossario in Azure Purview (anteprima)'
description: Questa esercitazione descrive come creare termini di glossario, aggiungerli a un asset e importarli.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: b240806b934cae54810cf9dfe1a6c1f369cede7e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587511"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Esercitazione: Creare e importare termini di glossario in Azure Purview (anteprima)

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

Il glossario è uno strumento importante per mantenere e organizzare un catalogo. Per creare un glossario, si definiscono nuovi termini oppure si importa un elenco di termini che poi vengono applicati agli asset.

Questa è la *quinta parte di una serie di esercitazioni in cinque parti* contenenti le informazioni di base per gestire la governance dell'intera piattaforma dati usando Azure Purview. Questa esercitazione si basa sull'attività completata nella [Parte 4: Esplorare set di risorse, dettagli, schemi e classificazioni in Azure Purview (anteprima)](tutorial-schemas-and-classifications.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare termini di glossario.
> * Aggiungere i termini di glossario a un asset.
> * Importare termini di glossario.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione: Esplorare set di risorse, dettagli, schemi e classificazioni in Azure Purview (anteprima)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Creare termini di glossario

Nel glossario è possibile creare termini aziendali o tecnici. È anche possibile annotare gli asset applicandovi termini.

Ecco un riepilogo dei campi più comuni presenti nella pagina **Termini di glossario**:

* **Stato**: assegnare uno stato al termine, ovvero **Bozza**, **Approvato**, **Scaduto** o **Avviso**.

* **Definizione**: immettere una definizione del termine.

* **Acronimo**: immettere una versione abbreviata del termine usando le iniziali di ogni parola.

* **Sinonimi**: selezionare altri termini con definizioni uguali o simili.

* **Termini correlati**: selezionare altri termini del glossario correlati a quello in uso ma con definizioni diverse. Gli esempi includono termini tecnici correlati a un termine aziendale, a un nome in codice o ad altri termini associabili.

Per creare un termine di glossario, seguire questa procedura:

1. Passare alla risorsa Azure Purview nel portale di Azure e selezionare **Apri in Purview Studio**. Si viene automaticamente reindirizzati nella home page di Purview Studio.

1. Selezionare **Glossario** nel riquadro sinistro per aprire la pagina **Termini di glossario**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Screenshot che mostra la pagina Termini di glossario.":::

1. Selezionare **Nuovo termine** > **Predefinito di sistema** > **Continua**.

1. Nella scheda **Panoramica** della pagina **Nuovo termine** immettere il **Nome** per il nuovo termine: *Financial*.

1. Immettere la **Definizione**: *This term represents financial information for Contoso Inc.*

1. Nell'elenco a discesa **Stato** selezionare **Approvato**.

1. Al termine, selezionare **Crea**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Screenshot che mostra come creare un nuovo termine di glossario.":::

## <a name="add-glossary-terms-to-an-asset"></a>Aggiungere i termini di glossario a un asset

1. Seguire la procedura descritta nella [parte 1 di questa serie di esercitazioni](tutorial-scan-data.md) per trovare un asset, ad esempio **Contoso_CashFlow_{N}.csv**.

1. Nella pagina Dettagli selezionare **Modifica**.

1. Nell'elenco a discesa **Termini di glossario** della scheda **Panoramica** selezionare **Financial** e quindi **Salva**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Screenshot che mostra come aggiungere un termine di glossario a un asset.":::

1. Passare alla sezione **Termini di glossario** della scheda **Panoramica** e notare che il termine *Financial* è stato applicato all'asset.

## <a name="import-glossary-terms"></a>Importare termini di glossario

Per importare o aggiornare i termini esistenti in blocco, caricare un modello prepopolato nel glossario.

In questa procedura i termini di glossario vengono importati tramite un file CSV:

1. Ricordarsi dove è stato archiviato il file *StarterKitTerms.csv*, che fa parte dello starter kit scaricato nella [parte 1 di questa esercitazione](tutorial-scan-data.md).

   Questo file contiene un elenco di termini prepopolati pertinenti per i dati.

 > [!Important]
   > Indirizzo di posta elettronica per amministratori ed esperti in . Il file CSV deve essere l'indirizzo primario dell'utente del gruppo di AAD. I messaggi di posta elettronica alternativi, il nome dell'entità utente e i messaggi di posta elettronica non AAD non sono ancora supportati. È necessario sostituire gli indirizzi di posta elettronica con l'indirizzo primario di AAD dell'organizzazione.

1. Per avviare l'importazione, selezionare **Glossario** e quindi **Import terms** (Importa termini).

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Screenshot che mostra come importare termini di glossario.":::

1. Nella pagina **Import terms** selezionare **Predefinito di sistema** e quindi **Continua**.

1. Selezionare **Sfoglia**, passare al percorso in cui è stato scaricato il file *StarterKitTerms.csv* e quindi selezionare **Apri**.

1. Selezionare **OK** per avviare l'importazione dei termini.

   Al termine dell'importazione, i nuovi termini di glossario vengono visualizzati nella pagina **Termini di glossario**.

1. Visualizzare ogni termine appena importato per verificarne la definizione.

## <a name="create-custom-term-templates"></a>Creare modelli di termini personalizzati

Questa sezione descrive come creare un modello di termini personalizzati con attributi personalizzati e come importare dati usando un file di modello in formato CSV.

Sono disponibili diversi modelli di termini di sistema, che è possibile visualizzare selezionando **Glossario** > **Manage term templates (Gestisci modelli di termini)**  > **Sistema**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="Modelli di termini di sistema.":::

Per creare un nuovo modello di termini, seguire questa procedura:

1. Selezionare **Glossario** nel menu a sinistra.
1. Selezionare **Manage term templates** > **Custom** > **New term template** (Gestisci modelli di termini > Personalizza > Nuovo modello di termini)

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="Creare un nuovo modello di termini personalizzato.":::

Nella schermata **New term template** (Nuovo modello di termini) seguire questa procedura:

1. Immettere il **nome del modello**: `Sensitivity level`.
1. Immettere la descrizione, ad esempio `Indicates the level of sensitivity for this data.`
1. Selezionare **+ Nuovo attributo** per aprire una finestra di dialogo per l'aggiunta di attributi.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Schermata iniziale per la creazione di un nuovo modello di termini.":::

1. Nella schermata **Nuovo attributo** immettere i parametri seguenti:

   |Impostazione|Valore consigliato|
   |---------|-----------|
   |Nome attributo |is sensitive information|
   |Tipo di campo | Single choice|
   |Mark as required | selezionare questa casella.|
   |+ Add a choice | Aggiungere due opzioni, "Sì" e "No".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Aggiungere un nuovo attributo.":::

1. Ripetere i passaggi precedenti per aggiungere un altro attributo denominato `can be shared externally`. Dopo aver aggiunto entrambi gli attributi, selezionare **Crea**.

## <a name="import-terms-from-a-template"></a>Importare termini da un modello

Ora importare un nuovo termine usando il modello **Sensitivity level** creato. 

1. Nella schermata **Termini di glossario** selezionare **Import terms** (Importa termini).

1. Selezionare **Sensitivity level** nella schermata **Import terms**. Selezionare **Continua**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Selezionare Sensitivity level.":::

1. Il modello di termini per **Sensitivity level** contiene attributi di sistema predefiniti, oltre ai nuovi attributi aggiunti, `can be shared externally` e `is sensitive information`. Selezionare **Download a sample .CSV** file (Scaricare un file CSV di esempio).

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Scaricare un file CSV di esempio.":::

1. Viene scaricato un file di modello che è possibile modificare per caricare un nuovo termine di glossario con attributi personalizzati. Aprire il file CSV scaricato. Aggiungere nuovi termini e i relativi valori appropriati come nuove righe nel file CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Creare un termine nel file CSV.":::

   I termini elencati nella colonna **Termini correlati** o **Sinonimi** che non esistono già verranno creati quando viene caricato il file. Verranno creati usando il modello **Predefinito di sistema**.

1. Per caricare il file, tornare nella schermata **Import terms** (Importa termini) e selezionare **Sfoglia** accanto alla casella **Select the complete .CSV file to upload** (Selezionare il file CSV completo da caricare). Selezionare il file nella finestra di dialogo visualizzata e quindi selezionare **OK**.

1. I nuovi termini sono ora elencati nella schermata **Termini di glossario**. È possibile visualizzare i dettagli sui nuovi termini facendo clic sui relativi nomi nell'elenco.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare termini di glossario.
> * Aggiungere i termini di glossario a un asset.
> * Importare termini di glossario.

Passare all'articolo successivo che descrive le diverse informazioni dettagliate del catalogo.

> [!div class="nextstepaction"]
> [Informazioni dettagliate in Azure Purview](concept-insights.md)
