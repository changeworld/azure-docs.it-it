---
title: 'Esercitazione: Introduzione a Machine Learning - Python'
titleSuffix: Azure Machine Learning
description: Iniziare a usare l'SDK Azure Machine Learning per Python in esecuzione nell'ambiente di sviluppo personale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 69340245cdebda570b526afd4f11ac8314384b43
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210619"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Esercitazione: Introduzione ad Azure Machine Learning nel proprio ambiente di sviluppo (parte 1 di 4)

In questa *serie di quattro esercitazioni* verranno illustrati i concetti fondamentali su Azure Machine Learning e verranno completate attività di Machine Learning per Python basate su processi nella piattaforma cloud di Azure. 

Nella parte 1 di questa serie di esercitazioni si apprenderà come:

> [!div class="checklist"]
> * Installare Azure Machine Learning SDK.
> * Configurare la struttura della directory per il codice.
> * Creare un'area di lavoro di Azure Machine Learning.
> * Configurare l'ambiente di sviluppo locale.
> * Configurare un cluster di calcolo, una risorsa basata sul cloud per il training dei modelli.

Questa serie di esercitazioni è incentrata sui concetti di Azure Machine Learning necessari per inviare **processi batch**, in cui il codice viene inviato al cloud per l'esecuzione in background senza interazione con l'utente. Questa funzionalità è utile per gli script o il codice completato che si vuole eseguire ripetutamente o per le attività Machine Learning a elevato utilizzo di calcolo.

Un altro modo eccellente per iniziare a usare Azure Machine Learning è Jupyter notebook.  Vedere [esercitazione: Introduzione a Azure Machine Learning in notebook di Jupyter](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare [Azure Machine Learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) o [Miniconda](https://www.anaconda.com/download/) per gestire gli ambienti virtuali Python e installare i pacchetti.  
- Se non si ha familiarità con l'uso di conda, vedere [Introduzione a conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).
- Qualsiasi IDE o editor di testo per creare gli script Python.

## <a name="install-the-azure-machine-learning-sdk"></a>Installare Azure Machine Learning SDK

In questa esercitazione viene usato Azure Machine Learning SDK per Python. Per evitare problemi di dipendenza di Python, si creerà un ambiente isolato. Questa serie di esercitazioni USA conda per creare l'ambiente. Se si preferisce usare altre soluzioni, ad esempio `venv`, `virtualenv` o Docker, assicurarsi di usare una versione di Python maggiore o uguale a 3.5 e minore di 3.9.

Controllare se nel sistema è installato conda:
    
```bash
conda --version
```
    
Se questo comando restituisce un errore `conda not found`, [scaricare e installare Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Dopo aver installato Conda, usare una finestra del terminale o del prompt di Anaconda per creare un nuovo ambiente:

```bash
conda create -n tutorial python=3.8
```

Installare quindi Azure Machine Learning SDK nell'ambiente Conda creato:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Per completare l'installazione di Azure Machine Learning SDK, sono necessari circa 2 minuti.
>
> Se viene ricevuto un errore di timeout, provare `pip install --default-timeout=100 azureml-core` .


> [!div class="nextstepaction"]
> [L'SDK è stato installato](?success=install-sdk#dir) [Si è verificato un problema](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Creare una struttura della directory per il codice

Per questa esercitazione è consigliabile configurare la semplice struttura della directory corrente:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="struttura di directory: livello principale dell'esercitazione con sottodirectory. azureml":::


- `tutorial`: directory di primo livello del progetto.
- `.azureml`: sottodirectory nascosta per l'archiviazione dei file di configurazione di Azure Machine Learning.

Ad esempio, per creare questo in una finestra bash:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Per creare o visualizzare la struttura in una finestra grafica, abilitare prima di tutto la possibilità di visualizzare e creare cartelle e file nascosti:
>
> * In una finestra del Finder in un Mac premere **CMD+MAIUSC+.** per visualizzare/nascondere i file o le cartelle nascoste.  
> * In Esplora file di Windows 10 vedere [come visualizzare cartelle e file nascosti](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Nell'interfaccia grafica di Linux premere **CTRL+H** o selezionare il menu **Visualizza** e quindi la casella di controllo **Mostra file nascosti**.




> [!div class="nextstepaction"]
> [È stata creata una directory](?success=create-dir#workspace) [Si è verificato un problema](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Creare un'area di lavoro di Machine Learning di Azure

Un'area di lavoro è una risorsa di primo livello per Azure Machine Learning e costituisce una posizione centralizzata per:

- Gestire le risorse, ad esempio quelle di calcolo.
- Archiviare asset come notebook, ambienti, set di dati, pipeline, modelli ed endpoint.
- Collaborare con altri membri del team.

Nella directory di primo livello `tutorial` aggiungere un nuovo file di Python denominato `01-create-workspace.py` usando il codice seguente. Adattare i parametri (nome, ID sottoscrizione, gruppo di risorse e [località](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) in base alle preferenze.

È possibile eseguire il codice in una sessione interattiva o come file di Python.

>[!NOTE]
> Se si usa un ambiente di sviluppo locale (ad esempio il proprio computer), la prima volta che si esegue il codice seguente viene chiesto di eseguire l'autenticazione con l'area di lavoro usando un *codice del dispositivo*. Segui le istruzioni visualizzate.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Nella finestra con l'ambiente Conda *tutorial1* attivato eseguire questo codice dalla directory `tutorial`.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Se l'esecuzione di questo codice genera un messaggio di errore che indica che non si ha accesso alla sottoscrizione, vedere [Creare un'area di lavoro](how-to-manage-workspace.md?tab=python#create-multi-tenant) per informazioni sulle opzioni di autenticazione.


Dopo la corretta esecuzione di *01-create-workspace.py*, la struttura di cartelle sarà simile alla seguente:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="Il file config.jsviene visualizzato nella sottodirectory. azureml dopo l'esecuzione di 01-create-workspace.py":::

Il file `.azureml/config.json` contiene i metadati necessari per connettersi all'area di lavoro di Azure Machine Learning. In particolare, contiene l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro. 

> [!NOTE]
> Il contenuto di `config.json` non è costituito da segreti. È possibile condividere tranquillamente questi dettagli.
>
> L'autenticazione è tuttavia necessaria per interagire con l'area di lavoro Azure Machine Learning.

> [!div class="nextstepaction"]
> [È stata creata un'area di lavoro](?success=create-workspace#cluster) [Si è verificato un problema](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Creare un cluster di elaborazione di Azure Machine Learning

Creare uno script di Python denominato `02-create-compute.py` nella directory di primo livello `tutorial`. Popolarlo con il codice seguente per creare un cluster di elaborazione di Azure Machine Learning che verrà automaticamente dimensionato tra zero e quattro nodi:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Nella finestra con l'ambiente Conda *tutorial1* attivato eseguire il file Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Una volta creato il cluster, risulteranno 0 nodi sottoposti a provisioning. Il cluster *non* comporta l'addebito di costi finché non si invia un processo. Le prestazioni di questo cluster verranno ridotte dopo che sarà rimasto inattivo per 2.400 secondi (40 minuti).

La struttura di cartelle ora sarà simile alla seguente:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Aggiungere 02-create-compute.py alla directory dell'esercitazione":::

> [!div class="nextstepaction"]
> [È stato creato un cluster di elaborazione](?success=create-compute-cluster#next-steps) [Si è verificato un problema](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Visualizzazione nello studio

Accedere allo [studio di Azure Machine Learning](https://ml.azure.com) per visualizzare l'area di lavoro e l'istanza di calcolo create.

1. In **Sottoscrizione** selezionare la sottoscrizione usata per creare l'area di lavoro.
1. In **Area di lavoro Machine Learning** selezionare l'area di lavoro *tutorial-ws* creata.
1. Una volta caricata l'area di lavoro, selezionare **Calcolo** a sinistra.
1. Nella parte superiore selezionare la scheda **Cluster di elaborazione**.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Screenshot: Visualizzare l'istanza di calcolo nell'area di lavoro.":::

Questa visualizzazione mostra il cluster di elaborazione di cui è stato effettuato il provisioning, insieme al numero di nodi inattivi, ai nodi occupati e ai nodi senza provisioning.  Poiché il cluster non è stato ancora usato, tutti i nodi sono attualmente senza provisioning.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

- Creazione di un'area di lavoro di Azure Machine Learning
- Configurazione dell'ambiente di sviluppo locale.
- Creazione di un cluster di elaborazione di Azure Machine Learning.

Nelle altre parti dell'esercitazione si apprenderà come:

* Parte 2. Eseguire il codice nel cloud usando Azure Machine Learning SDK per Python.
* Parte 3. Gestire l'ambiente Python usato per il training del modello.
* Parte 4. Caricare i dati in Azure e utilizzarli per il training.

Proseguire con l'esercitazione successiva, che illustra come inviare uno script al cluster di elaborazione di Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire uno script "Hello World!" di Python in Azure](tutorial-1st-experiment-hello-world.md)
