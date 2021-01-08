---
title: 'Esercitazione: Creare il modello predittivo con ML automatizzato (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire modelli di Machine Learning automatizzato, in modo da poter usare il modello migliore per prevedere risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814806"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Esercitazione: Integrazione di Power BI: creare il modello predittivo usando Machine Learning automatizzato (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando Machine Learning (ML) automatizzato nello studio di Azure Machine Learning.  Nella parte 2 si userà il modello con prestazioni migliori per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un cluster di elaborazione di Azure Machine Learning.
> * Creare un set di dati.
> * Creare un'esecuzione di Machine Learning automatizzato.
> * Distribuire il modello migliore in un endpoint di assegnazione punteggi in tempo reale.


Esistono tre modi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione C: Eseguire il training e la distribuzione di modelli con Machine Learning automatizzato nello studio.  Questa opzione offre un'esperienza di creazione senza codice. Automatizza completamente la preparazione dei dati e il training dei modelli. 

Tuttavia, è possibile usare una delle altre opzioni:

* [Opzione A: Eseguire il training e la distribuzione di modelli con notebook di Jupyter](tutorial-power-bi-custom-model.md). Questa esperienza di creazione code-first usa notebook di Jupyter ospitati nello studio di Azure Machine Learning.
* [Opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione di Azure Machine Learning](tutorial-power-bi-designer-model.md). Questa esperienza di creazione a basso contenuto di codice usa un'interfaccia utente con trascinamento della selezione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile creare una [versione di valutazione gratuita](https://aka.ms/AMLFree). 
- Un'area di lavoro di Azure Machine Learning. Se non si ha un'area di lavoro, vedere [Creare e gestire le aree di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Creare un cluster di elaborazione

Machine Learning automatizzato esegue il training di molti modelli diversi di Machine Learning per trovare l'algoritmo e i parametri migliori. Azure Machine Learning parallelizza l'esecuzione del training dei modelli in un cluster di elaborazione.

Per iniziare, nel menu a sinistra dello [studio di Azure Machine Learning](https://ml.azure.com) selezionare **Calcolo**. Aprire la scheda **Cluster di elaborazione**. Quindi selezionare **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Screenshot che mostra come creare un cluster di elaborazione.":::

Nella pagina **Crea cluster di elaborazione**:

1. Selezionare una dimensione di VM. Per questa esercitazione, è possibile selezionare una VM **Standard_D11_v2**.
1. Selezionare **Avanti**.
1. Specificare un nome di ambiente di calcolo valido.
1. Mantenere **Numero minimo di nodi** impostato su `0`.
1. Impostare **Numero massimo di nodi** su `4`.
1. Selezionare **Crea**.

Lo stato del cluster diventa **In fase di creazione**.

>[!NOTE]
> Il nuovo cluster ha 0 nodi, quindi non si incorre in costi di calcolo. Vengono addebitati costi solo quando viene eseguito il processo di Machine Learning automatizzato. Il numero di nodi del cluster torna automaticamente a 0 dopo 120 secondi di inattività.


## <a name="create-a-dataset"></a>Creare un set di dati

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Questo set di dati è disponibile nei [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/).

Per creare il set di dati, nel menu a sinistra selezionare **Set di dati**. Quindi selezionare **Crea set di dati**. Vengono visualizzate le opzioni seguenti:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot che mostra come creare un nuovo set di dati.":::

Selezionare **Da set di dati aperti**. Quindi nella pagina **Crea set di dati dai set di dati aperti**:

1. Usare la barra di ricerca per trovare *diabetes*.
1. Selezionare **Sample: Diabetes**.
1. Selezionare **Avanti**.
1. Assegnare al set di dati il nome *diabetes*.
1. Selezionare **Crea**.

Per esplorare i dati, selezionare il set di dati e quindi **Esplora**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot che mostra come esplorare il set di dati.":::

I dati contengono 10 variabili di input di base, ad esempio età, sesso, indice di massa corporea, pressione sanguigna media e sei misurazioni del siero. Contengono inoltre una variabile di destinazione denominata **Y**. Questa variabile di destinazione è una misura quantitativa della progressione del diabete un anno dopo quello di riferimento.

## <a name="create-an-automated-machine-learning-run"></a>Creare un'esecuzione di Machine Learning automatizzato

Nel menu a sinistra dello [studio di Azure Machine Learning](https://ml.azure.com) selezionare **ML automatizzato**. Quindi selezionare **Nuova esecuzione di ML automatizzato**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Screenshot che mostra come creare una nuova esecuzione di Machine Learning automatizzato.":::

Selezionare quindi il set di dati **diabetes** creato in precedenza. Selezionare **Avanti**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Screenshot che mostra come selezionare un set di dati.":::
 
Nella pagina **Configura esecuzione**:

1. In **Nome esperimento** selezionare **Crea nuovo**.
1. Assegnare un nome all'esperimento.
1. Nel campo **Colonna di destinazione** selezionare **Y**.
1. Nel campo **Seleziona cluster di elaborazione** selezionare il cluster di elaborazione creato in precedenza. 

Il modulo completato sarà simile al seguente:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Screenshot che mostra come configurare Machine Learning automatizzato.":::

Infine, selezionare un'attività di Machine Learning. In questo caso, l'attività è **Regressione**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Screenshot che mostra come configurare un'attività.":::

Selezionare **Fine**.

> [!IMPORTANT]
> Per completare il training dei 100 modelli in Machine Learning automatizzato, sono necessari circa 30 minuti.

## <a name="deploy-the-best-model"></a>Distribuire il modello migliore

Al termine dell'esecuzione di Machine Learning automatizzato, è possibile visualizzare tutti i modelli usati per i tentativi selezionando la scheda **Modelli**. I modelli vengono ordinati in base alle prestazioni. Il modello con le prestazioni migliori verrà visualizzato per primo. Dopo aver selezionato il modello migliore, il pulsante **Distribuisci** risulterà abilitato:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Screenshot che mostra l'elenco di modelli.":::

Selezionare **Distribuisci** per aprire la finestra **Distribuisci un modello**:

1. Assegnare al modello il nome *diabetes-model*.
1. Selezionare **Servizio Azure Container**.
1. Selezionare **Distribuisci**.

Verrà visualizzato un messaggio che indica che il modello è stato distribuito correttamente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come eseguire il training e la distribuzione di un modello di Machine Learning usando Machine Learning automatizzato. Nell'esercitazione successiva viene descritto come utilizzare questo modello in Power BI per assegnare punteggi.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
