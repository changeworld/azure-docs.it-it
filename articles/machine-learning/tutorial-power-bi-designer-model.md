---
title: 'Esercitazione: Creare il modello predittivo con trascinamento della selezione (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire un modello predittivo di Machine Learning usando la finestra di progettazione. In seguito è possibile usarlo per prevedere risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814789"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Esercitazione: Integrazione di Power BI: creare il modello predittivo con trascinamento della selezione (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando la finestra di progettazione di Azure Machine Learning. La finestra di progettazione è un'interfaccia utente con trascinamento della selezione a basso contenuto di codice. Nella parte 2 si userà il modello per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'istanza di calcolo di Azure Machine Learning.
> * Creare un cluster di inferenza di Azure Machine Learning.
> * Creare un set di dati.
> * Eseguire il training di un modello di regressione.
> * Distribuire il modello in un endpoint di assegnazione punteggi in tempo reale.


Esistono tre modi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione.  Questa opzione offre un'esperienza di creazione a basso contenuto di codice che usa l'interfaccia della finestra di progettazione.  

Tuttavia, è possibile usare una delle altre opzioni:

* [Opzione A: Eseguire il training e la distribuzione di modelli con notebook di Jupyter](tutorial-power-bi-custom-model.md). Questa esperienza di creazione code-first usa notebook di Jupyter ospitati nello studio di Azure Machine Learning.
* [Opzione C: Eseguire il training e la distribuzione di modelli con Machine Learning automatizzato](tutorial-power-bi-automated-model.md). Questa esperienza di creazione senza codice automatizza completamente la preparazione dei dati e il training dei modelli.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile creare una [versione di valutazione gratuita](https://aka.ms/AMLFree). 
- Un'area di lavoro di Azure Machine Learning. Se non si ha un'area di lavoro, vedere [Creare e gestire le aree di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conoscenze di base dei flussi di lavoro di Machine Learning.


## <a name="create-compute-to-train-and-score"></a>Creare l'istanza di calcolo per il training e l'assegnazione di punteggi

In questa sezione si crea un'*istanza di calcolo*. Le istanze di calcolo vengono usate per eseguire il training di modelli di Machine Learning. Si crea anche un *cluster di inferenza* in cui ospitare il modello distribuito per l'assegnazione di punteggi in tempo reale.

Accedere allo [studio di Azure Machine Learning](https://ml.azure.com). Nel menu a sinistra selezionare **Calcolo** e quindi **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Screenshot che mostra come creare un'istanza di calcolo.":::

Quindi, nella pagina **Crea istanza di calcolo** selezionare le dimensioni di una VM. Per questa esercitazione, selezionare una VM **Standard_D11_v2**. Fare quindi clic su **Avanti**. 

Nella pagina **Impostazioni** assegnare un nome all'istanza di calcolo. Selezionare quindi **Crea**. 

>[!TIP]
> È anche possibile usare l'istanza di calcolo per creare ed eseguire notebook.

Per l'istanza di calcolo la colonna **Stato** indica ora **In fase di creazione**. Il provisioning della macchina virtuale richiede circa 4 minuti. 

Durante l'attesa, nella pagina **Calcolo** selezionare la scheda **Cluster di inferenza**. Quindi selezionare **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Screenshot che mostra come creare un cluster di inferenza.":::

Nella pagina **Crea cluster di inferenza** selezionare un'area e le dimensioni di una VM. Per questa esercitazione, selezionare una VM **Standard_D11_v2**. Fare quindi clic su **Avanti**. 

Nella pagina **Configura impostazioni**:

1. Specificare un nome di ambiente di calcolo valido.
1. Selezionare **Sviluppo/test** come scopo del cluster. Questa opzione crea un singolo nodo in cui ospitare il modello distribuito.
1. Selezionare **Crea**.

Per il cluster di inferenza la colonna **Stato** indica ora **In fase di creazione**. La distribuzione del cluster a nodo singolo richiede circa 4 minuti.

## <a name="create-a-dataset"></a>Creare un set di dati

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Questo set di dati è disponibile nei [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/).

Per creare il set di dati, nel menu a sinistra selezionare **Set di dati**. Quindi selezionare **Crea set di dati**. Vengono visualizzate le opzioni seguenti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot che mostra come creare un nuovo set di dati.":::

Selezionare **Da set di dati aperti**. Nella pagina **Crea set di dati dai set di dati aperti**:

1. Usare la barra di ricerca per trovare *diabetes*.
1. Selezionare **Sample: Diabetes**.
1. Selezionare **Avanti**.
1. Assegnare al set di dati il nome *diabetes*.
1. Selezionare **Crea**.

Per esplorare i dati, selezionare il set di dati e quindi **Esplora**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot che mostra come esplorare il set di dati.":::

I dati contengono 10 variabili di input di base, ad esempio età, sesso, indice di massa corporea, pressione sanguigna media e sei misurazioni del siero. Contengono inoltre una variabile di destinazione denominata **Y**. Questa variabile di destinazione è una misura quantitativa della progressione del diabete un anno dopo quello di riferimento.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Creare un modello di Machine Learning con la finestra di progettazione

Dopo aver creato l'istanza di calcolo e i set di dati, è possibile usare la finestra di progettazione per creare il modello di Machine Learning. Nello studio di Azure Machine Learning selezionare **Finestra di progettazione** e quindi **Nuova pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Screenshot che mostra come creare una nuova pipeline.":::

Vengono visualizzati un'*area di disegno vuota* e un menu **Impostazioni**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Screenshot che mostra come selezionare una destinazione di calcolo.":::

Nel menu **Impostazioni** scegliere **Seleziona destinazione di calcolo**. Selezionare l'istanza di calcolo creata in precedenza e quindi **Salva**. Sostituire **Nome della bozza** con un nome più significativo, ad esempio *diabetes-model*. Infine, immettere una descrizione.

Nell'elenco degli asset espandere **Set di dati** e individuare il set di dati **diabetes**. Trascinare il componente nell'area di disegno:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Screenshot che mostra come trascinare un componente nell'area di disegno.":::

Trascinare quindi i componenti seguenti nell'area di disegno:

1. **Regressione lineare** (in **Algoritmi di Machine Learning**)
1. **Training modello** (in **Training modello**)

Nell'area di disegno si notino i cerchi nella parte superiore e inferiore dei componenti. Questi cerchi sono porte.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Screenshot che mostra le porte nei componenti non connessi.":::
 
A questo punto *collegare* i componenti. Selezionare la porta nella parte inferiore del set di dati **diabetes**. Trascinarla sulla porta in alto a destra del componente **Training modello**. Selezionare la porta nella parte inferiore del componente **Regressione lineare**. Trascinarla sulla porta in alto a sinistra del componente **Training modello**.

Scegliere la colonna del set di dati da usare come variabile di etichetta (destinazione) da prevedere. Selezionare il componente **Training modello** e quindi **Modifica colonna**. 

Nella finestra di dialogo selezionare **Immetti il nome di colonna** > **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Screenshot che mostra come selezionare una colonna di etichetta.":::

Selezionare **Salva**. Il *flusso di lavoro* di Machine Learning sarà simile al seguente:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Screenshot che mostra i componenti connessi.":::

Selezionare **Submit** (Invia). In **Esperimento** selezionare **Crea nuovo**. Assegnare un nome all'esperimento e quindi selezionare **Invia**.

>[!NOTE]
> La prima esecuzione dell'esperimento dovrebbe richiedere circa 5 minuti. Le esecuzioni successive sono molto più veloci, perché la finestra di progettazione memorizza nella cache i componenti già eseguiti per ridurre la latenza.

Al termine dell'esperimento, viene aperta questa visualizzazione:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Screenshot che mostra l'esecuzione completata.":::

Per esaminare i log dell'esperimento, selezionare **Training modello** e quindi **Output e log**.

## <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire il modello, nella parte superiore dell'area di disegno selezionare **Crea pipeline di inferenza** > **Pipeline di inferenza in tempo reale**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Screenshot che mostra dove selezionare una pipeline di inferenza in tempo reale.":::
 
La pipeline include solo i componenti necessari per l'assegnazione di punteggi al modello. Quando si assegnano punteggi ai dati, non si conoscono i valori delle variabili di destinazione. È quindi possibile rimuovere **Y** dal set di dati. 

Per rimuovere **Y**, aggiungere all'area di disegno un componente **Selezionare le colonne nel set di dati**. Collegare il componente in modo che il set di dati diabetes sia l'input. I risultati sono l'output nel componente **Score Model** (Punteggio modello):

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Screenshot che mostra come rimuovere una colonna.":::

Selezionare il componente **Selezionare le colonne nel set di dati** nell'area di disegno e quindi **Modifica colonne**. 

Nella finestra di dialogo **Seleziona colonne** scegliere **Per nome**. Assicurarsi quindi che siano selezionate tutte le variabili di input, ma che la destinazione *non* sia selezionata:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Screenshot che mostra come rimuovere le impostazioni delle colonne.":::

Selezionare **Salva**. 

Infine, selezionare il componente **Score Model** (Punteggio modello) e assicurarsi che la casella di controllo **Append score columns to output** (Aggiungi colonne dei punteggi all'output) sia deselezionata. Per ridurre la latenza, le previsioni vengono restituite senza gli input.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Screenshot che mostra le impostazioni del componente Score Model.":::

Nella parte superiore dell'area di disegno selezionare **Invia**.

Una volta eseguita correttamente la pipeline di inferenza, è possibile distribuire il modello nel cluster di inferenza. Selezionare **Distribuisci**. 

Nella finestra di dialogo **Configura endpoint in tempo reale** selezionare **Distribuisci nuovo endpoint in tempo reale**. Assegnare all'endpoint il nome *my-diabetes-model*. Selezionare l'inferenza creata in precedenza e quindi **Distribuisci**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Screenshot che mostra le impostazioni dell'endpoint in tempo reale.":::
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come eseguire il training e distribuire un modello nella finestra di progettazione. Nella parte successiva viene descritto come utilizzare questo modello in Power BI per assegnare punteggi.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
