---
title: 'Esercitazione: Eseguire il training e la distribuzione di un modello - Machine Learning in Azure IoT Edge'
description: In questa esercitazione si eseguirà il training di un modello di apprendimento automatico usando Azure Machine Learning e quindi il modello verrà assemblato come immagine del contenitore che può essere distribuita come modulo di Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463103"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Esercitazione: Eseguire il training e distribuire un modello di Azure Machine Learning

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In questo articolo si apprenderà come eseguire le attività seguenti:

* Usare lo studio di Azure Machine Learning per eseguire il training di un modello di Machine Learning.
* Assemblare il modello sottoposto a training in un'immagine del contenitore.
* Distribuire l'immagine del contenitore come modulo Azure IoT Edge.

Machine Learning Studio è un blocco fondamentale usato per sperimentare, eseguire il training e distribuire modelli di machine learning.

I passaggi di questo articolo vengono in genere eseguiti da data scientist.

In questa sezione dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare notebook Jupyter in un'area di lavoro Azure Machine Learning per eseguire il training di un modello di machine learning.
> * Distribuire in un contenitore il modello di Machine Learning addestrato.
> * Creare un modulo IoT Edge dal modello di Machine Learning in contenitori.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo fa parte di una serie per un'esercitazione sull'uso di Machine Learning in IoT Edge. Ogni articolo della serie si basa sulle attività di quello precedente. Se si è arrivati direttamente a questo articolo, vedere il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie.

## <a name="set-up-azure-machine-learning"></a>Configurare Azure Machine Learning

Si usa Machine Learning Studio per ospitare i due notebook di Jupyter e i file di supporto. Qui viene creato e configurato un progetto Machine Learning. Se non è stato usato Jupyter o Machine Learning Studio, di seguito sono riportati due documenti introduttivi:

* **Jupyter notebook**: [uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [Introduzione ai Azure Machine Learning in notebook di Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Dopo aver configurato il servizio, è possibile accedere a Machine Learning da qualsiasi computer. Durante l'installazione, è necessario usare la macchina virtuale di sviluppo, che include tutti i file necessari.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Installare l'estensione Azure Machine Learning per Visual Studio Code

Per Visual Studio Code nella macchina virtuale di sviluppo deve essere installata questa estensione. Se è in esecuzione un'istanza diversa, reinstallare l'estensione come descritto in [configurare l'estensione Visual Studio Code](../machine-learning/tutorial-setup-vscode-extension.md).

### <a name="create-an-azure-machine-learning-account"></a>Creare un account di Azure Machine Learning

Per effettuare il provisioning delle risorse ed eseguire i carichi di lavoro in Azure, accedere con le credenziali dell'account Azure.

1. In Visual Studio Code aprire il riquadro comandi selezionando **Visualizza** > **Riquadro comandi** sulla barra dei menu.

1. Immettere il comando `Azure: Sign In` nel riquadro comandi per avviare il processo di accesso. Seguire le istruzioni per completare l'accesso.

1. Creare un'istanza di calcolo Machine Learning per eseguire il carico di lavoro. Nel riquadro comandi immettere il comando `Azure ML: Create Compute` .
1. Selezionare la sottoscrizione di Azure.
1. Selezionare **+ Crea nuova area di lavoro di ml di Azure** e immettere il nome **turbofandemo**.
1. Selezionare il gruppo di risorse usato per questa demo.
1. Verrà visualizzato lo stato di avanzamento della creazione dell'area di lavoro nell'angolo in basso a destra della finestra di Visual Studio Code: **creazione dell'area di lavoro: turobofandemo**. Questo passaggio può richiedere un minuto o due.
1. Attendere che l'area di lavoro venga creata correttamente. Si dovrebbe vedere un messaggio analogo a **Area di lavoro di Azure ML turbofandemo creata**.

### <a name="upload-jupyter-notebook-files"></a>Caricare i file del notebook di Jupyter

Verranno caricati i file del notebook di esempio in una nuova area di lavoro Machine Learning.

1. Passare a ml.azure.com ed eseguire l'accesso.
1. Selezionare la directory Microsoft, la sottoscrizione di Azure e l'area di lavoro Machine Learning appena creata.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Screenshot che mostra la selezione dell'area di lavoro Azure Machine Learning." :::

1. Dopo aver effettuato l'accesso all'area di lavoro di Machine Learning, passare alla sezione **Notebooks (notebook** ) usando il menu a sinistra.
1. Selezionare la scheda **File personali**.

1. Selezionare **carica** (icona freccia su).

1. Passare a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selezionare tutti i file nell'elenco e selezionare **Apri**.

1. Selezionare la casella **di controllo Considera attendibile il contenuto di questi file** .

1. Selezionare **carica** per avviare il caricamento. Quindi selezionare **fine** al termine del processo.

### <a name="jupyter-notebook-files"></a>File di notebook di Jupyter

Esaminiamo i file caricati nell'area di lavoro Machine Learning. Le attività in questa parte dell'esercitazione si estendono su due file di notebook, che usano alcuni file di supporto.

* **01-turbofan \_ regressione. ipynb**: questo notebook usa l'area di lavoro Machine Learning per creare ed eseguire un esperimento di machine learning. In generale, il notebook esegue queste operazioni:

  1. Scarica i dati dall'account di archiviazione di Azure generato dall'harness del dispositivo.
  1. Esplora e prepara i dati, quindi utilizza i dati per eseguire il training del modello di classificazione.
  1. Valuta il modello dall'esperimento usando un set di dati di test (test \_FD003.txt).
  1. Pubblica il modello di classificazione migliore nell'area di lavoro Machine Learning.

* **02-il \_ modello di distribuzione di turbofan \_ . ipynb**: questo notebook acquisisce il modello creato nel notebook precedente e lo usa per creare un'immagine del contenitore pronta per essere distribuita in un dispositivo IOT Edge. Il notebook esegue i passaggi seguenti:

  1. Crea uno script di assegnazione di punteggi per il modello.
  1. Produce un'immagine del contenitore usando il modello di classificazione salvato nell'area di lavoro Machine Learning.
  1. Distribuisce l'immagine come servizio Web nelle istanze di contenitore di Azure.
  1. Usa il servizio Web per verificare se il modello e l'immagine funzionano come previsto. L'immagine convalidata verrà distribuita nel dispositivo IoT Edge nella sezione [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md) di questa esercitazione.

* **\_FD003.txtdi test**: questo file contiene i dati che verranno usati come set di test quando si convaliderà il classificatore sottoposto a training. Per semplicità, vengono usati i dati di test come set di test così come vengono forniti per il contesto originale.
* **RUL \_FD003.txt**: questo file contiene la vita utile rimanente (RUL) per l'ultimo ciclo di ogni dispositivo nel file diFD003.txt di test \_ . \\ \\ \\ \\ Per una spiegazione dettagliata dei dati, vedere il readme.txt e i file Modeling.pdf propagazione dei danni in C: Source IoTEdgeAndMlSample data turbofan.
* **Utils.py**: questo file contiene un set di funzioni di utilità Python per l'uso dei dati. Il primo notebook contiene una spiegazione dettagliata delle funzioni.
* **Readme.MD**: questo file Leggimi descrive l'uso dei notebook.

## <a name="run-the-jupyter-notebooks"></a>Eseguire i notebook di Jupyter

Una volta creata l'area di lavoro, è possibile eseguire i notebook.

1. Nella pagina **File personali** selezionare **01-turbofan\_regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Screenshot che mostra la selezione del primo notebook da eseguire.":::

1. Se il notebook è elencato come **non attendibile**, selezionare il widget **non attendibile** nell'angolo superiore destro del notebook. Quando viene visualizzata la finestra di dialogo, selezionare **attendibilità**.

1. Per ottenere risultati ottimali, leggere la documentazione relativa a ogni cella ed eseguirla singolarmente. Selezionare **Run** (Esegui) sulla barra degli strumenti. In un secondo momento, sarà opportuno eseguire più celle. È possibile ignorare gli avvisi relativi ad aggiornamenti e deprecazioni.

    Durante l'esecuzione, la cella visualizza un asterisco tra parentesi quadre, ([\*]). Al termine dell'operazione della cella, l'asterisco viene sostituito con un numero ed è possibile che venga visualizzato l'output pertinente. Le celle in un notebook vengono compilate in sequenza ed è possibile eseguire una sola cella alla volta.

    È anche possibile usare opzioni di esecuzione dal menu **cella** . Premere **CTRL + INVIO** per eseguire una cella e premere **MAIUSC + INVIO** per eseguire una cella e passare alla cella successiva.

    > [!TIP]
    > Per operazioni coerenti sulle celle, evitare di eseguire lo stesso notebook da più schede del browser.

1. Nella cella che segue le istruzioni **set Global Properties** immettere i valori per la sottoscrizione, le impostazioni e le risorse di Azure. Eseguire quindi la cella.

    ![Screenshot che mostra l'impostazione delle proprietà globali nel notebook.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Nella cella precedente ai **Dettagli dell'area di lavoro**, dopo l'esecuzione, cercare il collegamento che indica di effettuare l'accesso per l'autenticazione.

    ![Screenshot che mostra la richiesta di accesso per l'autenticazione del dispositivo.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Aprire il collegamento e immettere il codice specificato. Questa procedura di accesso autentica il notebook di Jupyter per accedere alle risorse di Azure usando l'interfaccia della riga di comando multipiattaforma Microsoft Azure.

    ![Screenshot che illustra l'autenticazione dell'applicazione nella conferma del dispositivo.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Nella cella che precede **Explore the results** (Esplora i risultati), copiare il valore dall'ID esecuzione e incollarlo per l'ID esecuzione nella cella che segue **Reconstitute a run** (Ricostituisci un'esecuzione).

   ![Screenshot che mostra la copia dell'ID esecuzione tra le celle.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Eseguire le celle rimanenti nel notebook.

1. Salvare il notebook e tornare alla pagina del progetto.

1. Aprire **02-turbofan \_ deploy \_ Model. ipynb** ed eseguire ogni cella. È necessario eseguire l'accesso per eseguire l'autenticazione nella cella che segue **configurare l'area di lavoro**.

1. Salvare il notebook e tornare alla pagina del progetto.

### <a name="verify-success"></a>Verificare l'esito positivo

Per verificare se i notebook sono stati completati correttamente, controllare che siano stati creati alcuni elementi.

1. Nella scheda file di Machine Learning notebook **personali** selezionare **Aggiorna**.

1. Verificare che siano stati creati i file seguenti.

    | File | Descrizione |
    | --- | --- |
    | ./aml_config/.azureml/config.json | File di configurazione utilizzato per creare l'area di lavoro Machine Learning. |
    | ./aml_config/model_config.json | File di configurazione che è necessario distribuire il modello nell'area di lavoro Machine Learning **turbofanDemo** in Azure. |
    | myenv.yml| Fornisce informazioni sulle dipendenze per il modello di Machine Learning distribuito.|

1. Verificare che siano state create le risorse di Azure seguenti. Alcuni nomi di risorse vengono aggiunti con caratteri casuali.

    | Risorsa di Azure | Nome |
    | --- | --- |
    | Area di lavoro di Azure Machine Learning | turborfanDemo |
    | Registro Azure Container | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Insieme di credenziali chiave di Azure | turbofankeyvaultbxxxxxxxx |
    | Archiviazione di Azure | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debug

È possibile inserire istruzioni Python nel notebook ai fini del debug, ad esempio il comando `print()` per mostrare i valori. Se vengono visualizzate variabili o oggetti che non sono definiti, eseguire le celle in cui sono stati dichiarati o creati per la prima volta.

Se è necessario ripetere i notebook, potrebbe essere necessario eliminare i file creati in precedenza e le risorse di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione fa parte di un set in cui ogni articolo si basa sul lavoro svolto nei precedenti. Attendere per pulire le risorse fino al completamento dell'esercitazione finale.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati usati due notebook di Jupyter in esecuzione in Machine Learning Studio per usare i dati dei dispositivi turbofan per:

- Eseguire il training di un classificatore RUL.
- Salvare il classificatore come modello.
- Creare un'immagine del contenitore.
- Distribuire e testare l'immagine come un servizio Web.

Continuare con l'articolo successivo per creare un dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurare un dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)