---
title: Introduzione allo sviluppo avanzato di Azure Percept in locale
description: Introduzione allo sviluppo di Machine Learning locali con VS Code + notebook Jupyter in AzureML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664856"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Introduzione allo sviluppo di Machine Learning con VS Code + notebook Jupyter in AzureML

Questo articolo illustra il processo di configurazione di un'area di lavoro Azure Machine Learning, la creazione di un'istanza di calcolo, la configurazione di un ambiente di sviluppo Visual Studio Code nel computer locale e l'esecuzione delle celle di un notebook di Jupyter di esempio preconfigurato in VS Code.

Il [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) esegue l'apprendimento del trasferimento usando un modello di TensorFlow con training preliminare (MobileNetSSDV2Lite) in AzureML in Python con un set di dati personalizzato per rilevare le ciotole.

Il notebook Mostra come iniziare dal set di [dati Coco](https://cocodataset.org/#home), filtrarlo fino alla classe di interesse (ciotole) e quindi convertirlo nel formato appropriato. In alternativa, è possibile usare lo strumento open source [VoTT 2](https://github.com/microsoft/VoTT) per l'assegnazione di etichette per creare ed etichettare i rettangoli di delimitazione nel formato Pascal VOC.

Dopo aver rieseguito il training del modello nel set di dati personalizzato, è possibile distribuire il modello in Azure Percept DK usando il metodo di aggiornamento del modulo gemello. È quindi possibile controllare l'inferenza del modello visualizzando il flusso RTSP Live dal modello di Azure Percept Vision SoM. La ripetizione del training e la distribuzione del modello vengono eseguite all'interno del notebook tramite l'istanza di calcolo remota.

## <a name="prerequisites"></a>Prerequisiti

- [Sottoscrizione di Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK con Azure Percept Vision SoM connesso](./overview-azure-percept-dk.md)
- [Esperienza di onboarding di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md) completata

## <a name="download-azure-percept-github-repository"></a>Scaricare il repository GitHub di Azure Percept

1. Passare al [repository GitHub Azure PERCEPT dk](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Clonare il repository o scaricare il file ZIP. Nella parte superiore della schermata fare clic su **codice**  ->  **Scarica zip**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Schermata di download di GitHub.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Creare un'area di lavoro Azure Machine Learning e un'istanza di calcolo remota

1. Passare al [portale di Azure Machine Learning](https://ml.azure.com).
1. Selezionare la directory, la sottoscrizione di Azure e l'area di lavoro Machine Learning dai menu a discesa e fare clic su **Introduzione**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Schermata iniziale di Azure ML.":::

    Se non si dispone ancora di un'area di lavoro Azure Machine Learning, fare clic su **Crea una nuova area di lavoro**. Nella scheda nuovo browser eseguire le operazioni seguenti:

    1. Selezionare la sottoscrizione di Azure.
    1. Selezionare un gruppo di risorse.
    1. Immettere un nome per l'area di lavoro.
    1. Selezionare un'area.
    1. Selezionare l'edizione dell'area di lavoro.
    1. Fare clic su **Rivedi e crea**.
    1. Nella pagina successiva rivedere le selezioni e fare clic su **Crea**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Schermata di creazione dell'area di lavoro in Azure ML.":::

    Attendere alcuni minuti per la creazione dell'area di lavoro. Al termine della creazione dell'area di lavoro, vengono visualizzati segni di spunta verdi accanto alle risorse e la **distribuzione viene completata** nella parte superiore della pagina Panoramica Machine Learning Services.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Conferma della creazione dell'area di lavoro." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Al termine della creazione dell'area di lavoro, tornare alla scheda portale di machine learning e fare clic su **Introduzione**.

1. Nella Home page dell'area di lavoro di Machine Learning fare clic su **calcolo** nel riquadro a sinistra.

1. Se non è presente un'istanza di calcolo esistente, creare un nuovo calcolo CPU o GPU facendo clic su **nuovo**. Nella finestra **nuova istanza di calcolo** immettere un nome di **calcolo**, scegliere un tipo di **macchina virtuale** e selezionare le dimensioni della **macchina virtuale**. Fare clic su **Crea**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Nuova schermata di creazione dell'istanza di calcolo.":::

    Una volta fatto clic su **Crea**, la creazione dell'istanza di calcolo richiede alcuni minuti. Lo stato di **calcolo** visualizzerà un cerchio verde e, **\<your compute name> in esecuzione** , dopo il completamento della creazione del calcolo. Questa istanza di calcolo esegue il server Jupyter e verrà usata per questa esercitazione.

## <a name="visual-studio-code-development-environment-setup"></a>Configurazione dell'ambiente di sviluppo Visual Studio Code

1. Installare gli strumenti necessari.

    1. Opzione 1:

        Usare il [programma di installazione di Dev Tools Pack](./dev-tools-installer.md) per installare i pacchetti seguenti:

        - Visual Studio Code
        - Python 3,5, 3,6 o 3,7
        - Miniconda3
        - Intel OpenVino Toolkit 2020,2

        Nota: Intel OpenVino Toolkit è elencato come pacchetto facoltativo nel programma di installazione di Dev Tools Pack, ma è necessario per l'uso di Azure Percept Vision SoM som del kit di sviluppo di Azure Percept DK.

    1. Opzione 2:

        Se si preferisce non usare il programma di installazione di Dev Tools Pack, installare manualmente i pacchetti seguenti facendo clic sui collegamenti riportati di seguito e seguendo le linee guida per il download e l'installazione specificati:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 o 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020,2](https://docs.openvinotoolkit.org/)

    Nota: se è già installata la distribuzione completa di Anaconda, non è necessario installare Miniconda. In alternativa, se si preferisce non usare anaconda o Miniconda, è possibile creare un ambiente virtuale Python e installare i pacchetti necessari per l'esecuzione dello sviluppo del modello di intelligenza artificiale con pip.

1. Avviare Visual Studio Code.
1. Configurare un ambiente data science:

    - Opzione 1: connettersi a un'istanza di calcolo remoto di Machine Learning esistente o nuova che include già i pacchetti ML curati. **Questa è l'opzione che verrà usata in questa esercitazione**.

    - Opzione 2: configurare un ambiente conda in un computer locale.
        1. Aprire un prompt dei comandi Anaconda o Miniconda ed eseguire il comando seguente per creare un ambiente denominato **MyENV** con i pacchetti specificati:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Per altre informazioni sulla creazione e la gestione di ambienti Anaconda, vedere la [documentazione di Anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Creare un'area di lavoro VS Code:

    1. Creare una cartella in una posizione comoda per fungere da area di lavoro Visual Studio Code. Assegnare un nome all' **area di lavoro**.
    1. Aprire l' **area di lavoro** in Visual Studio Code facendo clic su **file**  >  **Apri cartella**  >  **selezionare cartella**.
    1. In Esplora file passare a e selezionare il [file Transferlearningusing_SSDLiteV2 Model. ipynbb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) dalla copia locale del repository GitHub Azure Percept dk. Copiare il file del notebook nella cartella area di lavoro.

## <a name="azure-integration-with-visual-studio-code"></a>Integrazione di Azure con Visual Studio Code

1. Se non è già stato fatto, iscriversi per la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

1. Installare le estensioni di Azure seguenti per VS Code:
    - [Estensione Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Estensione di Python Insider. In vs code passare a **Visualizza**  ->  **riquadro comandi**. Nel riquadro comandi immettere e selezionare **Python: passa al canale giornaliero Insider**.
    - [Estensione account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Ricaricare la finestra in VS Code quando richiesto.
    - [Estensione Toolkit Azure per hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Estensione Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Accedere al proprio account Azure in Visual Studio Code per eseguire il provisioning delle risorse ed eseguire i carichi di lavoro in Azure.
    1. Aprire il riquadro comandi in Visual Studio Code selezionando **Visualizza**  >  **riquadro comandi** dalla barra dei menu.
    1. Immettere **Azure:** accedere al riquadro comandi per avviare il processo di accesso.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Schermata di accesso di Azure." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Attivare l'estensione Python e l'account Azure facendo clic sull'icona di Azure sul lato sinistro del VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Icona di Azure in VS Code.":::

    1. Aprire il notebook Transferlearningusing_SSDLiteV2 Model_VSCode. ipynb dalla cartella **area di lavoro** .
    1. Aprire il riquadro comandi. Immettere e selezionare **Python: specificare il server Jupyter locale o remoto per le connessioni**.
    1. Verrà visualizzato un elenco di opzioni di connessione tra cui scegliere. Selezionare **istanze di ambiente di calcolo di ml di Azure**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Opzioni di istanza di calcolo di Azure ML in VS Code.":::

    1. Seguire le istruzioni guidate per scegliere una sottoscrizione, un'area di lavoro e un'istanza di calcolo remota. Selezionare l'area di lavoro e l'istanza di calcolo remota creata in precedenza in questa esercitazione. È anche possibile creare una nuova istanza di calcolo.
    1. Dopo aver selezionato un'istanza di calcolo, verrà richiesto di ricaricare la finestra di VS Code. Dopo aver ricaricato, selezionare il kernel **Python 3,6-AzureML** . È ora possibile eseguire una qualsiasi delle celle del notebook. L'esecuzione di una cella del notebook creerà un'istanza della connessione tra il notebook e l'istanza di calcolo. La barra degli strumenti del notebook verrà aggiornata per riflettere l'istanza di calcolo su cui si sta lavorando.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Selezione kernel in VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Uso del notebook

A questo punto è possibile eseguire il notebook per eseguire il training del rilevatore Bowl personalizzato in VS Code e distribuirlo nella devkit. Assicurarsi di eseguire singolarmente ogni cella del notebook perché alcune celle richiedono parametri di input prima di eseguire lo script. Le celle che richiedono parametri di input possono essere modificate direttamente nel notebook. Per eseguire una cella, fare clic sull'icona di riproduzione sul lato sinistro della cella:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Icona della cella di evidenziazione del notebook.":::

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori Azure Machine Learning notebook di esempio del servizio, visitare questo [repository](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
