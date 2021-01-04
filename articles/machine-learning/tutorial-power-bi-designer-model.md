---
title: 'Esercitazione: Creare il modello predittivo con trascinamento della selezione (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire un modello predittivo di Machine Learning con la finestra di progettazione, in modo da poterlo usare per prevedere i risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370662"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Esercitazione:  Integrazione di Power BI: creare il modello predittivo con trascinamento della selezione (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando la finestra di progettazione di Azure Machine Learning, un'interfaccia utente con trascinamento della selezione a basso contenuto di codice. Nella parte 2 si userà quindi il modello per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'istanza di calcolo di Azure Machine Learning
> * Creare un cluster di inferenza di Azure Machine Learning
> * Creare un set di dati
> * Eseguire il training di un modello di regressione
> * Distribuire il modello in un endpoint di assegnazione punteggi in tempo reale


Esistono tre modi diversi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione.  Questa opzione mostra un'esperienza di creazione a basso contenuto di codice usando la finestra di progettazione, un'interfaccia utente con trascinamento della selezione.  

In alternativa è possibile scegliere:

* [Opzione A: Eseguire il training e la distribuzione di modelli tramite notebook](tutorial-power-bi-custom-model.md), un'esperienza di creazione code-first tramite notebook di Jupyter ospitati in Azure Machine Learning Studio.
* [Opzione C: Eseguire il training e la distribuzione di modelli usando ML automatizzato](tutorial-power-bi-automated-model.md), un'esperienza di creazione senza codice che automatizza completamente la preparazione dei dati e il training dei modelli.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure ([è disponibile una versione di valutazione gratuita](https://aka.ms/AMLFree)). 
- Un'area di lavoro di Azure Machine Learning. Se non è già disponibile un'area di lavoro, vedere l'articolo su [come creare un'area di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conoscenze di base dei flussi di lavoro di Machine Learning.


## <a name="create-compute-for-training-and-scoring"></a>Creare un'istanza di calcolo per il training e l'assegnazione di punteggi

In questa sezione si crea un'*istanza di calcolo*, che viene usata per il training dei modelli di Machine Learning. Si crea anche un *cluster di inferenza* che verrà usato per ospitare il modello distribuito per l'assegnazione di punteggi in tempo reale.

Accedere allo [studio di Azure Machine Learning](https://ml.azure.com) e selezionare **Calcolo** nel menu a sinistra, quindi **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Screenshot che mostra come creare un'istanza di calcolo":::

Nella schermata **Crea istanza di calcolo** risultante selezionare le dimensioni della VM. Per questa esercitazione, selezionare `Standard_D11_v2` e quindi **Avanti**. Nella pagina Impostazioni specificare un nome valido per l'istanza di calcolo, quindi selezionare **Crea**. 

>[!TIP]
> L'istanza di calcolo può essere usata anche per creare ed eseguire notebook.

È ora possibile vedere che la colonna **Stato** dell'istanza di calcolo indica **Creazione**. Il provisioning della macchina virtuale richiederà circa 4 minuti. Durante l'attesa, selezionare la scheda **Cluster di inferenza** scheda nella pagina dell'istanza di calcolo e quindi **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Screenshot che mostra come creare un cluster di inferenza":::

Nella pagina **Crea cluster di inferenza** risultante selezionare un'area seguita dalle dimensioni della VM. Per questa esercitazione, selezionare `Standard_D11_v2`, quindi **Avanti**. Nella pagina **Configura impostazioni**:

1. Specificare un nome di ambiente di calcolo valido
1. Selezionare **Sviluppo/test** come scopo del cluster. Verrà creato un singolo nodo in cui ospitare il modello distribuito.
1. Selezionare **Crea**

È ora possibile vedere che la colonna **Stato** del cluster di inferenza indica **Creazione**. La distribuzione del cluster a nodo singolo richiederà circa 4 minuti.

## <a name="create-a-dataset"></a>Creare un set di dati

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), disponibile nei [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/).

Per creare il set di dati, selezionare il menu **Set di dati** a sinistra e quindi **Crea set di dati**. Verranno visualizzate le opzioni seguenti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot che mostra come creare un nuovo set di dati":::

Selezionare **Da set di dati aperti** e quindi nella schermata **Crea set di dati dai set di dati aperti**:

1. Cercare *diabetes* usando la barra di ricerca
1. Selezionare **Sample: Diabetes**
1. Selezionare **Avanti**
1. Specificare un nome per il set di dati, ovvero *diabetes*
1. Selezionare **Crea**

Per esplorare i dati, selezionare il set di dati e quindi **Esplora**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot che mostra come esplorare il set di dati":::

I dati includono 10 variabili di input della baseline, ad esempio età, sesso, indice di massa corporea, pressione sanguigna media e sei misurazioni sierologiche, nonché una variabile di destinazione denominata **Y** (misura quantitativa della progressione del diabete un anno dopo la baseline).

## <a name="create-a-machine-learning-model-using-designer"></a>Creare un modello di Machine Learning con la finestra di progettazione

Dopo aver creato l'istanza di calcolo e i set di dati, è possibile passare alla creazione del modello di Machine Learning usando la finestra di progettazione. Nello studio di Azure Machine Learning selezionare **Finestra di progettazione** e quindi **Nuova pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Screenshot che mostra come creare una nuova pipeline":::

Viene visualizzata un'*area di disegno vuota* che contiene anche un **menu Impostazioni**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Screenshot che mostra come selezionare una destinazione di calcolo":::

Nel **menu Impostazioni** scegliere **Seleziona destinazione di calcolo**, selezionare l'istanza di calcolo creata in precedenza e quindi **Salva**. In **Nome della bozza** specificare un nome più significativo, ad esempio *diabetes-model*, e immettere una descrizione.

Quindi negli asset elencati espandere **Set di dati** e individuare il set di dati **diabetes**. Trascinare questo modulo nell'area di disegno:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Screenshot che illustra come trascinare un componente":::

Trascinare quindi i componenti seguenti nell'area di disegno:

1. Regressione lineare (in **Algoritmi di Machine Learning**)
1. Training modello (in **Training modello**)

L'area di disegno avrà un aspetto analogo al seguente. Si noti che nella parte superiore e inferiore dei componenti sono presenti piccoli cerchi denominati porte, evidenziati in rosso di seguito:

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Screenshot che mostra i componenti non connessi":::
 
Successivamente, è necessario *collegare* questi componenti tra loro. Selezionare la porta nella parte inferiore del set di dati **diabetes** e trascinarla sulla porta a destra nella parte superiore del componente **Training modello**. Selezionare la porta nella parte inferiore del componente **Regressione lineare** e trascinarla sulla porta a sinistra nella parte superiore della porta **Training modello**.

Scegliere la colonna nel set di dati da usare come variabile di etichetta (destinazione) da prevedere. Selezionare il componente **Training modello** e quindi **Modifica colonna**. Nella finestra di dialogo selezionare **Immettere il nome di colonna** e quindi **Y** nell'elenco a discesa:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Screenshot che mostra come selezionare la colonna etichetta":::

Selezionare **Salva**. Il *flusso di lavoro* di Machine Learning sarà simile al seguente:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Screenshot che mostra i componenti connessi":::

Selezionare **Invia** e quindi **Crea nuovo** in Esperimento. Specificare un nome per l'esperimento, quindi selezionare **Invia**.

>[!NOTE]
> La prima esecuzione dell'esperimento dovrebbe richiedere circa 5 minuti. Le esecuzioni successive sono molto più veloci: la finestra di progettazione memorizza nella cache i componenti già eseguiti per ridurre la latenza.

Al termine dell'esperimento, viene visualizzato quanto segue:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Screenshot che mostra l'esecuzione completata":::

È possibile esaminare i log dell'esperimento selezionando **Training modello** e quindi **Output e log**.

## <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire il modello, selezionare **Crea pipeline di inferenza** (nella parte superiore dell'area di disegno) e quindi **Pipeline di inferenza in tempo reale**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Screenshot che mostra la pipeline di inferenza in tempo reale":::
 
La pipeline include solo i componenti necessari per l'assegnazione di punteggi al modello. Quando si assegnano punteggi ai dati, i valori delle variabili di destinazione non saranno noti, quindi è possibile rimuovere **Y** dal set di dati. Per rimuovere Y, aggiungere all'area di disegno un componente **Selezionare le colonne nel set di dati**. Collegare il componente in modo che il set di dati diabetes corrisponda all'input e i risultati corrispondano all'output del componente **Punteggio del modello**:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Screenshot che mostra la rimozione di una colonna":::

Selezionare il componente **Selezionare le colonne nel set di dati** nell'area di disegno e quindi **Modifica colonne**. Nella finestra di dialogo Seleziona colonne selezionare **Per nome** e quindi assicurarsi che siano selezionate tutte le variabili di input, ma **non** la destinazione:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Screenshot che mostra la rimozione di una colonna":::

Selezionare **Salva**. Infine, selezionare il componente **Punteggio del modello** e assicurarsi che la casella di controllo **Append score columns to output** (Aggiungi le colonne del punteggio all'output) sia deselezionata. Vengono restituite solo le previsioni invece che gli input *e* le previsioni, riducendo la latenza:

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Screenshot che mostra le impostazioni del componente Punteggio del modello":::

Selezionare **Invia** nella parte superiore dell'area di disegno.

Una volta eseguita correttamente la pipeline di inferenza, è possibile distribuire il modello nel cluster di inferenza. Selezionare **Distribuisci**. Viene visualizzata la finestra di dialogo **Configura endpoint in tempo reale**. Selezionare **Distribuisci nuovo endpoint in tempo reale**, assegnare all'endpoint il nome **my-diabetes-model**, selezionare l'inferenza creata in precedenza, quindi selezionare **Distribuisci**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Screenshot che mostra le impostazioni dell'endpoint in tempo reale":::
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come eseguire il training e distribuire un modello nella finestra di progettazione. Nella parte successiva viene descritto come utilizzare questo modello in Power BI per assegnare il punteggio.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
