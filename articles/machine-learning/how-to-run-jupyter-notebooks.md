---
title: Come eseguire Jupyter notebook nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire Jupyter notebook senza uscire dall'area di lavoro in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: cdd8783daf9e1e1458f47e773eb3b6ccedfbae83
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624558"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Come eseguire Jupyter Notebook nell'area di lavoro

Informazioni su come eseguire i notebook di Jupyter direttamente nell'area di lavoro in Azure Machine Learning Studio. Sebbene sia possibile avviare [Jupyter](https://jupyter.org/) or [JupyterLab](https://jupyterlab.readthedocs.io), è anche possibile modificare ed eseguire i notebook senza uscire dall'area di lavoro.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Creare notebook

Nell'area di lavoro di Azure Machine Learning creare un nuovo Jupyter Notebook e iniziare a lavorare. Il notebook appena creato viene archiviato nella risorsa di archiviazione dell'area di lavoro predefinita. Questo notebook può essere condiviso con chiunque abbia accesso all'area di lavoro. 

Per creare un nuovo notebook: 

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook). 
1. Selezionare l'icona  **Create new file** (Crea nuovo file) sopra l'elenco **User files** (File utente) nella sezione **My files** (File personali).

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file"::: (Crea nuovo file)

1. Assegnare un nome al file. 
1. Per i file di Jupyter notebook, selezionare **notebook** come tipo di file.
1. Selezionare una directory di file.
1. Selezionare **Create** (Crea).

È anche possibile creare file di testo.  Selezionare **Text** (Testo) come tipo di file e aggiungere l'estensione al nome (ad esempio, myfile.py o myfile.txt)  

È anche possibile caricare cartelle e file, inclusi i notebook, con gli strumenti disponibili nella parte superiore della pagina Notebooks (Notebook).  I notebook e la maggior parte dei tipi di file di testo vengono visualizzati nella sezione di anteprima.  Non sono disponibili anteprime per la maggior parte degli altri tipi di file.

> [!IMPORTANT]
> Il contenuto di notebook e script può potenzialmente leggere i dati delle sessioni e accedere ai dati senza l'organizzazione in Azure.  Caricare solo file da origini attendibili. Per altre informazioni, vedere [procedure consigliate per il codice protetto](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Clonare gli esempi

L'area di lavoro contiene una cartella **Samples** (Esempi) con notebook progettati per facilitare l'esplorazione dell'SDK ed essere usati come esempi per i propri progetti di Machine Learning.  È possibile clonare questi notebook nella propria cartella nel contenitore di archiviazione dell'area di lavoro.  

Per un esempio, vedere [Esercitazione: Creare il primo esperimento di Machine Learning](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Usare i file GIT e controllare le versioni dei file personali

È possibile accedere a tutte le operazioni di GIT usando una finestra del terminale. Tutti i file e le cartelle GIT verranno archiviati nel file system dell'area di lavoro.

> [!NOTE]
> Aggiungere i file e le cartelle in un punto qualsiasi della cartella **~/cloudfiles/code/Users** in modo che siano visibili in tutti gli ambienti Jupyter.

Per accedere al terminale:

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook).
1. Selezionare un notebook che si trova nella sezione **User files** (File utente) sul lato sinistro.  Se non sono presenti notebook, prima [creare un notebook](#create)
1. Selezionare una destinazione di **calcolo** o crearne una nuova e attendere che sia in esecuzione.
1. Selezionare l'icona **Open terminal** (Apri terminale).

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Aprire il terminale":::

1. Se l'icona non viene visualizzata, selezionare il **...** a destra della destinazione di calcolo e quindi selezionare **Apri terminale**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Open terminal"::: (Apri terminale)


Vedere altre informazioni su come [clonare i repository GIT nel file system dell'area di lavoro](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Copiare e incollare nel terminale

> * Windows: `Ctrl-Insert` per copiare e usare `Ctrl-Shift-v` o `Shift-Insert` per incollare.
> * Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare.
> * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.

### <a name="share-notebooks-and-other-files"></a>Condividere notebook e altri file

Copiare e incollare l'URL per condividere un notebook o un file.  Solo gli altri utenti dell'area di lavoro possono accedere a questo URL.  Vedere altre informazioni su come [concedere l'accesso all'area di lavoro](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Modificare un notebook

Per modificare un notebook, aprire un notebook nella sezione **User files** (File utente) dell'area di lavoro. Fare clic sulla cella che si vuole modificare. 

È possibile modificare il notebook senza connettersi a un'istanza di calcolo.  Quando si desidera eseguire le celle nel notebook, selezionare o creare un'istanza di calcolo.  Se si seleziona un'istanza di calcolo arrestata, questa verrà avviata automaticamente quando si esegue la prima cella.

Quando un'istanza di calcolo è in esecuzione, è anche possibile usare il completamento del codice, alimentato da [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), in qualsiasi notebook di Python.

È anche possibile avviare Jupyter o JupyterLab dalla barra degli strumenti del notebook.  Azure Machine Learning non offre aggiornamenti e correzioni di bug di Jupyter o JupyterLab poiché sono prodotti open source non coperti dal supporto tecnico Microsoft.

### <a name="focus-mode"></a>Modalità messa a fuoco

Usare la modalità messa a fuoco per espandere la visualizzazione corrente in modo da potersi concentrare sulle schede attive. La modalità messa a fuoco nasconde Esplora file dei notebook.

1. Nella barra degli strumenti della finestra del terminale selezionare **modalità messa a fuoco** per attivare la modalità messa a fuoco. A seconda della larghezza della finestra, è possibile che si trovi nella voce di menu **..** . nella barra degli strumenti.
1. In modalità messa a fuoco tornare alla visualizzazione standard selezionando **visualizzazione standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Attiva/Nascondi modalità messa a fuoco/visualizzazione standard":::


### <a name="use-intellisense"></a>Usare IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) è un supporto per il completamento del codice che include numerose funzionalità: Elenca membri, informazioni sul parametro, informazioni rapide e completa parola. Queste funzionalità consentono di acquisire altre informazioni sul codice in uso, tengono traccia dei parametri che si digitano e aggiungono le chiamate a proprietà e metodi con poche sequenze di tasti.  

Quando si digita il codice, premere CTRL + barra spaziatrice per attivare IntelliSense.

### <a name="clean-your-notebook-preview"></a>Pulire il notebook (anteprima)

> [!IMPORTANT]
> La funzionalità di raccolta è attualmente disponibile in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nel corso della creazione di un notebook, in genere si finisce con le celle usate per l'esplorazione o il debug dei dati. La funzionalità di *raccolta* consente di creare un notebook pulito senza queste celle estranee.

1. Eseguire tutte le celle del notebook.
1. Selezionare la cella che contiene il codice che si desidera venga eseguito dal nuovo notebook. Ad esempio, il codice che invia un esperimento o forse il codice che registra un modello.
1. Selezionare l'icona **gather** visualizzata sulla barra degli strumenti della cella.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Screenshot: selezionare l'icona di raccolta":::
1. Immettere il nome del nuovo notebook "raccolto".  

Il nuovo notebook contiene solo celle di codice, con tutte le celle necessarie per produrre gli stessi risultati della cella selezionata per la raccolta.

### <a name="save-and-checkpoint-a-notebook"></a>Salvare e Checkpoint un notebook

Azure Machine Learning crea un file del checkpoint quando si crea un file *ipynb* .

Nella barra degli strumenti del notebook selezionare il menu e quindi **file &gt; Salva e Checkpoint** per salvare manualmente il notebook. verrà aggiunto un file del checkpoint associato al notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot dello strumento Salva nella barra degli strumenti del notebook":::

Ogni notebook viene salvato in automatico ogni 30 secondi. Salvataggio automatico aggiorna solo il file *ipynb* iniziale, non il file del checkpoint.
 
Selezionare **Checkpoint** dal menu notebook per creare un checkpoint denominato e ripristinare il blocco appunti a un checkpoint salvato.

## <a name="delete-a-notebook"></a>Eliminare un notebook

*Non è possibile* eliminare i notebook della cartella **Samples** (Esempi).  Questi notebook fanno parte di Studio e vengono aggiornati ogni volta che viene pubblicato un nuovo SDK.  

*È possibile* eliminare i notebook della cartella **User files** (File utente) in uno dei modi seguenti:

* In Studio selezionare **...** alla fine di una cartella o un file.  Assicurarsi di usare un browser supportato (Microsoft Edge, Chrome o Firefox).
* Da qualsiasi barra degli strumenti del notebook selezionare [**Apri terminale**](#terminal)  per accedere alla finestra del terminale per l'istanza di calcolo.
* In Jupyter o JupyterLab con i relativi strumenti.

## <a name="run-a-notebook-or-python-script"></a>Eseguire un notebook o uno script Python

Per eseguire un notebook o uno script Python, connettersi prima di tutto a un' [istanza di calcolo](concept-compute-instance.md)in esecuzione. Se non è presente un'istanza di calcolo, seguire questa procedura per crearne una: 

1. Selezionare **+** nella barra degli strumenti del notebook o dello script. 
2. Assegnare un nome al calcolo e scegliere le **dimensioni della macchina virtuale**. 
3. Selezionare **Create** (Crea).
4. L'istanza di calcolo è connessa automaticamente al file.  È ora possibile eseguire le celle del notebook o lo script Python usando lo strumento a sinistra dell'istanza di calcolo

È possibile visualizzare e usare solo le istanze di calcolo create.  I file della cartella **User files** (File utente) vengono archiviati separatamente dalla macchina virtuale e sono condivisi tra tutte le istanze di calcolo nell'area di lavoro.

### <a name="view-logs-and-output"></a>Visualizzare i log e l'output

Usare i [widget del notebook](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) per visualizzare lo stato di avanzamento dell'esecuzione e dei log. Il widget è asincrono e offre gli aggiornamenti fino al termine del training. I widget di Azure Machine Learning sono supportati anche in Jupyter e JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Schermata: widget del notebook di Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Esplorare le variabili nel notebook

Sulla barra degli strumenti del notebook usare lo strumento **Esplora variabili** per visualizzare il nome, il tipo, la lunghezza e i valori di esempio per tutte le variabili create nel notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Schermata: strumento Esplora variabili":::

Selezionare lo strumento per visualizzare la finestra Esplora variabili.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Schermata: finestra Esplora variabili":::

## <a name="navigate-with-a-toc"></a>Spostarsi con un sommario

Sulla barra degli strumenti del notebook utilizzare lo strumento  **Sommario** per visualizzare o nascondere il sommario.  Avviare una cella Markdown con un'intestazione per aggiungerla al sommario. Fare clic su una voce nella tabella per scorrere fino a tale cella nel notebook.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Screenshot: Sommario nel notebook":::

## <a name="change-the-notebook-environment"></a>Modificare l'ambiente del notebook

La barra degli strumenti del notebook consente di modificare l'ambiente in cui viene eseguito il notebook.  

Queste azioni non modificheranno lo stato del notebook o i valori delle variabili nel notebook:

|Azione  |Risultato  |
|---------|---------| --------|
|Arrestare il kernel     |  Vengono arrestate tutte le celle in esecuzione. Quando si esegue una cella, il kernel viene riavviato automaticamente. |
|Passare a un'altra sezione dell'area di lavoro     |     Vengono arrestate le celle in esecuzione. |

Queste azioni reimposteranno lo stato del notebook e ripristineranno tutte le variabili nel notebook.

|Azione  |Risultato  |
|---------|---------| --------|
| Cambiare il kernel | Il notebook usa il nuovo kernel |
| Cambiare l'ambiente di calcolo    |     Il notebook usa automaticamente il nuovo calcolo. |
| Reimpostare il calcolo | Viene riavviato quando si tenta di eseguire una cella |
| Arrestare il calcolo     |    Non vengono eseguite celle  |
| Aprire il notebook in Jupyter o JupyterLab     |    Il notebook viene aperto in una nuova scheda.  |

### <a name="add-new-kernels"></a>Aggiungere nuovi kernel

Il notebook troverà automaticamente tutti i kernel Jupyter installati nell'istanza di calcolo connessa.  Per aggiungere un kernel all'istanza di calcolo:

1. Selezionare [**Apri terminale**](#terminal) sulla barra degli strumenti del notebook.
1. Usare la finestra del terminale per creare un nuovo ambiente.  Il codice seguente, ad esempio, crea `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Attivare l'ambiente.  Ad esempio, dopo aver creato `newenv`:

    ```shell
    conda activate newenv
    ```
1. Installare il pacchetto PIP e ipykernel nel nuovo ambiente e creare un kernel per la conda ENV

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> Per la gestione dei pacchetti in un notebook, usare le funzioni **% PIP** o **% conda** Magic per installare automaticamente i pacchetti nel **kernel attualmente in esecuzione**, anziché **! PIP** o **! conda** , che fa riferimento a tutti i pacchetti (inclusi i pacchetti all'esterno del kernel attualmente in esecuzione)

È possibile installare uno dei [kernel Jupyter disponibili](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).

### <a name="status-indicators"></a>Indicatori di stato

Un indicatore accanto all'elenco a discesa **Compute** (Calcolo) mostra lo stato del calcolo.  Lo stato è visualizzato anche nell'elenco a discesa.  

|Colore |Stato del calcolo |
|---------|---------| 
| Green | Esecuzione del calcolo |
| Rosso |Calcolo non riuscito | 
| Nero | Calcolo arrestato |
|  Blu chiaro |Creazione, avvio, riavvio, configurazione del calcolo |
|  Grigio |Eliminazione, arresto del calcolo |

Un indicatore accanto all'elenco a discesa **Kernel** mostra lo stato del kernel.

|Colore |Stato del kernel |
|---------|---------|
|  Green |Kernel connesso, inattivo, occupato|
|  Grigio |Kernel non connesso |

## <a name="shortcut-keys"></a>Combinazioni di tasti
Analogamente ai notebook di Jupyter, Azure Machine Learning Studio notebook hanno un'interfaccia utente modale. La tastiera esegue diverse operazioni a seconda della modalità in cui si trova la cella del notebook. Azure Machine Learning Studio notebook supportano le due modalità seguenti per una cella di codice specificata: modalità comando e modalità di modifica.

### <a name="command-mode-shortcuts"></a>Collegamenti alla modalità di comando

Una cella è in modalità di comando quando non è presente un cursore di testo che richiede di digitare. Quando una cella è in modalità di comando, è possibile modificare il notebook nel suo complesso, ma non digitare in singole celle. Immettere la modalità comando premendo `ESC` o usando il mouse per selezionare all'esterno dell'area dell'editor di una cella.  Il bordo sinistro della cella attiva è blu e a tinta unita e il pulsante **Esegui** è blu.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Cella notebook in modalità comando ":::

| Tasto di scelta rapida                      | Descrizione                          |
| ----------------------------- | ------------------------------------|
| Immettere                         | Attivare la modalità di modifica             |        
| MAIUSC+INVIO                 | Eseguire la cella, selezionare sotto         |     
| Controllo/comando + invio       | Esegui cella                            |
| ALT+INVIO                   | Eseguire la cella, inserire la cella del codice sotto    |
| Controllo/comando + ALT + INVIO | Eseguire la cella, inserire la cella Markdown sotto|
| ALT + R                       | Esegui tutto      |                       
| S                             | Converti cella in codice    |                         
| M                             | Converte la cella in Markdown  |                       
| Massimo/K                          | Selezionare la cella in alto    |               
| Giù/J                        | Selezionare la cella in basso    |               
| A                             | Inserisci cella di codice sopra  |            
| B                             | Inserisci cella di codice sotto   |           
| Controllo/comando + MAIUSC + A   | Inserisci cella Markdown sopra    |      
| Controllo/comando + MAIUSC + B   | Inserisci cella Markdown sotto   |       
| X                             | Taglia cella selezionata    |               
| C                             | Copia cella selezionata   |               
| MAIUSC + V                     | Incolla la cella selezionata sopra           |
| V                             | Incolla la cella selezionata sotto    |       
| D D                           | Elimina cella selezionata|                
| O                             | Attiva/Disattiva Output         |              
| MAIUSC + O                     | Imposta/Nascondi scorrimento output   |          
| i                           | Interrompi kernel |                   
| 0 0                           | Riavviare il kernel |                     
| MAIUSC + barra spaziatrice                 | Scorri verso l'alto  |                         
| Space                         | Scorri verso il basso|
| Scheda                           | Imposta lo stato attivo su elemento attivabile successivo (quando il trap di tabulazione è disabilitato)|
| Controllo/comando + S           | Salva notebook |                      
| 1                             | Passa a H1|                       
| 2                             | Passa a H2|                        
| 3                             | Passa a H3|                        
| 4                             | Passa a H4 |                       
| 5                             | Passa a H5 |                       
| 6                             | Passa a H6 |                       

### <a name="edit-mode-shortcuts"></a>Collegamenti alla modalità di modifica

La modalità di modifica è indicata da un cursore di testo che richiede di digitare nell'area dell'editor. Quando una cella è in modalità di modifica, è possibile digitare nella cella. Per passare alla modalità di modifica, premere `Enter` o usare il mouse per selezionare l'area dell'editor di una cella. Il bordo sinistro della cella attiva è verde e tratteggiato e il pulsante **Esegui** è verde. Viene visualizzata anche la richiesta di cursore nella cella in modalità di modifica.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Cella notebook in modalità di modifica":::

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice in Azure Machine Learning notebook in modalità di modifica.

| Tasto di scelta rapida                      | Descrizione|                                     
| ----------------------------- | ----------------------------------------------- |
| Carattere speciale di escape                        | Immettere la modalità di comando|  
| Controllo/comando + barra spaziatrice       | Attiva IntelliSense |
| MAIUSC+INVIO                 | Eseguire la cella, selezionare sotto |                         
| Controllo/comando + invio       | Esegui cella  |                                      
| ALT+INVIO                   | Eseguire la cella, inserire la cella del codice sotto  |              
| Controllo/comando + ALT + INVIO | Eseguire la cella, inserire la cella Markdown sotto  |          
| ALT + R                       | Eseguire tutte le celle     |                              
| Su                            | Spostare il cursore verso l'alto o la cella precedente    |             
| Giù                          | Spostare il cursore verso il basso o la cella successiva |                  
| Controllo/comando + S           | Salva notebook   |                                
| Controllo/comando + freccia su          | Andare all'inizio della cella   |                             
| Controllo/comando + freccia giù        | Andare alla fine della cella |                                 
| Scheda                           | Completamento o rientro del codice (se il Trap Tab è abilitato) |
| Controllo/comando + M           | Abilita/Disabilita trap tabulazione  |                       
| Controllo/comando +]           | Impostare un rientro |                                         
| Controllo/comando + [           | Annullare l'impostazione di un rientro  |                                        
| Controllo/comando + A           | Seleziona tutto|                                      
| Controllo/comando + Z           | Annulla |                                           
| Controllo/comando + MAIUSC + Z   | Ripeti |                                           
| Controllo/comando + Y           | Ripeti |                                           
| Controllo/comando + Home        | Andare all'inizio della cella|                                
| Controllo/comando + fine         | Andare alla fine della cella   |                               
| Controllo/comando + freccia sinistra        | Andare a sinistra di una parola |                               
| Controllo/comando + diritto       | Andare a destra di una parola |                              
| Controllo/comando + Backspace   | Eliminare la parola prima |                             
| Controllo/comando + Elimina      | Eliminare la parola dopo |                              
| Controllo/comando +/           | Imposta/Nascondi commento su cu

## <a name="find-compute-details"></a>Dettagli del calcolo

I dettagli sulle istanze di calcolo sono disponibili nella pagina **Compute** (Calcolo) in [Studio](https://ml.azure.com).

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se non è possibile connettersi a un notebook, assicurarsi che la comunicazione del socket Web **non** sia disabilitata. Per il funzionamento della funzionalità Jupyter dell'istanza di calcolo, è necessario abilitare la comunicazione WebSocket. Assicurarsi che la rete consenta le connessioni WebSocket a *. instances.azureml.net e *. instances.azureml.ms. 

* Quando l'istanza di calcolo viene distribuita in un'area di lavoro di collegamento privato, è possibile accedervi solo dall'interno della rete virtuale. Se si usa un file host o DNS personalizzato, aggiungere una voce per <nome istanza <region>>. instances.azureml.ms con l'indirizzo IP privato dell'endpoint privato dell'area di lavoro. Per altre informazioni, vedere l'articolo [DNS personalizzato](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli) .
    
## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il primo esperimento](tutorial-1st-experiment-sdk-train.md)
* [Eseguire il backup dell'archiviazione file con gli snapshot](../storage/files/storage-snapshots-files.md)
* [Lavorare in ambienti protetti](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
