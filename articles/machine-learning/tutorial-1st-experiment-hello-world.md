---
title: 'Esercitazione: Eseguire uno script "Hello world!" Script Python'
titleSuffix: Azure Machine Learning
description: La seconda parte della serie introduttiva su Azure Machine Learning illustra come inviare al cloud un semplice script "Hello World!" di Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 18f76480d1327d6ab41c475395a689f8024d7b25
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369026"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Esercitazione: Eseguire uno script "Hello world!" di Python t (parte 2 di 4)

Questa esercitazione illustra come usare Azure Machine Learning SDK per Python per inviare ed eseguire uno script "Hello World!" di Python.

Questa esercitazione è la *seconda di una serie in quattro parti* in cui vengono illustrati i concetti fondamentali su Azure Machine Learning e vengono completate attività di Machine Learning basate su processi in Azure. Questa esercitazione si basa sull'attività completata nella [Parte 1: Configurare il computer locale per Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare ed eseguire uno script "Hello World!" di Python in locale.
> * Creare uno script di controllo di Python per inviare "Hello world!" ad Azure Machine Learning.
> * Acquisire i concetti di Azure Machine Learning nello script di controllo.
> * Inviare ed eseguire lo script "Hello world!" di Python.
> * Visualizzare l'output del codice nel cloud.

## <a name="prerequisites"></a>Prerequisiti

- Completamento della [parte 1](tutorial-1st-experiment-sdk-setup-local.md) se non si ha già un'area di lavoro di Azure Machine Learning.

## <a name="create-and-run-a-python-script-locally"></a>Creare ed eseguire in locale uno script di Python

Nella directory `tutorial` creare una nuova sottodirectory denominata `src` in cui archiviare il codice che si vuole eseguire in un cluster di elaborazione di Azure Machine Learning. Nella sottodirectory `src` creare lo script `hello.py` di Python:

```python
# src/hello.py
print("Hello world!")
```

La struttura di directory del progetto sarà ora simile alla seguente:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="La struttura di directory Mostra hello.py nella sottodirectory src":::


### <a name="test-your-script-locally"></a><a name="test"></a>Testare lo script in locale

È possibile eseguire il codice in locale, usando l'IDE preferito o un terminale. L'esecuzione in locale offre il vantaggio del debug interattivo del codice.  Nella finestra con l'ambiente Conda *tutorial1* attivato eseguire il file Python:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Lo script è stato eseguito in locale](?success=run-local#control-script) [Si è verificato un problema](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Creare uno script di controllo

Uno *script di controllo* consente di eseguire lo script `hello.py` nel cloud. Usare lo script di controllo per controllare come e dove viene eseguito il codice di Machine Learning.  

Nella directory dell'esercitazione creare un nuovo file di Python denominato `03-run-hello.py` e copiare/incollare in tale file il codice seguente:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Informazioni sul codice

Ecco come funziona lo script di controllo:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) si connette all'area di lavoro di Azure Machine Learning in modo che sia possibile comunicare con le risorse di Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) consente di organizzare in modo semplice più esecuzioni sotto un singolo nome. Più avanti si vedrà come gli esperimenti consentano di confrontare facilmente le metriche di decine di esecuzioni.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) esegue il wrapping del codice `hello.py` e lo passa all'area di lavoro. Come indicato dal nome, è possibile usare questa classe per _configurare_ la modalità di _esecuzione_ dello _script_ in Azure Machine Learning. Specifica inoltre la destinazione di calcolo in cui verrà eseguito lo script. In questo codice la destinazione è il cluster di elaborazione creato nell'[esercitazione relativa alla configurazione](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Invia lo script. Questo invio è denominato [run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). Un oggetto run incapsula una singola esecuzione del codice. Usare un oggetto run per monitorare lo stato dello script, acquisire l'output, analizzare i risultati, visualizzare le metriche e altro ancora.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        L'oggetto `run` fornisce un handle sull'esecuzione del codice. Monitorarne lo stato da Azure Machine Learning Studio con l'URL stampato dallo script di Python.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Lo script di controllo è stato creato](?success=create-control-script#submit) [Si è verificato un problema](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Inviare ed eseguire il codice nel cloud

Eseguire lo script di controllo, che a sua volta esegue `hello.py` nel cluster di elaborazione creato nell'[esercitazione relativa alla configurazione](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Se l'esecuzione di questo codice genera un messaggio di errore che indica che non si ha accesso alla sottoscrizione, vedere [Connettersi a un'area di lavoro](how-to-manage-workspace.md?tab=python#connect-multi-tenant) per informazioni sulle opzioni di autenticazione.

> [!div class="nextstepaction"]
> [Il codice è stato inviato nel cloud](?success=submit-to-cloud#monitor) [Si è verificato un problema](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Monitorare il codice nel cloud usando Studio

L'output dello script conterrà un collegamento allo studio simile al seguente: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Seguire il collegamento.  Inizialmente, verrà visualizzato lo stato **Preparazione**.  La prima esecuzione verrà completata in 5 - 10 minuti, per i motivi seguenti:

* Un'immagine Docker viene creata nel cloud
* Il cluster di elaborazione viene ridimensionato da 0 a 1 nodo
* L'immagine Docker viene scaricata nel calcolo. 

Le esecuzioni successive sono molto più veloci (circa 15 secondi) perché l'immagine Docker viene memorizzata nella cache nell'istanza di calcolo. Per verificarlo, inviare di nuovo il codice seguente dopo il completamento della prima esecuzione.

Al termine del processo, passare alla scheda **Output e log**. in cui è possibile vedere un file `70_driver_log.txt` simile al seguente:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Nella riga 8 verrà visualizzato l'output "Hello World!".

Il file `70_driver_log.txt` contiene l'output standard di un'esecuzione. Questo file può risultare utile per il debug di esecuzioni remote nel cloud.

> [!div class="nextstepaction"]
> [Il log è visibile in Studio](?success=monitor-in-studio#next-steps) [Si è verificato un problema](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito in Azure un semplice script "Hello world!". Si è visto come connettersi all'area di lavoro di Azure Machine Learning, creare un esperimento e inviare al cloud il codice `hello.py`.

Nell'esercitazione successiva si approfondiranno queste informazioni eseguendo uno script più interessante di `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Esercitazione: training di un modello](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Se si sceglie di terminare questa serie di esercitazioni senza procedere con il passaggio successivo, assicurarsi di [pulire le risorse](tutorial-1st-experiment-bring-data.md#clean-up-resources).
