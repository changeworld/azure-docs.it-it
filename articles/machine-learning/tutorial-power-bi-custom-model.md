---
title: 'Esercitazione: Creare il modello predittivo con un notebook (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire un modello di Machine Learning usando il codice di un notebook di Jupyter. È possibile usare il modello per prevedere risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 1dfee56f90011d3c532767e136b383e4eb95c234
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814772"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-a-jupyter-notebook-part-1-of-2"></a>Esercitazione: Integrazione di Power BI: creare il modello predittivo con un notebook di Jupyter (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando il codice di un notebook di Jupyter. Nella parte 2 si userà il modello per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un notebook di Jupyter Notebook.
> * Creare un'istanza di calcolo di Azure Machine Learning.
> * Eseguire il training di un modello di regressione usando scikit-learn.
> * Distribuire il modello in un endpoint di assegnazione punteggi in tempo reale.

Esistono tre modi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione A: Eseguire il training e la distribuzione di modelli con i notebook.  Questa opzione offre un'esperienza di creazione code-first. Usa notebook di Jupyter ospitati nello studio di Azure Machine Learning. 

Tuttavia, è possibile usare una delle altre opzioni:

* [Opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione di Azure Machine Learning](tutorial-power-bi-designer-model.md). Questa esperienza di creazione a basso contenuto di codice usa un'interfaccia utente con trascinamento della selezione.
* [Opzione C: Eseguire il training e la distribuzione di modelli con Machine Learning automatizzato](tutorial-power-bi-automated-model.md). Questa esperienza di creazione senza codice automatizza completamente la preparazione dei dati e il training dei modelli.


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile creare una [versione di valutazione gratuita](https://aka.ms/AMLFree). 
- Un'area di lavoro di Azure Machine Learning. Se non si ha un'area di lavoro, vedere [Creare e gestire le aree di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conoscenze di base del linguaggio Python e dei flussi di lavoro di Machine Learning.

## <a name="create-a-notebook-and-compute"></a>Creare un notebook e un'istanza di calcolo

Nella home page dello [**studio di Azure Machine Learning**](https://ml.azure.com) selezionare **Crea nuovo** > **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Screenshot che mostra come creare un notebook.":::
 
Nella pagina **Crea un nuovo file**:

1. Assegnare un nome al notebook, ad esempio *my_model_notebook*.
1. Impostare **Tipo di file** su **Notebook**.
1. Selezionare **Crea**. 
 
Successivamente, per eseguire le celle di codice, creare un'istanza di calcolo e collegarla al notebook. Per iniziare, selezionare l'icona con il segno più nella parte superiore del notebook:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Screenshot che mostra come creare un'istanza di calcolo.":::

Nella pagina **Crea istanza di calcolo**:

1. Scegliere le dimensioni di una macchina virtuale CPU. Per questa esercitazione, è possibile scegliere **Standard_D11_v2**, con 2 core e 14 GB di RAM.
1. Selezionare **Avanti**. 
1. Nella pagina **Configura impostazioni** specificare un valore valido per **Nome dell'ambiente di calcolo**. I caratteri validi sono lettere maiuscole e minuscole, numeri e trattini (-).
1. Selezionare **Crea**.

Nel notebook si può notare che il cerchio intorno a **Calcolo** è diventato di colore ciano. Questo cambiamento di colore indica che è in corso la creazione dell'istanza di calcolo:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Screenshot che mostra la creazione di un'istanza di calcolo.":::

> [!NOTE]
> Il provisioning dell'istanza di calcolo può richiedere da 2 a 4 minuti.

Al termine del provisioning, è possibile usare il notebook per eseguire le celle di codice. Ad esempio, nella cella è possibile digitare il codice seguente:

```python
import numpy as np

np.sin(3)
```

Quindi premere MAIUSC+INVIO (o CTRL+INVIO oppure selezionare il pulsante **Riproduci** accanto alla cella). Dovrebbe venire visualizzato l'output seguente:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Screenshot che mostra l'output di una cella.":::

A questo punto è possibile creare un modello di Machine Learning.

## <a name="build-a-model-by-using-scikit-learn"></a>Creare un modello con scikit-learn

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Questo set di dati è disponibile nei [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importa dati

Per importare i dati, copiare il codice seguente e incollarlo in una nuova *cella di codice* del notebook.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Il dataframe `X_df` pandas contiene 10 variabili di input di base. Queste variabili includono età, sesso, indice di massa corporea, pressione sanguigna media e sei misurazioni del siero. Il dataframe `y_df` pandas è la variabile di destinazione. Contiene una misura quantitativa della progressione del diabete un anno dopo quello di riferimento. Il dataframe contiene 442 record.

### <a name="train-the-model"></a>Eseguire il training del modello

Creare una nuova *cella di codice* nel notebook. Copiare quindi il codice seguente e incollarlo nella cella. Questo frammento di codice crea un modello di regressione ridge e lo serializza usando il formato pickle di Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrare il modello

Oltre al contenuto del file del modello, il modello registrato memorizzerà i metadati. I metadati includono la descrizione del modello, i tag e le informazioni sul framework. 

Sono utili per la gestione e la distribuzione di modelli nell'area di lavoro. Usando i tag, ad esempio, è possibile categorizzare i modelli e applicarvi filtri quando si visualizzano nell'area di lavoro. Inoltre, contrassegnando questo modello con il framework scikit-learn, sarà più semplice distribuirlo come servizio Web.

Copiare il codice seguente e quindi incollarlo in una nuova *cella di codice* del notebook.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

È anche possibile visualizzare il modello nello studio di Azure Machine Learning. Nel menu a sinistra selezionare **Modelli**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Screenshot che mostra come visualizzare un modello.":::

### <a name="define-the-scoring-script"></a>Definire lo script di assegnazione dei punteggi

Quando si distribuisce un modello che verrà integrato in Power BI, è necessario definire uno *script di assegnazione dei punteggi* di Python e un ambiente personalizzato. Lo script di assegnazione dei punteggi contiene due funzioni:

- La funzione `init()` viene eseguita all'avvio del servizio. Carica il modello (che viene scaricato automaticamente dal registro modelli) e lo deserializza.
- La funzione `run(data)` viene eseguita quando una chiamata al servizio include i dati di input a cui assegnare punteggi. 

>[!NOTE]
> Questo articolo usa elementi Decorator Python per definire lo schema dei dati di input e di output. Questa configurazione è importante per l'integrazione in Power BI.

Copiare il codice seguente e incollarlo in una nuova *cella di codice* del notebook. Il frammento di codice seguente include una cella magic che scrive il codice in un file denominato *score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definire l'ambiente personalizzato

Definire quindi l'ambiente per assegnare punteggi al modello. Nell'ambiente definire i pacchetti di Python, ad esempio pandas e scikit-learn, richiesti dallo script di assegnazione dei punteggi (*score.py*).

Per definire l'ambiente, copiare il codice seguente e incollarlo in una nuova *cella di codice* del notebook.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire il modello, copiare il codice seguente e incollarlo in una nuova *cella di codice* del notebook:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> La distribuzione del servizio può richiedere da 2 a 4 minuti.

Se la distribuzione viene completata correttamente, verrà visualizzato l'output seguente:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

È anche possibile visualizzare il servizio nello studio di Azure Machine Learning. Nel menu a sinistra selezionare **Endpoint**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Screenshot che mostra come visualizzare il servizio.":::

È consigliabile testare il servizio Web per assicurarsi che funzioni come previsto. Per restituire il notebook, nel menu a sinistra dello studio di Azure Machine Learning selezionare **Notebook**. Quindi copiare il codice seguente e incollarlo in una nuova *cella di codice* del notebook.

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

L'output avrà un aspetto simile alla struttura JSON seguente: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare e distribuire un modello in modo che possa essere utilizzato da Power BI. Nella parte successiva viene descritto come utilizzare questo modello in un report di Power BI.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
