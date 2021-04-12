---
title: Connettersi a un'istanza di calcolo in Visual Studio Code (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come connettersi a un'istanza di calcolo Azure Machine Learning in Visual Studio Code per eseguire carichi di lavoro di sviluppo interattivi Jupyter Notebook e remoti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028591"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Connettersi a un'istanza di calcolo Azure Machine Learning in Visual Studio Code (anteprima)

In questo articolo si apprenderà come connettersi a un'istanza di calcolo Azure Machine Learning usando Visual Studio Code.

Un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) è una workstation basata su cloud completamente gestita per gli esperti di dati e offre funzionalità di gestione e disponibilità aziendale per gli amministratori IT.

Esistono due modi per connettersi a un'istanza di calcolo da Visual Studio Code:

* Istanza di calcolo remota. Questa opzione offre un ambiente di sviluppo completo per la creazione di progetti di machine learning.
* Server Jupyter Notebook remoto. Questa opzione consente di impostare un'istanza di calcolo come server di Jupyter Notebook remoto.

## <a name="configure-a-remote-compute-instance"></a>Configurare un'istanza di calcolo remota

Per configurare un'istanza di calcolo remota per lo sviluppo, sono necessari alcuni prerequisiti.

* Azure Machine Learning Visual Studio Code estensione. Per ulteriori informazioni, vedere la [Guida all'installazione di Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Area di lavoro Azure Machine Learning. [Usare l'estensione Azure Machine Learning Visual Studio Code per creare una nuova area di lavoro](how-to-manage-resources-vscode.md#create-a-workspace) , se non è già presente.
* Azure Machine Learning istanza di calcolo. [Usare l'estensione Azure Machine Learning Visual Studio Code per creare una nuova istanza di calcolo](how-to-manage-resources-vscode.md#create-compute-instance) , se non è già presente.

Per connettersi all'istanza di calcolo remota:

# <a name="vs-code"></a>[Visual Studio Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Estensione Azure Machine Learning

1. In VS Code avviare l'estensione del Azure Machine Learning.
1. Espandere il nodo **istanze di calcolo** nell'estensione.
1. Fare clic con il pulsante destro del mouse sull'istanza di calcolo a cui si desidera connettersi e selezionare **Connetti a istanza di calcolo**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Connettersi all'istanza di calcolo Visual Studio Code estensione Azure ML" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Riquadro comandi

1. In VS Code aprire il riquadro comandi selezionando **visualizza > tavolozza comandi**.
1. Immettere nella casella di testo **Azure ml: connettersi all'istanza di calcolo**.
1. Selezionare la propria sottoscrizione.
1. Selezionare l'area di lavoro.
1. Selezionare l'istanza di calcolo o crearne una nuova.

# <a name="studio"></a>[Studio](#tab/studio)

Passare a [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Per connettersi all'istanza di calcolo remota da Visual Studio Code, assicurarsi che l'account a cui si è connessi in Azure Machine Learning Studio sia uguale a quello usato in Visual Studio Code.

### <a name="compute"></a>Calcolo

1. Selezionare la scheda **calcolo**
1. Nella colonna *URI applicazione* selezionare **vs code** per l'istanza di calcolo a cui si desidera connettersi.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Connettersi a un'istanza di calcolo VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook

1. Selezionare la scheda **notebook**
1. Nella scheda *notebook* selezionare il file che si desidera modificare.
1. Selezionare gli **editor > modifica in vs code (anteprima)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Connettersi a un'istanza di calcolo VS Code notebook di Azure ML" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Viene avviata una nuova finestra per l'istanza di calcolo remota. Quando si tenta di effettuare una connessione a un'istanza di calcolo remota, si verificano le attività seguenti:

1. Autorizzazione. Vengono eseguiti alcuni controlli per assicurarsi che l'utente che tenta di effettuare una connessione sia autorizzato a usare l'istanza di calcolo.
1. VS Code server remoto è installato nell'istanza di calcolo.
1. Viene stabilita una connessione WebSocket per l'interazione in tempo reale.

Una volta stabilita la connessione, questa viene mantenute. Un token viene emesso all'inizio della sessione, che viene aggiornato automaticamente per mantenere la connessione all'istanza di calcolo.

Dopo la connessione all'istanza di calcolo remota, usare l'editor per:

* [Creare e gestire i file nell'istanza di calcolo remota o nella condivisione file](https://code.visualstudio.com/docs/editor/codebasics).
* Usare il [terminale integrato vs code](https://code.visualstudio.com/docs/editor/integrated-terminal) per [eseguire comandi e applicazioni nell'istanza di calcolo remota](how-to-access-terminal.md).
* [Eseguire il debug di script e applicazioni](https://code.visualstudio.com/Docs/editor/debugging)
* [Usare VS Code per gestire i repository git](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurare un'istanza di calcolo come server notebook remoto

Per configurare un'istanza di calcolo come server di Jupyter Notebook remoto sono necessari alcuni prerequisiti:

* Azure Machine Learning Visual Studio Code estensione. Per ulteriori informazioni, vedere la [Guida all'installazione di Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Area di lavoro Azure Machine Learning. [Usare l'estensione Azure Machine Learning Visual Studio Code per creare una nuova area di lavoro](how-to-manage-resources-vscode.md#create-a-workspace) , se non è già presente.

Per connettersi a un'istanza di calcolo:

1. Aprire un Jupyter Notebook in Visual Studio Code.
1. Quando viene caricata l'esperienza del notebook integrato, selezionare **Jupyter server**.

    > [!div class="mx-imgBorder"]
    > ![Avvia Azure Machine Learning elenco a discesa Server Jupyter Notebook remoto](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    In alternativa, è possibile usare anche il riquadro comandi:

    1. Per aprire il riquadro comandi selezionare **Visualizza > Riquadro comandi** nella barra dei menu.
    1. Immettere nella casella di testo `Azure ML: Connect to Compute instance Jupyter server` .

1. Scegliere `Azure ML Compute Instances` dall'elenco di opzioni del server Jupyter.
1. Selezionare la sottoscrizione dall'elenco di sottoscrizioni. Se è già stata configurata l'area di lavoro predefinita Azure Machine Learning, questo passaggio viene ignorato.
1. Selezionare l'area di lavoro.
1. Selezionare l'istanza di calcolo dall'elenco. Se non è presente, selezionare **Crea nuova istanza di Azure ambiente di calcolo di ml** e seguire le istruzioni per crearne una.
1. Per rendere effettive le modifiche, è necessario ricaricare Visual Studio Code.
1. Aprire una Jupyter Notebook ed eseguire una cella.

> [!IMPORTANT]
> Per stabilire la connessione, è **necessario** eseguire una cella.

A questo punto, è possibile continuare a eseguire le celle nella Jupyter Notebook.

> [!TIP]
> È anche possibile usare i file di script Python (con estensione py) contenenti celle di codice simili a Jupyter. Per ulteriori informazioni, vedere il [Visual Studio Code documentazione interattiva di Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato Visual Studio Code remoto, è possibile usare un'istanza di calcolo come calcolo remoto da Visual Studio Code per [eseguire il debug interattivo del codice](how-to-debug-visual-studio-code.md).

In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.
