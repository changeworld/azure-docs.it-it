---
title: Risolvere i problemi di esperimenti Machine Learning automatici
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere e risolvere i problemi negli esperimenti di Machine Learning automatizzati.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: b66c768011c05e1105f1351ebe4ed7c3c9700b70
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519164"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Risolvere i problemi relativi agli esperimenti di Machine Learning automatici in Python

Questa guida illustra come identificare e risolvere i problemi noti negli esperimenti di Machine Learning automatici con [SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Dipendenze della versione

le **`AutoML` dipendenze con versioni più recenti del pacchetto interrompono la compatibilità**. Dopo la versione dell'SDK 1.13.0, i modelli non vengono caricati in SDK precedenti a causa dell'incompatibilità tra le versioni precedenti aggiunte nei `AutoML` pacchetti precedenti e le versioni più recenti bloccate oggi.

Prevedere errori come:

* Errori del modulo non trovati, ad esempio,

  `No module named 'sklearn.decomposition._truncated_svd`

* Errori di importazione come,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Errori di attributo, ad esempio,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Le risoluzioni dipendono dalla `AutoML` versione di training dell'SDK:

* Se la `AutoML` versione di training dell'SDK è maggiore di 1.13.0, sono necessari `pandas == 0.25.1` e `scikit-learn==0.22.1` .

    * In caso di mancata corrispondenza della versione, aggiornare Scikit-learn e/o Pandas alla versione corretta con il codice seguente:

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Se la `AutoML` versione di training dell'SDK è inferiore o uguale a 1.12.0, sono necessari `pandas == 0.23.4` e `sckit-learn==0.20.3` .
  * In caso di mancata corrispondenza della versione, effettuare il downgrade di Scikit-learn e/o Pandas alla versione corretta con il codice seguente:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Configurazione

`AutoML` le modifiche ai pacchetti dalla versione 1.0.76 richiedono la disinstallazione della versione precedente prima dell'aggiornamento alla nuova versione.

* **`ImportError: cannot import name AutoMLConfig`**

    Se si verifica questo errore dopo l'aggiornamento da una versione di SDK precedente a v 1.0.76 a v 1.0.76 o versioni successive, risolvere l'errore eseguendo: `pip uninstall azureml-train automl` e quindi `pip install azureml-train-automl` . Questa operazione viene eseguita automaticamente dallo script automl_setup. cmd.

* **automl_setup ha esito negativo**

  * In Windows eseguire automl_setup da un prompt Anaconda. [Installare Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Verificare che sia installato conda 64 bit versione 4.4.10 o successiva. È possibile controllare il bit con il `conda info` comando. `platform`Deve essere `win-64` per Windows o `osx-64` per Mac. Per controllare la versione, usare il comando `conda -V` . Se è installata una versione precedente, è possibile aggiornarla usando il comando: `conda update conda` . Per controllare 32 bit eseguendo 

  * Verificare che conda sia installato. 

  * Linux `gcc: error trying to exec 'cc1plus'`

    1. Se `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` viene rilevato l'errore, installare gli strumenti di compilazione GCC per la distribuzione Linux. Ad esempio, in Ubuntu usare il comando `sudo apt-get install build-essential` .

    1. Passare un nuovo nome come primo parametro per automl_setup per creare un nuovo ambiente conda. Visualizzare gli ambienti conda esistenti usando `conda env list` e rimuoverli con `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh ha esito negativo**: se automl_setup_linus. sh non riesce in Ubuntu Linux con l'errore: `unable to execute 'gcc': No such file or directory`

  1. Verificare che siano abilitate le porte in uscita 53 e 80. In una macchina virtuale di Azure, è possibile eseguire questa operazione dalla portale di Azure selezionando la macchina virtuale e facendo clic su **rete**.
  1. Eseguire il comando `sudo apt-get update`
  1. Eseguire il comando `sudo apt-get install build-essential --fix-missing`
  1. Esegui di `automl_setup_linux.sh` nuovo

* **Configuration. ipynb ha esito negativo**:

  * Per conda locale, verificare innanzitutto che `automl_setup` sia stato eseguito correttamente.
  * Verificare che il subscription_id sia corretto. Trovare il subscription_id nel portale di Azure selezionando tutti i servizi e quindi sottoscrizioni. I caratteri "<" e ">" non devono essere inclusi nel valore di subscription_id. Ad esempio, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` ha il formato valido.
  * Assicurarsi che collaboratore o proprietario acceda alla sottoscrizione.
  * Verificare che l'area sia una delle aree supportate: `eastus2` , `eastus` , `westcentralus` , `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Assicurarsi di accedere all'area usando il portale di Azure.
  
* **Workspace.from_config ha esito negativo**:

  Se la chiamata ha `ws = Workspace.from_config()` esito negativo:

  1. Verificare che il notebook Configuration. ipynb sia stato eseguito correttamente.
  1. Se il notebook è in esecuzione da una cartella che non si trova sotto la cartella in cui è `configuration.ipynb` stato eseguito, copiare la cartella aml_config e il file config.jsin cui è contenuto nella nuova cartella. Workspace.from_config legge il config.jsper la cartella del notebook o la relativa cartella padre.
  1. Se viene usata una nuova sottoscrizione, un gruppo di risorse, un'area di lavoro o un'area, assicurati di eseguire di `configuration.ipynb` nuovo il notebook. La modifica di config.jsdirettamente funzionerà solo se l'area di lavoro esiste già nel gruppo di risorse specificato nella sottoscrizione specificata.
  1. Se si vuole modificare l'area, modificare l'area di lavoro, il gruppo di risorse o la sottoscrizione. `Workspace.create` non creerà o aggiornerà un'area di lavoro, se esiste già, anche se l'area specificata è diversa.

## <a name="tensorflow"></a>TensorFlow

A partire dalla versione 1.5.0 dell'SDK, Machine Learning automatico non installa i modelli TensorFlow per impostazione predefinita. Per installare TensorFlow e usarlo con gli esperimenti di Machine Learning automatici, installare `tensorflow==1.12.0` tramite `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Errori numpy

* errore **`import numpy` in Windows**: alcuni ambienti Windows visualizzano un errore durante il caricamento di numpy con la versione più recente di Python 3.6.8 tramite. Se viene visualizzato questo problema, provare con Python versione 3.6.7.

* **`import numpy` esito negativo**: controllare la versione di TensorFlow nell'ambiente automatico ML conda. Le versioni supportate sono < 1,13. Disinstallare TensorFlow dall'ambiente se la versione è >= 1,13.

È possibile verificare la versione di TensorFlow e disinstallarla come segue:

  1. Avviare una shell dei comandi, attivare l'ambiente conda in cui sono installati i pacchetti di Machine Learning automatici.
  1. Immettere `pip freeze` e cercare `tensorflow` , se presente, la versione elencata deve essere < 1,13
  1. Se la versione elencata non è supportata, `pip uninstall tensorflow` nella shell dei comandi e immettere y per la conferma.

## `jwt.exceptions.DecodeError`

Messaggio di errore esatto: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Per le versioni dell'SDK <= 1.17.0, l'installazione potrebbe avere come risultato una versione non supportata di PyJWT. Verificare che la versione di PyJWT nell'ambiente automatico ML conda sia una versione supportata. Questa è la versione di PyJWT < 2.0.0.

È possibile controllare la versione di PyJWT come indicato di seguito:

1. Avviare una shell dei comandi e attivare l'ambiente conda in cui sono installati i pacchetti di Machine Learning automatici.

1. Immettere `pip freeze` e cercare `PyJWT` , se presente, la versione elencata deve essere < 2.0.0

Se la versione elencata non è supportata:

1. Provare a eseguire l'aggiornamento alla versione più recente di AutoML SDK: `pip install -U azureml-sdk[automl]`

1. Se non è possibile, disinstallare PyJWT dall'ambiente e installare la versione corretta nel modo seguente:

    1. `pip uninstall PyJWT` nella shell dei comandi e immettere `y` per la conferma.
    1. Eseguire l'installazione usando `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Vedere [come configurare un esperimento di Machine Learning automatizzato con databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Il Punteggio di previsione R2 è sempre zero

 Questo problema si verifica se i dati di training forniti hanno serie temporali che contengono lo stesso valore per gli ultimi `n_cv_splits`  +  `forecasting_horizon` punti dati.

Se questo modello è previsto nella serie temporale, è possibile passare dalla metrica primaria alla **radice normalizzata con l'errore quadratico medio**.

## <a name="failed-deployment"></a>Distribuzione non riuscita

 Per le versioni <= 1.18.0 dell'SDK, l'immagine di base creata per la distribuzione potrebbe non riuscire con l'errore seguente: `ImportError: cannot import name cached_property from werkzeug` .

  Per risolvere il problema, attenersi alla procedura seguente:

  1. Scaricare il pacchetto del modello
  1. Decomprimere il pacchetto
  1. Eseguire la distribuzione usando le risorse decompresse

## <a name="sample-notebook-failures"></a>Errori del notebook di esempio

 Se un notebook di esempio ha esito negativo e si verifica un errore, la proprietà, il metodo o la libreria non esiste:

* Verificare che nel Jupyter Notebook sia stato selezionato il kernel corretto. Il kernel viene visualizzato nella parte superiore destra della pagina del notebook. Il valore predefinito è *azure_automl*. Il kernel viene salvato come parte del notebook. Se si passa a un nuovo ambiente conda, è necessario selezionare il nuovo kernel nel notebook.

  * Ad Azure Notebooks, deve essere Python 3,6.
  * Per gli ambienti conda locali, deve essere il nome dell'ambiente conda specificato in automl_setup.

* Per assicurarsi che il notebook sia per la versione dell'SDK in uso,
  * Controllare la versione dell'SDK eseguendo `azureml.core.VERSION` in una cella Jupyter notebook.
  * È possibile scaricare la versione precedente dei notebook di esempio da GitHub con i passaggi seguenti:
    1. Selezionare il `Branch` pulsante
    1. Passare alla `Tags` scheda
    1. Selezionare la versione

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su [come eseguire il training di un modello di regressione con il Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [come eseguire il training con il Machine Learning automatizzato in una risorsa remota](how-to-auto-train-remote.md).

+ Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).