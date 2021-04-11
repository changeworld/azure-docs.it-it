---
title: Come accedere a un terminale di istanza di calcolo nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Usare il terminale in un'istanza di calcolo per le operazioni git, installare i pacchetti e aggiungere i kernel.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 6f6c8e309fde92b0c2cf6fabac3489557b0b6b93
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062387"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Accedere a un terminale dell'istanza di calcolo nell'area di lavoro

Accedere al terminale di un'istanza di calcolo nell'area di lavoro per:

* Usare i file dei file git e Version. Questi file vengono archiviati nell'area di lavoro file system, non limitati a una singola istanza di calcolo.
* Installare i pacchetti nell'istanza di calcolo.
* Creare kernel aggiuntivi nell'istanza di calcolo.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Accedere a un terminale

Per accedere al terminale:

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook).
1. Selezionare l'immagine **Apri terminale** .

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Apri finestra del terminale":::

1. Quando un'istanza di calcolo è in esecuzione, viene visualizzata la finestra del terminale per l'istanza di calcolo.
1. Quando non è in esecuzione alcuna istanza di calcolo, usare la sezione **calcolo** a destra per avviare o creare un'istanza di calcolo.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Avviare o creare un'istanza di calcolo":::

Oltre ai passaggi precedenti, è anche possibile accedere al terminale da:

* RStudio: selezionare la scheda **Terminal** (Terminale) in alto a sinistra.
* Jupyter Lab:  selezionare il riquadro **Terminal** (Terminale) nell'intestazione **Other** (Altro) nella scheda Launcher (Utilità di avvio).
* Jupyter:  selezionare **New>Terminal** (Nuovo>Terminale) in alto a destra nella scheda File.
* Eseguire SSH nel computer, se è stato abilitato l'accesso SSH quando è stata creata l'istanza di calcolo.

## <a name="copy-and-paste-in-the-terminal"></a>Copiare e incollare nel terminale

> * Windows: `Ctrl-Insert` per copiare e usare `Ctrl-Shift-v` o `Shift-Insert` per incollare.
> * Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare.
> * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Usare i file di file git e Version

Accedere a tutte le operazioni git dal terminale. Tutti i file e le cartelle GIT verranno archiviati nel file system dell'area di lavoro. Questa risorsa di archiviazione consente di usare questi file da qualsiasi istanza di calcolo nell'area di lavoro.

> [!NOTE]
> Aggiungere i file e le cartelle in un punto qualsiasi della cartella **~/cloudfiles/code/Users** in modo che siano visibili in tutti gli ambienti Jupyter.

Vedere altre informazioni su come [clonare i repository GIT nel file system dell'area di lavoro](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Installare i pacchetti

 Installare i pacchetti da una finestra del terminale. Installare i pacchetti Python nell'ambiente **python 3,6-AzureML** .  Installare i pacchetti R nell'ambiente **R**.

In alternativa, è possibile installare i pacchetti direttamente in Jupyter Notebook o RStudio:

* RStudio: usare la scheda **Packages** (Pacchetti) in basso a sinistra o la scheda **Console** in alto a sinistra.  
* Python: aggiungere il codice di installazione ed eseguire in una cella Jupyter Notebook.

> [!NOTE]
> Per la gestione dei pacchetti in un notebook, usare le funzioni **% PIP** o **% conda** Magic per installare automaticamente i pacchetti nel **kernel attualmente in esecuzione**, anziché **! PIP** o **! conda** , che fa riferimento a tutti i pacchetti (inclusi i pacchetti all'esterno del kernel attualmente in esecuzione)

## <a name="add-new-kernels"></a>Aggiungere nuovi kernel

> [!WARNING]
>  Durante la personalizzazione dell'istanza di calcolo, assicurarsi di non eliminare l'ambiente **azureml_py36** conda o il kernel **Python 3,6-azureml** . Questa operazione è necessaria per la funzionalità Jupyter/JupyterLab

Per aggiungere un nuovo kernel Jupyter all'istanza di calcolo:

1. Usare la finestra del terminale per creare un nuovo ambiente.  Il codice seguente, ad esempio, crea `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Attivare l'ambiente.  Ad esempio, dopo aver creato `newenv`:

    ```shell
    conda activate newenv
    ```

1. Installare il pacchetto PIP e ipykernel nel nuovo ambiente e creare un kernel per la conda ENV

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

È possibile installare uno dei [kernel Jupyter disponibili](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).

## <a name="manage-terminal-sessions"></a>Gestisci sessioni terminal

 Selezionare **Visualizza sessioni attive** sulla barra degli strumenti del terminale per visualizzare un elenco di tutte le sessioni terminali attive. Quando non sono presenti sessioni attive, questa scheda verrà disabilitata.

Chiudere le sessioni inutilizzate per mantenere le risorse dell'istanza di calcolo.