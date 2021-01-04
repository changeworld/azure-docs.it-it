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
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733334"
---
# <a name="troubleshoot-environment-image-builds"></a>Risolvere i problemi relativi alle build dell'immagine ambiente
Informazioni su come risolvere i problemi relativi alle compilazioni di immagini dell'ambiente Docker e alle installazioni di pacchetti.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug localmente, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

## <a name="docker-image-build-failures"></a>Errori di compilazione dell'immagine Docker
 
Per la maggior parte degli errori di compilazione delle immagini, la causa principale è reperibile nel log di compilazione dell'immagine.
È possibile trovare il log di compilazione dell'immagine dal portale di Azure Machine Learning (20 \_ \_log.txt di compilazione dell'immagine \_ ) o dalle attività di ACR che eseguono i log
 
Nella maggior parte dei casi, è più facile riprodurre gli errori localmente. Verificare il tipo di errore e provare a eseguire una delle operazioni seguenti `setuptools` :

- Installare la dipendenza conda localmente `conda install suspicious-dependency==X.Y.Z`
- Installare la dipendenza PIP localmente `pip install suspicious-dependency==X.Y.Z`
- Provare a materializzare l'intero ambiente `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Assicurarsi che la piattaforma e l'interprete nel calcolo locale corrispondano a quelli in remoto. 

### <a name="timeout"></a>Timeout 
 
Possono verificarsi problemi di timeout per diversi problemi di rete:
- Larghezza di banda Internet bassa
- Problemi del server
- Dipendenza di grandi dimensioni che non può essere scaricata con le impostazioni di timeout conda o PIP specificate
 
Un messaggio simile al seguente indicherà il problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Soluzioni possibili:
 
- Provare un'origine diversa per la dipendenza, se disponibile, ad esempio Mirrors, archiviazione BLOB o altri feed Python.
- Aggiornare conda o PIP. Se viene usato un file Docker personalizzato, aggiornare le impostazioni di timeout.
- Alcune versioni PIP presentano problemi noti. Prendere in considerazione l'aggiunta di una versione specifica di PIP alle dipendenze dell'ambiente.

### <a name="package-not-found"></a>Pacchetto non trovato

Questo è il caso più comune per gli errori di compilazione dell'immagine.

- Non è stato possibile trovare il pacchetto conda
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Il pacchetto o la versione PIP specificata non è stata trovata
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Dipendenza PIP nidificata non valida
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Verificare che il pacchetto esista nelle origini specificate. Usare la [ricerca PIP](https://pip.pypa.io/en/stable/reference/pip_search/) per verificare le dipendenze PIP.

`pip search azureml-core`

Per le dipendenze conda, utilizzare la [ricerca conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Per altre opzioni:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Note del programma di installazione

Assicurarsi che la distribuzione necessaria esista per la versione specificata della piattaforma e dell'interprete Python.

Per le dipendenze PIP, passare a `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` per verificare se è disponibile la versione richiesta. Ad esempio, usare https://pypi.org/project/azureml-core/1.11.0/#files

Per le dipendenze conda, controllare il pacchetto nel repository del canale.
Per i canali gestiti da Anaconda, Inc., vedere [qui](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aggiornamento pacchetto pip

Durante l'installazione o l'aggiornamento di un pacchetto pip, il resolver potrebbe dover aggiornare un pacchetto già installato per soddisfare i nuovi requisiti.
La disinstallazione può non riuscire per vari motivi correlati alla versione PIP o al modo in cui è stata installata la dipendenza.
Lo scenario più comune è che una dipendenza installata da conda non può essere disinstallata da PIP.
Per questo scenario, provare a disinstallare la dipendenza utilizzando `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemi del programma di installazione

Alcune versioni del programma di installazione presentano problemi nei resolver dei pacchetti che possono causare un errore di compilazione.

Se viene usata un'immagine di base personalizzata o dockerfile, è consigliabile usare conda versione 4.5.4 o successiva.

Il pacchetto pip è necessario per installare le dipendenze PIP e se non è specificata una versione nell'ambiente verrà usata la versione più recente.
Si consiglia di utilizzare una versione nota di PIP per evitare problemi temporanei o modifiche di rilievo che possono essere causati dalla versione più recente dello strumento.

Si consiglia di aggiungere la versione PIP nell'ambiente se viene visualizzato uno dei messaggi seguenti:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Inoltre, l'installazione PIP può rimanere bloccata in un ciclo infinito se sono presenti conflitti irrisolvibile nelle dipendenze. Se funziona localmente, effettuare il downgrade della versione PIP a < 20,3. In un ambiente conda creato da un file YAML, questo problema si verifica solo se Conda-Forge è il canale con la priorità più alta. Per attenuare il problema, specificare in modo esplicito PIP < 20,3 (! = 20,3 o = 20.2.4 pin ad altre versioni) come dipendenza conda nel file di specifica conda.

## <a name="service-side-failures"></a>Errori lato servizio

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Non è stato possibile eseguire il pull dell'immagine da non è stato possibile risolvere per Container Registry.
Possibili problemi:
- Il nome del percorso del registro contenitori potrebbe non essere risolto correttamente. Verificare che i nomi delle immagini usino barre doppie e che la direzione delle barre negli host Linux e Windows sia corretta.
- Se l'ACR dietro una VNET usa un endpoint privato in [un'area non supportata](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), configurare il record di accesso dietro una VNET usando l'endpoint del servizio (accesso pubblico) dal portale e riprovare.
- Dopo aver inserito l'ACR dietro una VNet, assicurarsi che il [modello ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) venga eseguito. Ciò consente all'area di lavoro di comunicare con l'istanza di ACR.

### <a name="401-error-from-workspace-acr"></a>401 errore dall'area di lavoro ACR
Risincronizzare le chiavi di archiviazione utilizzando [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>L'ambiente continua a generare "in attesa del completamento di altre operazioni conda..." Errore
Quando la compilazione di un'immagine è in corso, conda è bloccata dal client SDK. Se il processo si è arrestato in modo anomalo o è stato annullato erroneamente dall'utente, conda rimane nello stato bloccato. Per risolvere il problema, eliminare manualmente il file di blocco. 

### <a name="custom-docker-image-not-in-registry"></a>Immagine Docker personalizzata non nel registro di sistema
Controllare se viene usato il [tag corretto](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) e questo `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` Disabilita conda e utilizza i pacchetti installati dell'utente.

### <a name="common-vnet-issues"></a>Problemi comuni di VNet

1. Verificare che l'account di archiviazione, il cluster di calcolo e il Container Registry di Azure si trovino tutti nella stessa subnet della rete virtuale.
2. Quando ACR è dietro una VNet, non può essere usato direttamente per compilare immagini. Il cluster di calcolo deve essere usato per compilare le immagini.
3. Potrebbe essere necessario disporre di spazio di archiviazione dietro una VNet quando:
    - Uso dell'inferenza o della rotellina privata
    - Visualizzazione degli errori del servizio 403 (non autorizzato)
    - Non è possibile ottenere i dettagli dell'immagine da ACR/di

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>La compilazione dell'immagine non riesce quando si tenta di accedere all'archiviazione protetta da rete
- Le attività ACR non funzionano dietro la VNet. Se l'utente ha il proprio ACR dietro il VNet, è necessario usare il cluster di calcolo per creare un'immagine.
- L'archiviazione deve essere dietro VNet per poter eseguire il pull delle dipendenze. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Non è possibile eseguire esperimenti quando è abilitata la sicurezza di rete per l'archiviazione
Quando si usano le immagini Docker predefinite e si abilitano le dipendenze gestite dall'utente, è necessario usare i [tag del servizio](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry e AzureFrontDoor. FirstParty per consentire la gestione delle dipendenze e le relative dipendenze.

 Per ulteriori informazioni, vedere [Abilitazione di VNET](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) .

### <a name="creating-an-icm"></a>Creazione di un ICM

Quando si crea o si assegna un ICM al Metastore, includere il ticket di supporto CSS in modo da poter comprendere meglio il problema.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello di apprendimento automatico per categorizzare i fiori](how-to-train-scikit-learn.md)
- [Eseguire il training di un modello di apprendimento automatico usando un'immagine Docker personalizzata](how-to-train-with-custom-image.md)