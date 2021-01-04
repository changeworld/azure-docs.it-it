---
title: 'Esercitazione: Creare il modello predittivo con un notebook (parte 1 di 2)'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e distribuire un modello predittivo di Machine Learning con il codice di un notebook di Jupyter, in modo da poterlo usare per prevedere i risultati in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370727"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Esercitazione: Integrazione di Power BI: creare il modello predittivo con un notebook (parte 1 di 2)

Nella prima parte di questa esercitazione si eseguono il training e la distribuzione di un modello predittivo di Machine Learning usando il codice di un notebook di Jupyter. Nella parte 2 si userà quindi il modello per prevedere i risultati in Microsoft Power BI.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un notebook di Jupyter Notebook
> * Creare un'istanza di calcolo di Azure Machine Learning
> * Eseguire il training di un modello di regressione usando scikit-learn
> * Distribuire il modello in un endpoint di assegnazione punteggi in tempo reale

Esistono tre modi diversi per creare e distribuire il modello che verrà usato in Power BI.  Questo articolo illustra l'opzione A: Eseguire il training e la distribuzione di modelli con i notebook.  Questa opzione illustra un'esperienza di creazione code-first tramite notebook di Jupyter ospitati nello studio di Azure Machine Learning. 

In alternativa è possibile scegliere:

* [Opzione B: Eseguire il training e la distribuzione di modelli con la finestra di progettazione](tutorial-power-bi-designer-model.md), un'esperienza di creazione a basso contenuto di codice usando la finestra di progettazione, un'interfaccia utente con trascinamento della selezione.
* [Opzione C: Eseguire il training e la distribuzione di modelli usando ML automatizzato](tutorial-power-bi-automated-model.md), un'esperienza di creazione senza codice che automatizza completamente la preparazione dei dati e il training dei modelli.


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure ([è disponibile una versione di valutazione gratuita](https://aka.ms/AMLFree)). 
- Un'area di lavoro di Azure Machine Learning. Se non è già disponibile un'area di lavoro, vedere l'articolo su [come creare un'area di lavoro di Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conoscenze di base del linguaggio Python e dei flussi di lavoro di Machine Learning.

## <a name="create-a-notebook-and-compute"></a>Creare un notebook e un'istanza di calcolo

Nella home page dello [studio di Azure Machine Learning](https://ml.azure.com) selezionare **Crea nuovo** e quindi **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Screenshot che illustra come creare un notebook":::
 
Viene visualizzata la finestra di dialogo **Crea un nuovo file**:

1. Immettere un nome file per il notebook, ad esempio `my_model_notebook`
1. Impostare **Tipo di file** su **Notebook**

Selezionare **Crea**. Successivamente, è necessario creare un'istanza di calcolo e collegarla al notebook per eseguire le celle di codice. A tale scopo, selezionare l'icona con il segno più nella parte superiore del notebook:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Screenshot che mostra come creare un'istanza di calcolo":::

Quindi, nella pagina **Crea istanza di calcolo**:

1. Scegliere le dimensioni della macchina virtuale CPU. Ai fini di questa esercitazione, è sufficiente scegliere **Standard_D11_v2** (due core, 14 GB di RAM).
1. Selezionare **Avanti**. 
1. Nella pagina **Configura impostazioni** specificare un nome valido in **Nome dell'ambiente di calcolo** (i caratteri validi sono lettere maiuscole e minuscole, numeri e il trattino -).
1. Selezionare **Crea**.

Nel notebook si noterà che il cerchio accanto a **Calcolo** è diventato di colore ciano, a indicare che è in corso la creazione dell'istanza di calcolo:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Screenshot che mostra la creazione dell'istanza di calcolo":::

> [!NOTE]
> Il provisioning dell'istanza di calcolo può richiedere circa 2-4 minuti.

Una volta effettuato il provisioning dell'istanza di calcolo, è possibile usare il notebook per eseguire le celle di codice. Ad esempio, nella cella digitare:

```python
import numpy as np

np.sin(3)
```

Seguito da **MAIUSC+INVIO** (o **CTRL+INVIO** oppure selezionare il pulsante Riproduci accanto alla cella). Viene visualizzato l'output seguente:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Screenshot che mostra l'esecuzione della cella":::

A questo punto è possibile creare un modello di Machine Learning.

## <a name="build-a-model-using-scikit-learn"></a>Creare un modello con scikit-learn

In questa esercitazione si usa il [set di dati Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), disponibile nei [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Importa dati

Per importare i dati, copiare e incollare il codice seguente in una nuova **cella di codice** del notebook:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Il dataframe `X_df` pandas contiene 10 variabili di input di base, ad esempio età, sesso, indice di massa corporea, pressione sanguigna media e sei misurazioni del siero. Il dataframe `y_df` pandas è la variabile di destinazione che contiene una misura quantitativa della progressione della malattia un anno dopo quello di riferimento. Sono presenti 442 record in totale.

### <a name="train-model"></a>Eseguire il training del modello

Creare una nuova **cella di codice** nel notebook e copiare e incollare il frammento di codice seguente, che crea un modello di regressione ridge e lo serializza con il formato pickle di Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrare il modello

Oltre al contenuto del file stesso, il modello registrato archivia anche i metadati, ovvero descrizione del modello, tag e informazioni sul framework, che risulteranno utili per la gestione e la distribuzione di modelli nell'area di lavoro. Usando i tag, ad esempio, è possibile categorizzare i modelli e applicarvi filtri quando si visualizzano nell'area di lavoro. Inoltre, contrassegnando questo modello con il framework scikit-learn, sarà più semplice distribuirlo come servizio Web, come si vedrà più avanti.

Copiare e incollare i codice seguente in una nuova **cella di codice** del notebook:

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

È anche possibile visualizzare il modello nello studio di Azure Machine Learning passando a **Endpoint** nel menu a sinistra:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Screenshot che mostra il modello":::

### <a name="define-the-scoring-script"></a>Definire lo script di assegnazione dei punteggi

Quando si distribuisce un modello per integrarlo in Microsoft Power BI, è necessario definire uno *script di assegnazione dei punteggi* di Python e un ambiente personalizzato. Lo script di assegnazione dei punteggi contiene due funzioni:

- `init()`: questa funzione viene eseguita dopo l'avvio del servizio. Carica il modello (si noti che il modello viene scaricato automaticamente dal registro modelli) e lo deserializza.
- `run(data)`: questa funzione viene eseguita quando viene effettuata una chiamata al servizio con alcuni dati di input per cui è necessario assegnare punteggi. 

>[!NOTE]
> Si usano gli elementi Decorator di Python per definire lo schema dei dati di input e di output, il che è importante per la corretta integrazione di Microsoft Power BI.

Copiare e incollare il codice seguente in una nuova **cella di codice** del notebook. Il frammento di codice seguente include una cella magic che scriverà il codice in un file denominato score.py.

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definire l'ambiente personalizzato

Successivamente, è necessario definire l'ambiente per assegnare punteggi al modello. In questo ambiente è necessario definire i pacchetti Python richiesti dallo script di assegnazione dei punteggi (score.py) definito in precedenza, ad esempio pandas, scikit-learn e così via.

Per definire l'ambiente, copiare e incollare il codice seguente in una nuova **cella di codice** del notebook:

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

Per distribuire il modello, copiare e incollare il codice seguente in una nuova **cella di codice** del notebook:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> La distribuzione del modello può richiedere 2-4 minuti.

Dopo la corretta distribuzione di un servizio, verrà visualizzato l'output seguente:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

È anche possibile visualizzare il servizio nello studio di Azure Machine Learning passando a **Endpoint** nel menu a sinistra:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Screenshot che mostra l'endpoint":::

È consigliabile testare il servizio Web per assicurarsi che funzioni come previsto. Tornare nel notebook selezionando **Notebook** nel menu a sinistra nello studio di Azure Machine Learning. Copiare e incollare il codice seguente in una nuova **cella di codice** del notebook per testare il servizio:

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

In questa esercitazione è stato illustrato come creare e distribuire un modello in modo che possa essere utilizzato da Microsoft Power BI. Nella parte successiva viene descritto come utilizzare questo modello in un report di Power BI.

> [!div class="nextstepaction"]
> [Esercitazione: Utilizzare il modello in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
