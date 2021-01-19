---
title: 'Esercitazione: Eseguire il training e la distribuzione di un modello - Machine Learning in Azure IoT Edge'
description: Questa esercitazione mostra come eseguire il training di un modello di Machine Learning con Azure Machine Learning e quindi inserirlo in un pacchetto come immagine di contenitore che è possibile distribuire in un modulo Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121141"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Esercitazione: Eseguire il training e distribuire un modello di Azure Machine Learning

In questo articolo si apprenderà come eseguire le attività seguenti:

* Usare lo studio di Azure Machine Learning per eseguire il training di un modello di Machine Learning.
* Assemblare il modello sottoposto a training in un'immagine del contenitore.
* Distribuire l'immagine del contenitore come modulo Azure IoT Edge.

Lo studio di Azure Machine Learning è un elemento di base usato per sperimentare, eseguire il training e distribuire modelli di Machine Learning.

I passaggi di questo articolo vengono in genere eseguiti da data scientist.

In questa sezione dell'esercitazione si apprende come:

> [!div class="checklist"]
> * Creare notebook di Jupyter nell'area di lavoro di Azure Machine Learning per eseguire il training di un modello di Machine Learning.
> * Distribuire in un contenitore il modello di Machine Learning addestrato.
> * Creare un modulo Azure IoT Edge dal modello di Machine Learning in contenitori.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Ogni articolo della serie si basa sulle attività di quello precedente. Se questo articolo è stato aperto direttamente, vedere il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie.

## <a name="set-up-azure-machine-learning"></a>Configurare Azure Machine Learning 

Usare lo studio di Azure Machine Learning per ospitare due notebook di Jupyter e i file di supporto. Ora verrà creato e configurato un progetto di Azure Machine Learning. Se Jupyter e/o lo studio di Azure Machine Learning non sono mai stati usati, ecco un paio di documenti introduttivi:

* **Notebook di Jupyter:** [Uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Introduzione al servizio Azure Machine Learning nei notebook di Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Una volta configurato, il servizio Azure Machine Learning diventa accessibile da qualsiasi computer. Durante la configurazione, è consigliabile usare la VM di sviluppo, che include tutti i file necessari.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Installare l'estensione Azure Machine Learning per Visual Studio Code
Per VS Code nella macchina virtuale di sviluppo deve essere installata questa estensione. Se è in esecuzione un'istanza diversa, reinstallare l'estensione come descritto [qui.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Creare un account di Azure Machine Learning  
Per effettuare il provisioning delle risorse ed eseguire i carichi di lavoro in Azure, è necessario eseguire l'accesso con le credenziali dell'account Azure.

1. In Visual Studio Code aprire il riquadro comandi selezionando **Visualizza** > **Riquadro comandi** sulla barra dei menu. 

1. Immettere il comando `Azure: Sign In` nel riquadro comandi per avviare il processo di accesso. Seguire le istruzioni per completare l'accesso. 

1. Creare un'istanza di ambiente di calcolo di Machine Learning per eseguire il carico di lavoro. Usando il riquadro comandi immettere il comando `Azure ML: Create Compute`. 
1. Selezionare la sottoscrizione di Azure
1. Selezionare **Create a new Azure ML workspace** (Crea una nuova area di lavoro di Azure ML) e immettere il nome `turbofandemo`.
1. Selezionare il gruppo di risorse usato per questa demo.
1. Nell'angolo in basso a destra della finestra di VS Code si dovrebbe vedere lo stato di avanzamento della creazione dell'area di lavoro: **Creazione dell'area di lavoro: turobofandemo** (l'operazione può richiedere uno o due minuti). 
1. Attendere che l'area di lavoro venga creata correttamente. Si dovrebbe vedere un messaggio analogo a **Area di lavoro di Azure ML turbofandemo creata**.


### <a name="upload-jupyter-notebook-files"></a>Caricare i file del notebook di Jupyter

I file del notebook di esempio vengono caricati in una nuova area di lavoro di Azure Machine Learning.

1. Passare a ml.azure.com e accedere.
1. Selezionare la directory Microsoft, la sottoscrizione di Azure e l'area di lavoro di Azure Machine Learning appena creata.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Selezionare l'area di lavoro di Azure Machine Learning." :::

1. Dopo aver eseguito l'accesso all'area di lavoro di Azure Machine Learning, passare alla sezione **Notebook** usando il menu a sinistra.
1. Selezionare la scheda **File personali**.

1. Selezionare **Caricare** (l'icona con la freccia su) 


1. Passare a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selezionare tutti i file nell'elenco e fare clic su **Apri**.

1. Selezionare la casella **I trust the content of these files** (Considera attendibile il contenuto di questi file).

1. Selezionare **Upload** (Carica) per avviare il caricamento e quindi **Done** (Fatto) al termine del processo.

### <a name="jupyter-notebook-files"></a>File di notebook di Jupyter

Esaminare i file caricati nell'area di lavoro di Azure Machine Learning. Le attività in questa parte dell'esercitazione si estendono su due file di notebook, che usano alcuni file di supporto.

* **01-turbofan\_regression.ipynb:** Questo notebook usa l'area di lavoro del servizio Machine Learning per creare ed eseguire un esperimento di Machine Learning. In generale, il notebook esegue queste operazioni:

  1. Scarica i dati dall'account di archiviazione di Azure generato dall'harness del dispositivo.
  1. Esplora e prepara i dati, quindi usa i dati per eseguire il training del modello di classificatore.
  1. Valuta il modello dell'esperimento usando un set di dati di test (Test\_FD003.txt).
  1. Pubblica il modello di classificatore migliore nell'area di lavoro del servizio Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** questo notebook usa il modello creato nel notebook precedente per creare un'immagine di contenitore da distribuire in un dispositivo Azure IoT Edge. Il notebook esegue i passaggi seguenti:

  1. Crea uno script di assegnazione di punteggi per il modello.
  1. Produce un'immagine del contenitore usando il modello di classificatore salvato nell'area di lavoro del servizio Machine Learning.
  1. Distribuisce l'immagine come servizio Web in Istanze di Azure Container.
  1. Usa il servizio Web per verificare se il modello e l'immagine funzionano come previsto. L'immagine convalidata verrà distribuita nel dispositivo IoT Edge nella sezione [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md) di questa esercitazione.

* **Test\_FD003.txt:** questo file contiene i dati che verranno usati come set di test durante la convalida del classificatore sottoposto a training. Per semplicità, vengono usati i dati di test come set di test così come vengono forniti per il contesto originale.

* **RUL\_FD003.txt:** questo file contiene la vita utile rimanente relativa all'ultimo ciclo di ogni dispositivo incluso nel file Test\_FD003.txt. Per una spiegazione dettagliata dei dati, vedere il file readme.txt e il file Damage Propagation Modeling.pdf disponibili in C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py:** contiene un set di funzioni di utilità di Python per l'uso dei dati. Il primo notebook contiene una spiegazione dettagliata delle funzioni.

* **README.md:** file che descrive l'uso dei notebook.  

## <a name="run-jupyter-notebooks"></a>Eseguire Jupyter Notebook

Una volta creata l'area di lavoro, è possibile eseguire i notebook. 

1. Nella pagina **File personali** selezionare **01-turbofan\_regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Selezionare il primo notebook da eseguire. ":::

1. Se il notebook è indicato come **Not Trusted** (Non attendibile), fare clic sul widget **Not Trusted** nell'angolo in alto a destra. Quando viene visualizzata la finestra di dialogo, selezionare **Trust** (Considera attendibile).

1. Per ottenere risultati ottimali, leggere la documentazione relativa a ogni cella ed eseguirla singolarmente. Selezionare **Run** (Esegui) sulla barra degli strumenti. In un secondo momento, sarà utile per eseguire più celle. È possibile ignorare gli avvisi relativi ad aggiornamenti e deprecazioni.

    Durante l'esecuzione, la cella visualizza un asterisco tra parentesi quadre, ([\*]). Una volta completata l'operazione della cella, l'asterisco viene sostituito con un numero e potrebbe essere visualizzato l'output appropriato. Le celle di un notebook vengono compilate in sequenza ed è possibile eseguirne solo una alla volta.

    È anche possibile usare le opzioni di esecuzione nel menu **Cell** (Cella), `Ctrl` + `Enter` per eseguire una cella e `Shift` + `Enter` per eseguire una cella e passare a quella successiva.

    > [!TIP]
    > Per operazioni coerenti sulle celle, evitare di eseguire lo stesso notebook da più schede del browser.

1. Nella cella che segue le istruzioni **Set global properties** (Impostare le proprietà globali) specificare i valori per la sottoscrizione, le impostazioni e le risorse di Azure. Eseguire quindi la cella.

    ![Impostare le proprietà globali nel notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Nella cella precedente a **Dettagli dell'area di lavoro**, dopo l'esecuzione cercare il collegamento che indica di effettuare l'accesso per l'autenticazione:

    ![Richiesta di accesso per l'autenticazione del dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Aprire il collegamento e immettere il codice specificato. Questa procedura di accesso autentica il notebook di Jupyter per l'accesso alle risorse di Azure usando l'interfaccia della riga di comando multipiattaforma di Microsoft Azure.  

    ![Conferma dell'autenticazione dell'applicazione nel dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Nella cella che precede **Explore the results** (Esplora i risultati), copiare il valore dall'ID esecuzione e incollarlo per l'ID esecuzione nella cella che segue **Reconstitute a run** (Ricostituisci un'esecuzione).

   ![Copiare l'ID esecuzione tra celle](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Eseguire le celle rimanenti nel notebook.

1. Salvare il notebook e tornare alla pagina del progetto.

1. Aprire **02-turbofan\_deploy\_model.ipynb** ed eseguire ogni cella. È necessario eseguire l'accesso per eseguire l'autenticazione nella cella che segue **Configura area di lavoro**.

1. Salvare il notebook e tornare alla pagina del progetto.

### <a name="verify-success"></a>Verificare l'esito positivo

Per verificare se i notebook sono stati completati correttamente, controllare che siano stati creati alcuni elementi.

1. Nella scheda **File personali** dei notebook di Azure Machine Learning selezionare **Aggiorna**.

1. Verificare che siano stati creati i file seguenti:

    | File | Descrizione |
    | --- | --- |
    | ./aml_config/.azureml/config.json | File di configurazione usato per creare l'area di lavoro di Azure Machine Learning. |
    | ./aml_config/model_config.json | File di configurazione necessario per distribuire il modello nell'area di lavoro di Machine Learning **turbofanDemo** in Azure. |
    | myenv.yml| Fornisce informazioni sulle dipendenze per il modello di Machine Learning distribuito.|

1. Verificare che siano state create le risorse di Azure seguenti. Ad alcuni nomi di risorse vengono aggiunti caratteri casuali.

    | Risorsa di Azure | Nome |
    | --- | --- |
    | Area di lavoro di Machine Learning | turborfanDemo |
    | Registro Container | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Archiviazione | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debug

È possibile inserire istruzioni Python nel notebook ai fini del debug, ad esempio il comando `print()` per mostrare i valori. Se vengono visualizzate variabili o oggetti che non sono definiti, eseguire le celle in cui vengono dichiarati o di cui viene creata un'istanza la prima volta.

Se è necessario ripetere i notebook, potrebbe essere necessario eliminare i file creati in precedenza e le risorse di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione fa parte di un set in cui ogni articolo si basa sul lavoro svolto nei precedenti. Prima di pulire le risorse attendere il completamento dell'esercitazione finale.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati usati due notebook di Jupyter in esecuzione nello studio di Azure Machine Learning per usare i dati dei dispositivi turbofan ed eseguire il training di un classificatore di vita utile rimanente, salvarlo come modello, creare un'immagine di contenitore, quindi distribuire e testare l'immagine come servizio Web.

Continuare con l'articolo successivo per creare un dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurare un dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)