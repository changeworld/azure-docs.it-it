---
title: Tenere traccia, monitorare e analizzare le esecuzioni
titleSuffix: Azure Machine Learning
description: Informazioni su come avviare, monitorare e tenere traccia delle esecuzioni dell'esperimento di Machine Learning con Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: af591a5defcef1e8d043d54f08447324a34a10c4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876296"
---
# <a name="start-monitor-and-track-run-history"></a>Avviare, monitorare e tenere traccia della cronologia di esecuzione

L Azure Machine Learning SDK per [Python,](/python/api/overview/azure/ml/intro) [Machine Learning CLI](reference-azure-machine-learning-cli.md)e [studio di Azure Machine Learning](https://ml.azure.com) diversi metodi per monitorare, organizzare e tenere traccia delle esecuzioni per il training e la sperimentazione. La cronologia di esecuzione di ML è una parte importante di un processo di sviluppo di ML spiegabile e ripetibile.

Questo articolo illustra come eseguire le attività seguenti:

* Monitorare le prestazioni di esecuzione.
* Creare una visualizzazione personalizzata. 
* Aggiungere una descrizione dell'esecuzione. 
* Contrassegnare e trovare le esecuzioni.
* Eseguire la ricerca nella cronologia di esecuzione. 
* Annullamento o esito negativo delle esecuzioni.
* Creare esecuzioni figlio.
* Monitorare lo stato di esecuzione tramite notifica tramite posta elettronica.
 

> [!TIP]
> Per informazioni sul monitoraggio dell'servizio Azure Machine Learning e dei servizi di Azure associati, vedere [Come monitorare Azure Machine Learning](monitor-azure-machine-learning.md).
> Per informazioni sui modelli di monitoraggio distribuiti come servizi Web o moduli IoT Edge, vedere [Raccogliere](how-to-enable-data-collection.md) dati del modello e Monitorare con Application Insights [.](how-to-enable-app-insights.md)

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Un'Azure Machine Learning di lavoro.](how-to-manage-workspace.md)

* L Azure Machine Learning SDK per Python (versione 1.0.21 o successiva). Per installare o aggiornare l'SDK alla versione più recente, vedere [Installare o aggiornare l'SDK](/python/api/overview/azure/ml/install).

    Per controllare la versione di Azure Machine Learning SDK, usare il codice seguente:

    ```python
    print(azureml.core.VERSION)
    ```

* [L'interfaccia della riga di comando](/cli/azure/?preserve-view=true&view=azure-cli-latest) di Azure e [l'estensione dell'Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Monitorare le prestazioni di esecuzione

* Avviare un'esecuzione e il relativo processo di registrazione

    # <a name="python"></a>[Python](#tab/python)
    
    1. Configurare l'esperimento importando le classi [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29)e [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) dal [pacchetto azureml.core.](/python/api/azureml-core/azureml.core)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Avviare un'esecuzione e il relativo processo di registrazione con il [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metodo .
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    Per avviare un'esecuzione dell'esperimento, seguire questa procedura:
    
    1. Da una shell o da un prompt dei comandi usare l'interfaccia della riga di comando di Azure per eseguire l'autenticazione nella sottoscrizione di Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Collegare una configurazione dell'area di lavoro alla cartella che contiene lo script di training. Sostituire `myworkspace` con l'area Azure Machine Learning lavoro. Sostituire `myresourcegroup` con il gruppo di risorse di Azure che contiene l'area di lavoro:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Questo comando crea una `.azureml` sottodirectory che contiene i file di ambiente runconfig e conda di esempio. Contiene anche un file `config.json` usato per comunicare con l'area di lavoro di Azure Machine Learning.
    
        Per altre informazioni, vedere [az ml folder attach](/cli/azure/ml/folder?preserve-view=true&view=azure-cli-latest#az_ml_folder_attach).
    
    2. Per avviare l'esecuzione, usare il comando seguente. Quando si usa questo comando, specificare il nome del file runconfig (il testo prima di .runconfig se si sta esaminando il file system) rispetto \* al parametro -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Il `az ml folder attach` comando ha creato una `.azureml` sottodirectory che contiene due file runconfig di esempio.
        >
        > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
        >
        > Per altri file runconfig di esempio, vedere [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Per altre informazioni, vedere [az ml run submit-script](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Per un esempio di training di un modello nella finestra Azure Machine Learning, vedere Esercitazione: Stimare il prezzo di [un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md).

    ---

* Monitorare lo stato di un'esecuzione

    # <a name="python"></a>[Python](#tab/python)
    
    * Ottenere lo stato di un'esecuzione con il [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metodo .
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Per ottenere l'ID esecuzione, il tempo di esecuzione e altri dettagli sull'esecuzione, usare il [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) metodo .
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Al termine dell'esecuzione, usare il [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) metodo per contrassegnarla come completata.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Se si usa il modello di progettazione di Python, l'esecuzione contrassegnerà automaticamente se stessa come completata quando `with...as` l'esecuzione non è inclusa nell'ambito. Non è necessario contrassegnare manualmente l'esecuzione come completata.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    * Per visualizzare un elenco di esecuzioni per l'esperimento, usare il comando seguente. Sostituire `experiment` con il nome dell'esperimento:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Questo comando restituisce un documento JSON che elenca informazioni sulle esecuzioni per questo esperimento.
    
        Per altre informazioni, vedere [az ml experiment list](/cli/azure/ml/experiment?preserve-view=true&view=azure-cli-latest#az_ml_experiment_list).
    
    * Per visualizzare informazioni su un'esecuzione specifica, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Questo comando restituisce un documento JSON che elenca le informazioni sull'esecuzione.
    
        Per altre informazioni, vedere [az ml run show](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---
## <a name="custom-view"></a>Vista personalizzata 
    
Per visualizzare le esecuzioni in studio: 
    
1. Passare alla **scheda Esperimenti.**
    
1. Selezionare **Tutti gli esperimenti per** visualizzare tutte le  esecuzioni in un esperimento oppure Tutte le esecuzioni per visualizzare tutte le esecuzioni inviate nell'area di lavoro.
    
Nella pagina **Tutte le esecuzioni** è possibile filtrare l'elenco di esecuzioni in base a tag, esperimenti, destinazione di calcolo e altro ancora per organizzare e impostare l'ambito del lavoro.  
    
1. Apportare personalizzazioni alla pagina selezionando le esecuzioni da confrontare, aggiungendo grafici o applicando filtri. Queste modifiche possono essere salvate come **visualizzazione personalizzata in** modo da poter tornare facilmente al lavoro. Gli utenti con autorizzazioni dell'area di lavoro possono modificare o visualizzare la visualizzazione personalizzata. Condividere anche la visualizzazione personalizzata con i membri del team per una collaborazione avanzata selezionando **Condividi visualizzazione**.   
    
:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views.gif" alt-text="Screenshot: creare una visualizzazione personalizzata":::
    
1. Per visualizzare i log di esecuzione, selezionare un'esecuzione specifica e nella scheda **Output e log** è possibile trovare i log di diagnostica e di errore per l'esecuzione.

## <a name="run-description"></a>Descrizione dell'esecuzione 

Una descrizione dell'esecuzione può essere aggiunta a un'esecuzione per fornire più contesto e informazioni all'esecuzione. È anche possibile cercare queste descrizioni dall'elenco delle esecuzioni e aggiungere la descrizione dell'esecuzione come colonna nell'elenco delle esecuzioni. 

Passare alla pagina **Dettagli esecuzione per** l'esecuzione e selezionare l'icona di modifica o matita per aggiungere, modificare o eliminare descrizioni per l'esecuzione. Per rendere persistenti le modifiche all'elenco delle esecuzioni, salvare le modifiche nella visualizzazione personalizzata esistente o in una nuova visualizzazione personalizzata. Il formato Markdown è supportato per le descrizioni di esecuzione, che consente di incorporare e deep linking come illustrato di seguito.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description.gif" alt-text="Screenshot: creare una descrizione dell'esecuzione"::: 

## <a name="tag-and-find-runs"></a>Contrassegnare e trovare le esecuzioni

In Azure Machine Learning è possibile usare proprietà e tag per organizzare ed eseguire query sulle esecuzioni per ottenere informazioni importanti.

* Aggiungere proprietà e tag

    # <a name="python"></a>[Python](#tab/python)
    
    Per aggiungere metadati ricercabili alle esecuzioni, usare il [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metodo . Ad esempio, il codice seguente aggiunge la `"author"` proprietà all'esecuzione:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Le proprietà non sono modificabili, quindi creano un record permanente a scopo di controllo. L'esempio di codice seguente genera un errore, perché è già stato aggiunto `"azureml-user"` come valore della proprietà nel codice `"author"` precedente:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    A differenza delle proprietà, i tag sono modificabili. Per aggiungere informazioni ricercabili e significative per i consumer dell'esperimento, usare il [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metodo .
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    È anche possibile aggiungere semplici tag di stringa. Quando questi tag vengono visualizzati nel dizionario dei tag come chiavi, hanno un valore `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Usando l'interfaccia della riga di comando, è possibile aggiungere o aggiornare solo tag.
    
    Per aggiungere o aggiornare un tag, usare il comando seguente:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Per altre informazioni, vedere [az ml run update.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_update)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    È possibile aggiungere, modificare o eliminare tag di esecuzione da Studio. Passare alla pagina **Dettagli esecuzione** per l'esecuzione e selezionare l'icona di modifica o matita per aggiungere, modificare o eliminare tag per le esecuzioni. È anche possibile cercare e filtrare questi tag dalla pagina dell'elenco delle esecuzioni.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Screenshot: Aggiungere, modificare o eliminare tag di esecuzione":::
    
    ---

* Eseguire query su proprietà e tag

    È possibile eseguire query su esecuzioni all'interno di un esperimento per restituire un elenco di esecuzioni che corrispondono a proprietà e tag specifici.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    L'interfaccia della riga di comando di Azure supporta le [query JMESPath,](http://jmespath.org) che possono essere usate per filtrare le esecuzioni in base alle proprietà e ai tag. Per usare una query JMESPath con l'interfaccia della riga di comando di Azure, specificarla con il `--query` parametro . Gli esempi seguenti illustrano alcune query che usano proprietà e tag:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Per altre informazioni sull'esecuzione di query sui risultati dell'interfaccia della riga di comando di Azure, vedere [Eseguire query sull'output del comando dell'interfaccia della riga di comando di Azure.](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Per cercare esecuzioni specifiche, passare **all'elenco Tutte le esecuzioni.** Da qui sono disponibili due opzioni:
    
    1. Usare il **pulsante Aggiungi** filtro e selezionare filtra i tag per filtrare le esecuzioni in base al tag assegnato alle esecuzioni. <br><br>
    OR
    
    1. Usare la barra di ricerca per trovare rapidamente le esecuzioni eseguendo ricerche nei metadati di esecuzione, ad esempio lo stato di esecuzione, le descrizioni, i nomi degli esperimenti e il nome dell'mittente. 
    
## <a name="cancel-or-fail-runs"></a>Annullamento o esito negativo delle esecuzioni

Se si nota un errore o se l'esecuzione sta prendendo troppo tempo per il completamento, è possibile annullare l'esecuzione.

# <a name="python"></a>[Python](#tab/python)

Per annullare un'esecuzione usando l'SDK, usare il [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metodo :

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Se l'esecuzione viene completata, ma contiene un errore (ad esempio, è stato usato lo script di training non corretto), è possibile usare il metodo per contrassegnarlo come non [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) riuscito.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per annullare un'esecuzione tramite l'interfaccia della riga di comando, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Per altre informazioni, vedere [az ml run cancel.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_cancel)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Per annullare un'esecuzione in Studio, seguire questa procedura:

1. Passare alla pipeline in esecuzione nella **sezione Experiments** (Esperimenti) **o Pipelines (Pipeline).** 

1. Selezionare il numero di esecuzione della pipeline che si vuole annullare.

1. Sulla barra degli strumenti selezionare **Annulla**

---

## <a name="create-child-runs"></a>Creare esecuzioni figlio

Creare esecuzioni figlio per raggruppare le esecuzioni correlate, ad esempio per diverse iterazioni di ottimizzazione degli iperparamementi.

> [!NOTE]
> Le esecuzioni figlio possono essere create solo usando l'SDK.

Questo esempio di codice usa lo script per creare un batch di cinque esecuzioni figlio dall'interno di `hello_with_children.py` un'esecuzione inviata usando il metodo [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) :

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Quando vengono spostate all'esterno dell'ambito, le esecuzioni figlio vengono contrassegnate automaticamente come completate.

Per creare molte esecuzioni figlio in modo efficiente, usare il [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metodo . Poiché ogni creazione comporta una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente rispetto alla creazione una alla volta.

### <a name="submit-child-runs"></a>Inviare esecuzioni figlio

Le esecuzioni figlio possono anche essere inviate da un'esecuzione padre. In questo modo è possibile creare gerarchie di esecuzioni padre e figlio. Non è possibile creare un'esecuzione figlio senza padre: anche se l'esecuzione padre non esegue alcuna operazione ma avvia le esecuzioni figlio, è comunque necessario creare la gerarchia. Gli stati di tutte le esecuzioni sono indipendenti: un elemento padre può essere nello stato completato anche se una o più esecuzioni figlio sono state `"Completed"` annullate o non riuscite.  

È possibile che le esecuzioni figlio usino una configurazione di esecuzione diversa rispetto all'esecuzione padre. Ad esempio, è possibile usare una configurazione meno potente basata sulla CPU per l'elemento padre, mentre si usano configurazioni basate su GPU per gli elementi figlio. Un altro obiettivo comune è passare ogni argomento e dati diversi figlio. Per personalizzare un'esecuzione figlio, creare un `ScriptRunConfig` oggetto per l'esecuzione figlio. 

> [!IMPORTANT]
> Per inviare un'esecuzione figlio da un'esecuzione padre in un calcolo remoto, è prima necessario accedere all'area di lavoro nel codice di esecuzione padre. Per impostazione predefinita, l'oggetto contesto di esecuzione in un'esecuzione remota non dispone di credenziali per inviare esecuzioni figlio. Usare un'entità servizio o le credenziali dell'identità gestita per accedere. Per altre informazioni sull'autenticazione, vedere [Configurare l'autenticazione](how-to-setup-authentication.md).

Il codice seguente:

- Recupera una risorsa di calcolo denominata `"gpu-cluster"` dall'area di lavoro `ws`
- Scorre valori di argomento diversi da passare agli oggetti `ScriptRunConfig` figlio
- Crea e invia una nuova esecuzione figlio, usando la risorsa di calcolo personalizzata e l'argomento
- Si blocca fino al completamento di tutte le esecuzioni figlio

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Per creare in modo efficiente molte esecuzioni figlio con configurazioni, argomenti e input identici, usare il [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metodo . Poiché ogni creazione comporta una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente rispetto alla creazione una alla volta.

All'interno di un'esecuzione figlio è possibile visualizzare l'ID esecuzione padre:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Esecuzione di query figlio

Per eseguire query nelle esecuzioni figlio di un elemento padre specifico, usare il [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metodo . ``recursive = True``L'argomento consente di eseguire query su un albero annidato di figli e nonni.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Eseguire il log nell'esecuzione padre o radice

È possibile usare il `Run.parent` campo per accedere all'esecuzione che ha avviato l'esecuzione figlio corrente. Un caso d'uso comune per `Run.parent` l'uso è quello di combinare i risultati del log in un'unica posizione. Le esecuzioni figlio vengono eseguite in modo asincrono e non vi è alcuna garanzia di ordinamento o sincronizzazione oltre la possibilità del padre di attendere il completamento delle esecuzioni figlio.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>Monitorare lo stato dell'esecuzione tramite notifica tramite posta elettronica

1. Nel riquadro [portale di Azure](https://ms.portal.azure.com/)selezionare la scheda Monitoraggio nella barra **di spostamento** a sinistra. 

1. Selezionare **Impostazioni di diagnostica** e quindi + Aggiungi impostazione di **diagnostica.**

    ![Screenshot delle impostazioni di diagnostica per la notifica tramite posta elettronica](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. Nell'impostazione di diagnostica 
    1. In **Dettagli categoria selezionare** **AmlRunStatusChangedEvent.** 
    1. In Dettagli **destinazione selezionare** l'area di lavoro Invia a **Log Analytics**  e specificare l'area **di** lavoro Sottoscrizione e **Log Analytics**. 

    > [!NOTE]
    > **L'area di lavoro Log Analytics** di Azure è un tipo di risorsa di Azure diverso rispetto all'area servizio Azure Machine Learning **lavoro**. Se non sono presenti opzioni nell'elenco, è possibile creare [un'area di lavoro Log Analytics.](../azure-monitor/logs/quick-create-workspace.md) 
    
    ![Dove salvare la notifica tramite posta elettronica](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. Nella scheda **Log** aggiungere una **nuova regola di avviso.** 

    ![Nuova regola di avviso](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Informazioni [su come creare e gestire gli avvisi del log usando Monitoraggio di Azure](../azure-monitor/alerts/alerts-log.md).

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti illustrati in questo articolo:

* Per altre informazioni sulle API di registrazione, vedere il [notebook dell'API di registrazione.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* Per altre informazioni sulla gestione delle esecuzioni con Azure Machine Learning SDK, vedere il [notebook gestire le esecuzioni.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere Registrare le metriche [durante le esecuzioni di training.](how-to-log-view-metrics.md)
* Per informazioni su come monitorare le risorse e i log Azure Machine Learning, vedere [Monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md).