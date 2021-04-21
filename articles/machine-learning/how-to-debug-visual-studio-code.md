---
title: Debug interattivo con Visual Studio Code
titleSuffix: Azure Machine Learning
description: Eseguire il debug Azure Machine Learning codice, pipeline e distribuzioni in modo interattivo usando Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813867"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Debug interattivo con Visual Studio Code



Informazioni su come eseguire il debug interattivo di Azure Machine Learning, pipeline e distribuzioni usando Visual Studio Code (VS Code) e [debugpy.](https://github.com/microsoft/debugpy/)

## <a name="run-and-debug-experiments-locally"></a>Eseguire ed eseguire il debug degli esperimenti in locale

Usare l Azure Machine Learning per convalidare, eseguire ed eseguire il debug degli esperimenti di Machine Learning prima di inviarli al cloud.

### <a name="prerequisites"></a>Prerequisiti

* Azure Machine Learning VS Code(anteprima). Per altre informazioni, vedere [Set up Azure Machine Learning VS Code extension](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop per Mac e Windows
  * Motore Docker per Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> In Windows assicurarsi di configurare [Docker per l'uso di contenitori Linux.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

> [!TIP]
> Per Windows, sebbene non sia obbligatorio, è consigliabile usare [Docker con sottosistema Windows per Linux (WSL) 2.](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)

> [!IMPORTANT]
> Prima di eseguire l'esperimento in locale, assicurarsi che Docker sia in esecuzione.

### <a name="debug-experiment-locally"></a>Eseguire il debug dell'esperimento in locale

1. In VS Code aprire la visualizzazione Azure Machine Learning dell'estensione.
1. Espandere il nodo della sottoscrizione contenente l'area di lavoro. Se non è già presente, è possibile creare un'area di [lavoro Azure Machine Learning usando](how-to-manage-resources-vscode.md#create-a-workspace) l'estensione .
1. Espandere il nodo dell'area di lavoro.
1. Fare clic con il pulsante destro **del mouse sul nodo Experiments** (Esperimenti) e scegliere Create experiment **(Crea esperimento).** Quando viene visualizzata la richiesta, specificare un nome per l'esperimento.
1. Espandere il **nodo Esperimenti,** fare clic con il pulsante destro del mouse sull'esperimento da eseguire e scegliere **Esegui esperimento**.
1. Nell'elenco di opzioni per eseguire l'esperimento selezionare **Localmente**.
1. **Uso per la prima volta solo in Windows.** Quando viene richiesto di consentire la condivisione file, selezionare **Sì.** Quando si abilita la condivisione file, Docker può montare la directory contenente lo script nel contenitore. Consente inoltre a Docker di archiviare i log e gli output dell'esecuzione in una directory temporanea nel sistema.
1. Selezionare **Sì per** eseguire il debug dell'esperimento. In caso contrario, selezionare **No**. Se si seleziona No, l'esperimento verrà eseguito in locale senza connettersi al debugger.
1. Selezionare **Crea nuova configurazione di esecuzione** per creare la configurazione di esecuzione. La configurazione di esecuzione definisce lo script da eseguire, le dipendenze e i set di dati usati. In alternativa, se ne è già presente uno, selezionarlo nell'elenco a discesa.
    1. Scegliere l'ambiente. È possibile scegliere tra qualsiasi Azure Machine Learning [curato](resource-curated-environments.md) o crearne uno personalizzato.
    1. Specificare il nome dello script da eseguire. Il percorso è relativo alla directory aperta in VS Code.
    1. Scegliere se usare o meno un set Azure Machine Learning set di dati. È possibile creare Azure Machine Learning [set di dati usando](how-to-manage-resources-vscode.md#create-dataset) l'estensione .
    1. Debugpy è necessario per collegare il debugger al contenitore che esegue l'esperimento. Per aggiungere debugpy come dipendenza, selezionare **Aggiungi debugpy**. In caso contrario, selezionare **Ignora**. Se non si aggiunge debugpy come dipendenza, l'esperimento viene eseguito senza connettersi al debugger.
    1. Nell'editor verrà aperto un file di configurazione contenente le impostazioni di configurazione di esecuzione. Se si è soddisfatti delle impostazioni, selezionare Submit **experiment (Invia esperimento).** In alternativa, aprire il riquadro comandi ( Visualizza **> Riquadro** comandi ) dalla barra dei menu e immettere il comando nella casella `Azure ML: Submit experiment` di testo.
1. Dopo l'invio dell'esperimento, viene creata un'immagine Docker contenente lo script e le configurazioni specificate nella configurazione di esecuzione.

    Quando inizia il processo di compilazione dell'immagine Docker, il contenuto del flusso di file alla `60_control_log.txt` console di output in VS Code.

    > [!NOTE]
    > La prima creazione dell'immagine Docker può richiedere alcuni minuti.

1. Dopo aver compilato l'immagine, viene visualizzato un prompt per avviare il debugger. Impostare i punti di interruzione nello script e selezionare **Avvia debugger** quando si è pronti per avviare il debug. In questo modo il debugger VS Code al contenitore che esegue l'esperimento. In alternativa, nell'estensione Azure Machine Learning passare il puntatore del mouse sul nodo per l'esecuzione corrente e selezionare l'icona di riproduzione per avviare il debugger.

    > [!IMPORTANT]
    > Non è possibile avere più sessioni di debug per un singolo esperimento. È tuttavia possibile eseguire il debug di due o più esperimenti usando più VS Code istanze.

A questo punto, dovrebbe essere possibile eseguire un'istruzione alla volta ed eseguire il debug del codice usando VS Code.

Se in qualsiasi momento si vuole annullare l'esecuzione, fare clic con il pulsante destro del mouse sul nodo di esecuzione e **scegliere Annulla esecuzione.**

Analogamente alle esecuzioni remote dell'esperimento, è possibile espandere il nodo di esecuzione per esaminare i log e gli output.

> [!TIP]
> Le immagini Docker che usano le stesse dipendenze definite nell'ambiente vengono riutilizzate tra le esecuzioni. Tuttavia, se si esegue un esperimento usando un ambiente nuovo o diverso, viene creata una nuova immagine. Poiché queste immagini vengono salvate nell'archiviazione locale, è consigliabile rimuovere le immagini Docker meno datate o inutilizzate. Per rimuovere immagini dal sistema, usare l'interfaccia della riga di comando di [Docker](https://docs.docker.com/engine/reference/commandline/rmi/) o [l'VS Code Docker.](https://code.visualstudio.com/docs/containers/overview)

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Eseguire il debug e risolvere i problemi delle pipeline di Machine Learning

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline di Machine Learning. Usando VS Code e debugpy, è possibile connettersi al codice durante l'esecuzione nell'ambiente di training.

### <a name="prerequisites"></a>Prerequisiti

* __Un'Azure Machine Learning di__ lavoro configurata per l'uso di una __rete virtuale di Azure.__
* Una __Azure Machine Learning pipeline__ che usa script Python come parte dei passaggi della pipeline. Ad esempio, pythonScriptStep.
* Un Azure Machine Learning di calcolo, che si __trova nella rete__ virtuale e viene usato dalla pipeline per il training __di__.
* Ambiente __di sviluppo__ che si trova nella rete __virtuale__. L'ambiente di sviluppo potrebbe essere uno dei seguenti:

  * Una macchina virtuale di Azure nella rete virtuale
  * Un'istanza di calcolo della macchina virtuale Notebook nella rete virtuale
  * Un computer client con connettività di rete privata alla rete virtuale, tramite VPN o ExpressRoute.

Per altre informazioni sull'uso di una rete virtuale di Azure con Azure Machine Learning, vedere Panoramica dell'isolamento della rete virtuale [e della privacy.](how-to-network-security-overview.md)

> [!TIP]
> Anche se è possibile usare Azure Machine Learning che non si trova dietro una rete virtuale, è consigliabile usare una rete virtuale.

### <a name="how-it-works"></a>Funzionamento

I passaggi della pipeline di Machine Learning eseguono script Python. Questi script vengono modificati per eseguire le azioni seguenti:

1. Registrare l'indirizzo IP dell'host in cui sono in esecuzione. Usare l'indirizzo IP per connettere il debugger allo script.

2. Avviare il componente debugpy e attendere la connessione di un debugger.

3. Dall'ambiente di sviluppo si monitorano i log creati dal processo di training per trovare l'indirizzo IP in cui è in esecuzione lo script.

4. Per indicare VS Code'indirizzo IP a cui connettere il debugger usando un `launch.json` file.

5. Collegare il debugger ed eseguire lo script in modo interattivo.

### <a name="configure-python-scripts"></a>Configurare gli script Python

Per abilitare il debug, apportare le modifiche seguenti agli script Python usati dai passaggi nella pipeline di Machine Learning:

1. Aggiungere le istruzioni import seguenti:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Aggiungere gli argomenti seguenti. Questi argomenti consentono di abilitare il debugger in base alle esigenze e di impostare il timeout per il collegamento del debugger:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Aggiungere le istruzioni seguenti. Queste istruzioni caricano il contesto di esecuzione corrente in modo che sia possibile registrare l'indirizzo IP del nodo in cui è in esecuzione il codice:

    ```python
    global run
    run = Run.get_context()
    ```

1. Aggiungere `if` un'istruzione che avvia debugpy e attende il collegamento di un debugger. Se nessun debugger si collega prima del timeout, lo script continua come di consueto. Assicurarsi di sostituire i `HOST` valori e con la funzione con i `PORT` `listen` propri.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

L'esempio python seguente mostra un `train.py` semplice file che abilita il debug:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurare la pipeline di Machine Learning

Per fornire i pacchetti Python necessari per avviare debugpy e ottenere il contesto di esecuzione, creare un ambiente e impostare `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Modificare la versione dell'SDK in modo che corrisponda a quella in uso. Il frammento di codice seguente illustra come creare un ambiente:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Nella sezione [Configura script Python](#configure-python-scripts) sono stati aggiunti nuovi argomenti agli script usati dai passaggi della pipeline di Machine Learning. Il frammento di codice seguente illustra come usare questi argomenti per abilitare il debug per il componente e impostare un timeout. Illustra anche come usare l'ambiente creato in precedenza impostando `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando la pipeline viene eseguita, ogni passaggio crea un'esecuzione figlio. Se il debug è abilitato, lo script modificato registra informazioni simili al testo seguente in `70_driver_log.txt` per l'esecuzione figlio:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Salvare il `ip_address` valore. Verranno usate nella sezione successiva.

> [!TIP]
> È anche possibile trovare l'indirizzo IP dai log di esecuzione per l'esecuzione figlio per questo passaggio della pipeline. Per altre informazioni sulla visualizzazione di queste informazioni, vedere Monitorare le esecuzioni e le metriche degli [esperimenti di Azure ML.](how-to-log-view-metrics.md)

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare debugpy nell'VS Code di sviluppo, usare il comando seguente:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Per altre informazioni sull'uso di debugpy con VS Code, vedere [Debug remoto](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Per configurare VS Code per comunicare con il Azure Machine Learning di calcolo che esegue il debugger, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __Debug__, quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json__.

    1. Nellaunch.js __file__ trovare la riga che contiene `"configurations": [` e inserire il testo seguente dopo di esso. Modificare la `"host": "<IP-ADDRESS>"` voce con l'indirizzo IP restituito nei log della sezione precedente. Modificare la voce in una directory locale contenente una copia dello script di cui `"localRoot": "${workspaceFolder}/code/step"` viene eseguito il debug:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se nella sezione Configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        > [!TIP]
        > La procedura consigliata, in particolare per le pipeline, consiste nel mantenere le risorse per gli script in directory separate in modo che il codice sia pertinente solo per ognuno dei passaggi. In questo esempio il `localRoot` valore di esempio fa riferimento a `/code/step1` .
        >
        > Se si esegue il debug di più script, in directory diverse creare una sezione di configurazione separata per ogni script.

    1. Salvare il file __launch.json__.

### <a name="connect-the-debugger"></a>Connettere il debugger

1. Aprire VS Code e aprire una copia locale dello script.
2. Impostare i punti di interruzione in cui arrestare lo script dopo aver eseguito il collegamento.
3. Mentre il processo figlio esegue lo script e viene visualizzato nei log, usare `Timeout for debug connection` il tasto F5 o selezionare __Debug__. Quando richiesto, selezionare la configurazione Azure Machine Learning __calcolo: debug__ remoto. È anche possibile selezionare l'icona di debug dalla barra laterale, la __voce Azure Machine Learning: remote debug__ dal menu a discesa Debug e quindi usare la freccia verde per collegare il debugger.

    A questo punto, VS Code si connette a debugpy nel nodo di calcolo e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile scorrere il codice durante l'esecuzione, visualizzare le variabili e così via.

    > [!NOTE]
    > Se nel log viene visualizzata una voce che indica , il timeout è scaduto e `Debugger attached = False` lo script è stato continuato senza il debugger. Inviare di nuovo la pipeline e connettere il debugger dopo `Timeout for debug connection` il messaggio e prima della scadenza del timeout.

## <a name="debug-and-troubleshoot-deployments"></a>Eseguire il debug e risolvere i problemi delle distribuzioni

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di immissione ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Usando VS Code e debugpy, è possibile connettersi al codice in esecuzione all'interno del contenitore Docker.

> [!IMPORTANT]
> Questo metodo di debug non funziona quando si usano `Model.deploy()` e `LocalWebservice.deploy_configuration` per distribuire un modello localmente. Al contrario, è necessario creare un'immagine usando il metodo [Model.package()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-).

Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Per altre informazioni sull'uso di Docker, vedere la [Documentazione di Docker](https://docs.docker.com/). Si noti che quando si lavora con le istanze di calcolo, Docker è già installato.

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare debugpy nell'ambiente VS Code di sviluppo, usare il comando seguente:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Per altre informazioni sull'uso di debugpy con VS Code, vedere [Debug remoto.](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

1. Per configurare VS Code per la comunicazione con l'immagine Docker, creare una nuova configurazione di debug:

    1. In VS Code selezionare il menu __Debug__ in __Esegui__ estensione e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json__.

    1. Nellaunch.js __file__ trovare l'elemento __"configurations"__ (la riga che contiene ) e inserire il `"configurations": [` testo seguente dopo di esso. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Dopo __l'inserimento,launch.jsnel__ file dovrebbe essere simile al seguente:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se sono già presenti altre voci nella sezione configurations, aggiungere una virgola ( __,__ ) dopo il codice inserito.

        Questa sezione si collega al contenitore Docker usando la __porta 5678.__

    1. Salvare il file __launch.json__.

### <a name="create-an-image-that-includes-debugpy"></a>Creare un'immagine che includa debugpy

1. Modificare l'ambiente Conda per la distribuzione in modo che includa debugpy. L'esempio seguente illustra come aggiungerlo usando il parametro `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Per avviare debugpy e attendere una connessione all'avvio del servizio, aggiungere quanto segue all'inizio del `score.py` file:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Creare un'immagine in base alla definizione dell'ambiente ed eseguire il pull dell'immagine nel Registro di sistema locale. 

    > [!NOTE]
    > In questo esempio si presuppone che `ws` punti all'area di lavoro di Azure Machine Learning e che `model` sia il modello distribuito. Il file `myenv.yml` contiene le dipendenze Conda create nel passaggio 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Dopo che l'immagine è stata creata e scaricata (questo processo può richiedere più di 10 minuti, quindi attendere con attenzione), il percorso dell'immagine (che include repository, nome e tag, che in questo caso è anche il digest), viene infine visualizzato in un messaggio simile al seguente:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Per semplificare l'uso dell'immagine in locale, è possibile usare il comando seguente per aggiungere un tag per questa immagine. Sostituire `myimagepath` nel comando seguente con il valore della posizione del passaggio precedente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per i passaggi rimanenti, è possibile fare riferimento all'immagine locale come `debug:1` anziché al valore del percorso dell'immagine completa.

### <a name="debug-the-service"></a>Eseguire il debug del servizio

> [!TIP]
> Se si imposta un timeout per la connessione debugpy nel file, è necessario VS Code alla sessione di debug prima della `score.py` scadenza del timeout. Avviare VS Code, aprire la copia locale di `score.py`, impostare un punto di interruzione e fare in modo che sia pronto prima di usare i passaggi descritti in questa sezione.
>
> Per altre informazioni sul debug e sull'impostazione di punti di interruzione, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker usando l'immagine, usare il comando seguente:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    In questo modo `score.py` l'utente viene collegato in locale a quello nel contenitore. Pertanto, tutte le modifiche apportate nell'editor vengono riflesse automaticamente nel contenitore

2. Per un'esperienza migliore, è possibile accedere al contenitore con una nuova interfaccia di Visual Studio Code. Selezionare `Docker` l'estensione dalla barra VS Code laterale e trovare il contenitore locale creato, in questa documentazione è `debug:1` . Fare clic con il pulsante destro del mouse su questo contenitore e scegliere , quindi verrà aperta automaticamente una nuova interfaccia VS Code e questa interfaccia mostra l'interno `"Attach Visual Studio Code"` del contenitore creato.

    ![Interfaccia VS Code contenitore](./media/how-to-troubleshoot-deployment/container-interface.png)

3. All'interno del contenitore eseguire il comando seguente nella shell

    ```bash
    runsvdir /var/runit
    ```
    È quindi possibile visualizzare l'output seguente nella shell all'interno del contenitore:

    ![Output della console di esecuzione del contenitore](./media/how-to-troubleshoot-deployment/container-run.png)

4. Per collegare VS Code debugpy all'interno del contenitore, aprire VS Code usare il tasto F5 o selezionare __Debug__. Quando richiesto, selezionare la configurazione Azure Machine Learning __distribuzione: Docker Debug.__ È anche possibile  selezionare l'icona Esegui extention dalla barra laterale, la voce __Azure Machine Learning Deployment: Docker Debug__ dal menu a discesa Debug e quindi usare la freccia verde per collegare il debugger.

    ![Icona Debug, pulsante Avvia debug e selettore della configurazione](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Dopo aver fatto clic sulla freccia verde e aver collegato il debugger, nel contenitore VS Code'interfaccia è possibile visualizzare alcune nuove informazioni:
    
    ![Informazioni associate al debugger del contenitore](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Inoltre, nell'interfaccia VS Code principale, è possibile vedere quanto segue:

    ![Punto VS Code punto di interruzione score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

A questo punto, l'oggetto locale collegato al contenitore è già stato arrestato in corrispondenza dei punti `score.py` di interruzione impostati. A questo punto, VS Code si connette a debugpy all'interno del contenitore Docker e arresta il contenitore Docker in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile scorrere il codice durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso di VS Code per eseguire il debug di Python, vedere [Eseguire il debug del codice Python](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, usare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato VS Code remoto, è possibile usare un'istanza di calcolo come calcolo remoto da VS Code per eseguire il debug interattivo del codice. 

Altre informazioni sulla risoluzione dei problemi:

* [Distribuzione del modello locale](how-to-troubleshoot-deployment-local.md)
* [Distribuzione di modelli remoti](how-to-troubleshoot-deployment.md)
* [Pipeline di apprendimento automatico](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

