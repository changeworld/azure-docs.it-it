---
title: Introduzione allo sviluppo avanzato di Azure Percept nel cloud
description: Inizia a usare lo sviluppo avanzato nel cloud tramite notebook Jupyter e Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664799"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Introduzione allo sviluppo avanzato nel cloud tramite notebook Jupyter e Azure Machine Learning

Questo articolo illustra il processo di configurazione di un'area di lavoro Azure Machine Learning, il caricamento di un esempio preconfigurato Jupyter Notebook nell'area di lavoro, la creazione di un'istanza di calcolo e l'esecuzione delle celle del notebook nell'area di lavoro.

Il [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) esegue l'apprendimento del trasferimento usando un modello di TensorFlow con training preliminare (MobileNetSSDV2Lite) in AzureML in Python con un set di dati personalizzato per rilevare le ciotole.

Il notebook Mostra come iniziare da [Coco](https://cocodataset.org/#home), filtrarlo fino alla classe di interesse (Bowl), quindi convertirlo nel formato appropriato. In alternativa, è possibile usare lo strumento open source [VoTT 2](https://github.com/microsoft/VoTT) per l'assegnazione di etichette per creare ed etichettare i rettangoli di delimitazione nel formato Pascal VOC.

Dopo aver rieseguito il training del modello nel set di dati personalizzato, è possibile distribuire il modello in Azure Percept DK usando il metodo di aggiornamento del modulo gemello.

È quindi possibile controllare l'inferenza del modello visualizzando il flusso RTSP Live dal modello di Azure Percept Vision SoM. Sia la ripetizione del training del modello che la distribuzione vengono eseguite all'interno del notebook nel cloud.

## <a name="prerequisites"></a>Prerequisiti

- [Sottoscrizione di Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK con Azure Percept Vision SoM connesso](./overview-azure-percept-dk.md)
- [Esperienza di onboarding di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md) completata

## <a name="download-azure-percept-github-repository"></a>Scaricare il repository GitHub di Azure Percept

1. Passare al [repository GitHub di Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Clonare il repository o scaricare il file ZIP. Nella parte superiore della schermata fare clic su **codice**  ->  **Scarica zip**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Schermata di download di GitHub.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Configurare Azure Machine Learning portale e notebook

1. Passare al [portale di Azure Machine Learning](https://ml.azure.com).

1. Selezionare la directory, la sottoscrizione di Azure e l'area di lavoro Machine Learning dai menu a discesa e fare clic su **Introduzione**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Schermata iniziale di Azure ML.":::

    Se non si dispone ancora di un'area di lavoro Azure Machine Learning, fare clic su **Crea una nuova area di lavoro**. Nella scheda nuovo browser eseguire le operazioni seguenti:

    1. Selezionare la sottoscrizione di Azure.
    1. Selezionare un gruppo di risorse.
    1. Immettere un nome per l'area di lavoro.
    1. Selezionare un'area.
    1. Selezionare l'edizione dell'area di lavoro.
    1. Fare clic su **Rivedi e crea**.
    1. Nella pagina successiva rivedere le selezioni e fare clic su **Crea**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Schermata di creazione dell'area di lavoro in Azure ML.":::

    Attendere alcuni minuti per la creazione dell'area di lavoro. Al termine della creazione dell'area di lavoro, vengono visualizzati segni di spunta verdi accanto alle risorse e la **distribuzione viene completata** nella parte superiore della pagina Panoramica Machine Learning Services.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Conferma della creazione dell'area di lavoro." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Al termine della creazione dell'area di lavoro, tornare alla scheda portale di machine learning e fare clic su **Introduzione**.

1. Nella Home page dell'area di lavoro di Machine Learning fare clic su **Notebooks (notebook** ) nel riquadro a sinistra.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Home page di Azure ML.":::

1. Nella scheda **file** fare clic sulla freccia verticale per caricare il file con estensione ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Homepage che evidenzia l'icona Carica file.":::

1. Passare a e selezionare il [file Transferlearningusing_SSDLiteV2 Model. ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) dalla copia locale del repository Azure Percept github. Fare clic su **Apri**. Nella finestra **Carica file** selezionare la casella accanto a **Considera attendibile il contenuto di questo file** e fare clic su **carica**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Schermata di selezione file.":::

1. Nella barra dei menu in alto a destra controllare lo stato di **calcolo** . Se non viene trovato alcun calcolo, fare clic sull' **+** icona per creare un nuovo calcolo.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Stato di calcolo con icona + evidenziato." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Nella finestra **nuova istanza di calcolo** immettere un nome di **calcolo**, scegliere un tipo di **macchina virtuale** e selezionare le dimensioni della **macchina virtuale**. Fare clic su **Crea**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Nuova schermata di creazione dell'istanza di calcolo.":::

    Quando si fa clic su **Crea**, lo stato di **calcolo** visualizzerà un cerchio blu e **\<your compute name> -Creating**

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Stato di calcolo quando la creazione del calcolo è ancora in corso.":::

    Lo stato di **calcolo** visualizzerà un cerchio verde e, **\<your compute name> in esecuzione** , dopo il completamento della creazione del calcolo.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="Stato di calcolo che mostra la creazione del calcolo completata.":::

1. Quando il calcolo è in esecuzione, selezionare il kernel **Python 3,6-AzureML** dal menu a discesa accanto all' **+** icona.

## <a name="working-with-the-notebook"></a>Uso del notebook

A questo punto è possibile eseguire il notebook per eseguire il training del rilevatore Bowl personalizzato e distribuirlo nella devkit. Assicurarsi di eseguire singolarmente ogni cella del notebook perché alcune celle richiedono parametri di input prima di eseguire lo script. Le celle che richiedono parametri di input possono essere modificate direttamente nel notebook. Per eseguire una cella, fare clic sull'icona di riproduzione sul lato sinistro della cella:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Icona della cella di evidenziazione del notebook." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Passaggi successivi

Per altri notebook di esempio del servizio Azure Machine Learning, visitare questo [repository](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
