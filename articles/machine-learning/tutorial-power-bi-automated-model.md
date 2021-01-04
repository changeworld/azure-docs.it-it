---
title: 'Esercitazione: Creare il modello predittivo con Machine Learning automatizzato (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire modelli di Machine Learning automatizzato, in modo da poter usare il modello migliore per prevedere i risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370758"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Esercitazione: Integrazione di Power BI: Creare il modello predittivo usando Machine Learning automatizzato (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando Machine Learning automatizzato nello studio di Azure Machine Learning.  Nella parte 2 si userà quindi il modello con prestazioni migliori per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un cluster di elaborazione di Azure Machine Learning
> * Creare un set di dati
> * Creare un'esecuzione di Machine Learning automatizzato
> * Distribuire il modello migliore in un endpoint di assegnazione punteggi in tempo reale


Esistono tre modi diversi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione C: Eseguire il training e la distribuzione di modelli usando Machine Learning automatizzato nello studio.  Questa opzione illustra un'esperienza di creazione senza codice che automatizza completamente la preparazione dei dati e il training dei modelli. 

In alternativa è possibile scegliere:

* [Opzione A: Eseguire il training e la distribuzione di modelli tramite notebook](tutorial-power-bi-custom-model.md), un'esperienza di creazione code-first tramite notebook di Jupyter ospitati nello studio di Azure Machine Learning.
* [Opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione](tutorial-power-bi-designer-model.md), un'esperienza di creazione a basso contenuto di codice usando la finestra di progettazione, un'interfaccia utente con trascinamento della selezione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure ([è disponibile una versione di valutazione gratuita](https://aka.ms/AMLFree)). 
- Un'area di lavoro di Azure Machine Learning. Se non è già disponibile un'area di lavoro, vedere l'articolo su [come creare un'area di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Creare il cluster di elaborazione

Machine Learning automatizzato esegue automaticamente il training di numerosi modelli di Machine Learning diversi per trovare l'algoritmo e i parametri "migliori". Azure Machine Learning parallelizza l'esecuzione del training dei modelli in un cluster di elaborazione.

In [Azure Machine Learning Studio](https://ml.azure.com) selezionare **Calcolo** nel menu di sinistra e quindi la scheda **Cluster di elaborazione**. Selezionare **Nuovo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Screenshot che mostra come creare un cluster di elaborazione":::

Nella schermata **Crea cluster di elaborazione**:

1. Selezionare un valore per le dimensioni della macchina virtuale. Ai fini di questa esercitazione è possibile usare una macchina `Standard_D11_v2`.
1. Selezionare **Avanti**
1. Specificare un nome di ambiente di calcolo valido
1. Mantenere **Numero minimo di nodi** impostato su 0
1. Impostare **Numero massimo di nodi** su 4
1. Selezionare **Crea**

Lo stato del cluster è cambiato in **Creazione in corso**.

>[!NOTE]
> Il cluster creato include 0 nodi, di conseguenza non viene addebitato alcun costo di calcolo. Vengono addebitati solo i costi relativi all'esecuzione del processo di Machine Learning automatizzato. Il numero di nodi del cluster verrà impostato automaticamente su 0 dopo 120 secondi di inattività.


## <a name="create-dataset"></a>Creare set di dati

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), disponibile nel [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/).

Per creare il set di dati, selezionare il menu di sinistra **Set di dati** e quindi **Crea set di dati**. Verranno visualizzate le opzioni seguenti:

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

## <a name="create-automated-ml-run"></a>Creare l'esecuzione di Machine Learning automatizzato

In [Azure Machine Learning Studio](https://ml.azure.com) selezionare **ML automatizzato** nel menu di sinistra e quindi **Nuova esecuzione di ML automatizzato**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Screenshot che mostra come creare una nuova esecuzione di Machine Learning automatizzato":::

Selezionare quindi il set di dati **diabetes** creato in precedenza e selezionare **Avanti**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Screenshot che mostra come selezionare un set di dati":::
 
Nella schermata **Configura esecuzione**:

1. In **Nome esperimento** selezionare **Crea nuovo**
1. Assegnare un nome all'esperimento
1. Nel campo Colonna di destinazione selezionare **Y**
1. Nel campo **Seleziona cluster di elaborazione** selezionare il cluster di elaborazione creato in precedenza. 

Il modulo completato dovrebbe essere simile al seguente:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Screenshot che mostra come configurare Machine Learning automatizzato":::

È infine necessario selezionare l'attività di Machine Learning da eseguire, ovvero **Regressione**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Screenshot che mostra come configurare l'attività":::

Selezionare **Fine**.

> [!IMPORTANT]
> Il training dei 100 modelli diversi richiede circa 30 minuti in Machine Learning automatizzato.

## <a name="deploy-the-best-model"></a>Distribuire il modello migliore

Al termine dell'esecuzione di Machine Learning automatizzato, è possibile visualizzare l'elenco di tutti i diversi modelli di Machine Learning usati per i tentativi selezionando la scheda **Modelli**. I modelli vengono ordinati in base alle prestazioni. Il modello con le prestazioni migliori verrà visualizzato per primo. Quando si seleziona il modello migliore, il pulsante **Distribuisci** risulterà abilitato:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Screenshot che mostra l'elenco di modelli":::

Se si seleziona **Distribuisci**, viene visualizzata una schermata **Distribuisci un modello** :

1. Specificare un nome per il servizio modello, ovvero **diabetes-model**
1. Selezionare **Servizio Azure Container**
1. Selezionare **Distribuisci**

Verrà visualizzato un messaggio che indica che il modello è stato distribuito.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come eseguire il training e distribuire un modello di Machine Learning usando Machine Learning automatizzato. Nell'esercitazione successiva viene spiegato come utilizzare questo modello da Power BI per assegnare il punteggio.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
