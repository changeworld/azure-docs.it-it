---
title: Attiva pipeline Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Le pipeline attivate consentono di automatizzare le attività di routine, che richiedono molto tempo, ad esempio l'elaborazione, il training e il monitoraggio dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519657"
---
# <a name="trigger-machine-learning-pipelines"></a>Attiva pipeline di Machine Learning

In questo articolo si apprenderà come pianificare una pipeline per l'esecuzione in Azure a livello di codice. È possibile creare una pianificazione in base al tempo trascorso o alle modifiche del file System. È possibile usare pianificazioni basate sul tempo per gestire le attività di routine, ad esempio il monitoraggio della deriva dei dati. È possibile utilizzare pianificazioni basate sulle modifiche per rispondere a modifiche irregolari o imprevedibili, ad esempio il caricamento di nuovi dati o la modifica dei dati obsoleti. Dopo aver appreso come creare pianificazioni, si apprenderà come recuperarli e disattivarli. Infine, si apprenderà come usare altri servizi di Azure, app per la logica di Azure e Azure Data Factory per eseguire le pipeline. Un'app per la logica di Azure consente di attivare logica o comportamento più complesso. Azure Data Factory pipeline consentono di chiamare una pipeline di machine learning come parte di una pipeline di orchestrazione dati più ampia.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* Ambiente Python in cui è installato Azure Machine Learning SDK per Python. Per altre informazioni, vedere [creare e gestire ambienti riutilizzabili per il training e la distribuzione con Azure Machine Learning.](how-to-use-environments.md)

* Un'area di lavoro Machine Learning con una pipeline pubblicata. È possibile usare quello creato in [creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Attivare pipeline con Azure Machine Learning SDK per Python

Per pianificare una pipeline, è necessario un riferimento all'area di lavoro, l'identificatore della pipeline pubblicata e il nome dell'esperimento in cui si vuole creare la pianificazione. È possibile ottenere questi valori con il codice seguente:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Creare una pianificazione

Per eseguire una pipeline in base a una pianificazione ricorrente, è necessario creare una pianificazione. Un oggetto `Schedule` associa una pipeline, un esperimento e un trigger. Il trigger può essere un oggetto `ScheduleRecurrence` che descrive l'attesa tra le esecuzioni o un percorso dell'archivio dati che specifica una directory da controllare per le modifiche. In entrambi i casi sarà necessario l'identificatore della pipeline e il nome dell'esperimento in cui creare la pianificazione.

Nella parte superiore del file Python importare le `Schedule` `ScheduleRecurrence` classi e:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Creare una pianificazione basata sul tempo

Il `ScheduleRecurrence` costruttore dispone di un `frequency` argomento obbligatorio che deve essere una delle seguenti stringhe: "minute", "hour", "Day", "week" o "month". Richiede anche un argomento integer che `interval` specifica il numero di `frequency` unità che devono trascorrere tra l'inizio della pianificazione. Gli argomenti facoltativi consentono di essere più specifici dell'ora di inizio, come descritto in dettaglio nella [documentazione di SCHEDULERECURRENCE SDK](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence).

Creare un `Schedule` che inizia un'esecuzione ogni 15 minuti:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Creare una pianificazione basata sulle modifiche

Le pipeline attivate dalle modifiche ai file possono essere più efficienti delle pianificazioni basate sul tempo. Quando si desidera eseguire un'operazione prima della modifica di un file o quando viene aggiunto un nuovo file a una directory dei dati, è possibile pre-elaborare il file. È possibile monitorare le modifiche apportate a un archivio dati o alle modifiche all'interno di una directory specifica all'interno dell'archivio dati. Se si monitora una directory specifica, le modifiche apportate all'interno di sottodirectory della directory _non_ attiverà un'esecuzione.

Per creare un file reattivo `Schedule` , è necessario impostare il `datastore` parametro nella chiamata a [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Per monitorare una cartella, impostare l' `path_on_datastore` argomento.

L' `polling_interval` argomento consente di specificare, in minuti, la frequenza con cui viene verificata la presenza di modifiche nell'archivio dati.

Se la pipeline è stata costruita con un [percorso di DataPath](/python/api/azureml-core/azureml.data.datapath.datapath) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter), è possibile impostare tale variabile sul nome del file modificato impostando l' `data_path_parameter_name` argomento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argomenti facoltativi durante la creazione di una pianificazione

Oltre agli argomenti descritti in precedenza, è possibile impostare l' `status` argomento su `"Disabled"` per creare una pianificazione inattiva. Infine, `continue_on_step_failure` consente di passare un valore booleano che eseguirà l'override del comportamento di errore predefinito della pipeline.

## <a name="view-your-scheduled-pipelines"></a>Visualizzare le pipeline pianificate

Nella Web browser passare a Azure Machine Learning. Dalla sezione **endpoint** del pannello di navigazione scegliere **endpoint della pipeline**. Verrà quindi inviato un elenco delle pipeline pubblicate nell'area di lavoro.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="Pagina pipeline di AML":::

In questa pagina è possibile visualizzare informazioni di riepilogo su tutte le pipeline nell'area di lavoro: nomi, descrizioni, stato e così via. Eseguire il drill-through facendo clic sulla pipeline. Nella pagina risultante sono disponibili altri dettagli sulla pipeline ed è possibile eseguire il drill-down nelle singole esecuzioni.

## <a name="deactivate-the-pipeline"></a>Disattiva la pipeline

Se si dispone di un `Pipeline` pubblicato ma non pianificato, è possibile disabilitarlo con:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se la pipeline è pianificata, è necessario annullare prima la pianificazione. Recuperare l'identificatore della pianificazione dal portale oppure eseguendo:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Quando si vuole `schedule_id` disabilitare, eseguire:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se quindi si esegue `Schedule.list(ws)` nuovamente, si otterrà un elenco vuoto.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Usare app per la logica di Azure per trigger complessi 

È possibile creare regole o comportamenti di trigger più complessi usando un'app per la [logica di Azure](../logic-apps/logic-apps-overview.md).

Per usare un'app per la logica di Azure per attivare una pipeline di Machine Learning, è necessario l'endpoint REST per una pipeline di Machine Learning pubblicata. [Creare e pubblicare la pipeline](./how-to-create-machine-learning-pipelines.md). Individuare quindi l'endpoint REST del `PublishedPipeline` usando l'ID pipeline:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Creare ora un'istanza di app per la [logica di Azure](../logic-apps/logic-apps-overview.md) . Se si vuole, [usare un ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [configurare una chiave gestita dal cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) per l'uso da parte dell'app per la logica.

Una volta eseguito il provisioning dell'app per la logica, seguire questa procedura per configurare un trigger per la pipeline:

1. [Creare un'identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) per concedere all'app l'accesso all'area di lavoro di Azure Machine Learning.

1. Passare alla visualizzazione progettazione app per la logica e selezionare il modello app per la logica vuota. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Modello Vuoto":::

1. Nella finestra di progettazione cercare **BLOB**. Selezionare il trigger **quando un BLOB viene aggiunto o modificato (solo proprietà)** e aggiungere questo trigger all'app per la logica.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Aggiunta di trigger":::

1. Immettere le informazioni di connessione per l'account di archiviazione BLOB da monitorare per le aggiunte o le modifiche ai BLOB. Consente di selezionare il contenitore da monitorare. 
 
    Scegliere l' **intervallo** e la **frequenza** di polling per gli aggiornamenti che funzionano.  

    > [!NOTE]
    > Questo trigger monitorerà il contenitore selezionato ma non eseguirà il monitoraggio delle sottocartelle.

1. Aggiungere un'azione HTTP che verrà eseguita quando viene rilevato un BLOB nuovo o modificato. Selezionare **+ nuovo passaggio**, quindi cercare e selezionare l'azione http.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="Cerca azione HTTP":::

  Usare le impostazioni seguenti per configurare l'azione:

  | Impostazione | Valore | 
  |---|---|
  | Azione HTTP | POST |
  | URI |endpoint della pipeline pubblicata che è stato trovato come [prerequisito](#prerequisites) |
  | Modalità di autenticazione | Identità gestita |

1. Configurare la pianificazione per impostare il valore di qualsiasi [percorso di DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) è possibile:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    Usare l' `DataStoreName` aggiunta all'area di lavoro come [prerequisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="Impostazioni HTTP":::

1. Selezionare **Save (Salva** ). la pianificazione è ora pronta.

> [!IMPORTANT]
> Se si usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso alla pipeline, [impostare le autorizzazioni per lo scenario della pipeline (training o assegnazione del punteggio)](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Chiamare pipeline di machine learning da pipeline di Azure Data Factory

In una pipeline di Azure Data Factory, l'attività *Esegui pipeline Machine Learning* esegue una pipeline di Azure Machine Learning. È possibile trovare questa attività nella pagina di creazione Data Factory nella categoria *Machine Learning* :

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="Screenshot che mostra l'attività della pipeline ML nell'ambiente di creazione Azure Data Factory":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato l'SDK Azure Machine Learning per Python per pianificare una pipeline in due modi diversi. Una pianificazione si ripete in base al tempo di clock trascorso. L'altra pianificazione viene eseguita se un file viene modificato in un oggetto specificato `Datastore` o in una directory dell'archivio. Si è visto come usare il portale per esaminare la pipeline e le singole esecuzioni. Si è appreso come disabilitare una pianificazione in modo che l'esecuzione della pipeline venga arrestata. Infine, è stata creata un'app per la logica di Azure per attivare una pipeline. 

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)

* Altre informazioni sulle [pipeline](concept-ml-pipelines.md)
* Scopri di più sull' [esplorazione Azure Machine Learning con Jupyter](samples-notebooks.md)