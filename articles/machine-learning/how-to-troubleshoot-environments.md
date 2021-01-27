---
title: Risolvere i problemi relativi alle immagini dell'ambiente
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere i problemi relativi alle compilazioni di immagini dell'ambiente e alle installazioni di pacchetti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 7ddd5dec87a122a0b36fee17b5434c8a49dcf434
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881636"
---
# <a name="troubleshoot-environment-image-builds"></a>Risolvere i problemi relativi alle build dell'immagine ambiente

Informazioni su come risolvere i problemi relativi alle compilazioni di immagini dell'ambiente Docker e alle installazioni di pacchetti.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug localmente, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

## <a name="docker-image-build-failures"></a>Errori di compilazione dell'immagine Docker
 
Per la maggior parte degli errori di compilazione delle immagini, si troverà la causa radice nel log di compilazione dell'immagine.
Trovare il log di compilazione dell'immagine dal portale di Azure Machine Learning (20 \_ \_log.txt di compilazione dell'immagine \_ ) o dai log di esecuzione dell'attività container Registry di Azure.
 
È in genere più facile riprodurre gli errori localmente. Verificare il tipo di errore e provare a eseguire una delle operazioni seguenti `setuptools` :

- Installare una dipendenza conda localmente: `conda install suspicious-dependency==X.Y.Z` .
- Installare una dipendenza PIP localmente: `pip install suspicious-dependency==X.Y.Z` .
- Provare a materializzare l'intero ambiente: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Assicurarsi che la piattaforma e l'interprete nel cluster di calcolo locale corrispondano a quelli nel cluster di calcolo remoto. 

### <a name="timeout"></a>Timeout 
 
I problemi di rete seguenti possono causare errori di timeout:

- Larghezza di banda Internet bassa
- Problemi del server
- Dipendenze di grandi dimensioni che non possono essere scaricate con le impostazioni di timeout conda o PIP specificate
 
Messaggi simili agli esempi seguenti indicheranno il problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Se viene ricevuto un messaggio di errore, provare una delle soluzioni possibili seguenti:
 
- Per la dipendenza, provare a usare un'origine diversa, ad esempio Mirrors, archiviazione BLOB di Azure o altri feed Python.
- Aggiornare conda o PIP. Se si usa un file Docker personalizzato, aggiornare le impostazioni di timeout.
- Alcune versioni PIP presentano problemi noti. Prendere in considerazione l'aggiunta di una versione specifica di PIP alle dipendenze dell'ambiente.

### <a name="package-not-found"></a>Pacchetto non trovato

Gli errori seguenti sono più comuni per gli errori di compilazione delle immagini:

- Non è stato possibile trovare il pacchetto conda:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Non è stato possibile trovare il pacchetto o la versione PIP specificata:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Dipendenza PIP nidificata non valida:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Verificare che il pacchetto esista nelle origini specificate. Usare la [ricerca PIP](https://pip.pypa.io/en/stable/reference/pip_search/) per verificare le dipendenze PIP:

- `pip search azureml-core`

Per le dipendenze conda, usare la [ricerca conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Per altre opzioni, provare:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Note del programma di installazione

Assicurarsi che la distribuzione necessaria esista per la versione specificata della piattaforma e dell'interprete Python.

Per le dipendenze PIP, passare a `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` per verificare se la versione richiesta è disponibile. Passare a https://pypi.org/project/azureml-core/1.11.0/#files per vedere un esempio.

Per le dipendenze conda, controllare il pacchetto nel repository del canale.
Per i canali gestiti da Anaconda, Inc., vedere la [pagina dei pacchetti Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aggiornamento pacchetto pip

Durante un'installazione o un aggiornamento di un pacchetto pip, il resolver potrebbe dover aggiornare un pacchetto già installato per soddisfare i nuovi requisiti.
La disinstallazione può non riuscire per vari motivi correlati alla versione PIP o al modo in cui è stata installata la dipendenza.
Lo scenario più comune è che una dipendenza installata da conda non può essere disinstallata da PIP.
Per questo scenario, provare a disinstallare la dipendenza tramite `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemi del programma di installazione

Alcune versioni del programma di installazione presentano problemi nei resolver dei pacchetti che possono causare un errore di compilazione.

Se si usa un'immagine di base personalizzata o Dockerfile, è consigliabile usare conda Version 4.5.4 o versioni successive.

Per installare le dipendenze PIP è necessario un pacchetto pip. Se nell'ambiente non è specificata una versione, verrà usata la versione più recente.
Si consiglia di utilizzare una versione nota di PIP per evitare problemi temporanei o modifiche di rilievo che potrebbero causare la versione più recente dello strumento.

Si consiglia di aggiungere la versione PIP nell'ambiente se viene visualizzato il messaggio seguente:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Errore del sottoprocesso PIP:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

L'installazione PIP può rimanere bloccata in un ciclo infinito se sono presenti conflitti irrisolvibile nelle dipendenze. Se si lavora localmente, effettuare il downgrade della versione PIP a < 20,3. In un ambiente conda creato da un file YAML si noterà questo problema solo se Conda-Forge è il canale con la priorità più alta. Per attenuare il problema, specificare in modo esplicito PIP < 20,3 (! = 20,3 o = 20.2.4 pin ad altre versioni) come dipendenza conda nel file di specifica conda.

## <a name="service-side-failures"></a>Errori sul lato servizio

Per risolvere i possibili errori sul lato servizio, vedere gli scenari seguenti.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Non è possibile eseguire il pull di un'immagine da un registro contenitori oppure non è stato possibile risolvere l'indirizzo per un registro contenitori

Possibili problemi:
- Il nome del percorso del registro contenitori potrebbe non essere risolto correttamente. Verificare che i nomi delle immagini usino barre doppie e che la direzione delle barre in Linux rispetto agli host Windows sia corretta.
- Se un registro contenitori dietro una rete virtuale usa un endpoint privato in [un'area non supportata](/azure/private-link/private-link-overview#availability), configurare il registro contenitori usando l'endpoint del servizio (accesso pubblico) dal portale e riprovare.
- Dopo aver inserito il registro contenitori dietro una rete virtuale, eseguire il [modello di Azure Resource Manager](/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) in modo che l'area di lavoro possa comunicare con l'istanza del registro contenitori.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Si riceve un errore 401 da un registro contenitori dell'area di lavoro

Risincronizzare le chiavi di archiviazione utilizzando [WS.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>L'ambiente continua a generare un "in attesa del completamento di altre operazioni conda..." errore

Quando la compilazione di un'immagine è in corso, conda è bloccata dal client SDK. Se il processo è stato arrestato in modo anomalo o è stato annullato erroneamente dall'utente, conda rimane nello stato bloccato. Per risolvere questo problema, eliminare manualmente il file di blocco. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>L'immagine Docker personalizzata non è presente nel registro di sistema

Controllare se viene usato il [tag corretto](/azure/machine-learning/how-to-use-environments#create-an-environment) e questo `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` Disabilita conda e utilizza i pacchetti installati dell'utente.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Si ottiene uno dei seguenti problemi comuni della rete virtuale

- Verificare che l'account di archiviazione, il cluster di calcolo e il registro contenitori si trovino nella stessa subnet della rete virtuale.
- Quando il registro contenitori si trova dietro una rete virtuale, non può essere usato direttamente per la compilazione di immagini. Per compilare le immagini, è necessario usare il cluster di elaborazione.
- Potrebbe essere necessario disporre di spazio di archiviazione dietro una rete virtuale se:
    - Usare l'inferenza o la rotellina privata.
    - Vedere gli errori del servizio 403 (non autorizzato).
    - Non è possibile ottenere i dettagli dell'immagine dal Container Registry di Azure.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>La compilazione dell'immagine ha esito negativo quando si prova ad accedere all'archiviazione protetta da rete

- Le attività di Container Registry di Azure non funzionano dietro una rete virtuale. Se l'utente ha il registro contenitori dietro una rete virtuale, è necessario usare il cluster di calcolo per creare un'immagine.
- L'archiviazione deve trovarsi dietro una rete virtuale per poter effettuare il pull delle dipendenze.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Non è possibile eseguire esperimenti quando per l'archiviazione è abilitata la sicurezza di rete

Se si usano immagini Docker predefinite e si abilitano le dipendenze gestite dall'utente, usare i tag del [servizio](/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry e AzureFrontDoor. FirstParty per consentire l'container Registry Azure e le relative dipendenze.

 Per ulteriori informazioni, vedere [Abilitazione di reti virtuali](/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry).

### <a name="you-need-to-create-an-icm"></a>È necessario creare un ICM

Quando si crea o si assegna un ICM al Metastore, includere il ticket di supporto CSS in modo da poter comprendere meglio il problema.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello di apprendimento automatico per categorizzare i fiori](how-to-train-scikit-learn.md)
- [Eseguire il training di un modello di apprendimento automatico usando un'immagine Docker personalizzata](how-to-train-with-custom-image.md)