---
title: Guida introduttiva alla libreria client multivariate Python del rilevamento anomalie
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316019"
---
Introduzione alla libreria client multivariata per il rilevatore di anomalie per Python. Seguire questi passaggi per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Le nuove API di rilevamento delle anomalie multivariate consentono agli sviluppatori di integrare in modo semplice l'intelligenza artificiale avanzata per il rilevamento di anomalie da gruppi di metriche, senza la necessità di informazioni di apprendimento automatico o dati con etichetta. Le dipendenze e le correlazioni tra segnali diversi vengono conteggiate automaticamente come fattori chiave. Ciò consente di proteggere in modo proattivo i sistemi complessi dagli errori.

Usare la libreria client multivariata del rilevatore di anomalie per Python per:

* Rilevare le anomalie a livello di sistema da un gruppo di serie temporali.
* Quando una singola serie temporale non comunica molto ed è necessario esaminare tutti i segnali per rilevare un problema.
* Predicativo la manutenzione di risorse fisiche costose con decine a centinaia di diversi tipi di sensori che misurano diversi aspetti dell'integrità del sistema.

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.x](https://www.python.org/)
* [Llibreria di analisi dei dati Pandas](https://pandas.pydata.org/)
* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie </a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

 Creare un nuovo file Python e importare le librerie seguenti.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Creare variabili per la chiave sotto forma di variabile di ambiente, percorso di un file di dati della serie temporale e località di Azure della sottoscrizione. Ad esempio: `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Python:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training del modello](#train-the-model)
* [Rilevare le anomalie](#detect-anomalies)
* [Esporta modello](#export-model)
* [Elimina modello](#delete-model)

## <a name="authenticate-the-client"></a>Autenticare il client

Per creare un'istanza di un nuovo client di rilevamento anomalie è necessario passare la chiave di sottoscrizione del rilevatore di anomalie e l'endpoint associato. Verrà inoltre stabilita un'origine dati.  

Per usare le API multivariate del rilevatore di anomalie, è necessario eseguire il training del modello prima di usare il rilevamento. I dati utilizzati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie temporali devono essere compresse in un unico file zip e caricate nell' [archiviazione BLOB di Azure](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Per impostazione predefinita, il nome del file verrà utilizzato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere nel file zip un meta.jsaggiuntivo sul file se si desidera che il nome della variabile sia diverso dal nome del file con estensione zip. Una volta generato l' [URL della firma di accesso condiviso (SAS) BLOB](../../../../storage/common/storage-sas-overview.md), è possibile usare l'URL del file zip per il training.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Eseguire il training del modello

Si eseguirà prima il training del modello, si verificherà lo stato del modello durante il training per determinare quando il training è completo e quindi si recupererà l'ID modello più recente, che sarà necessario quando si passa alla fase di rilevamento.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Rilevare le anomalie

Usare `detect_anomaly` e `get_dectection_result` per determinare se sono presenti anomalie all'interno dell'origine dati. Sarà necessario passare l'ID modello per il modello appena sottoposto a training.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Esporta modello

Se si desidera esportare un modello, utilizzare `export_model` e passare l'ID modello del modello che si desidera esportare:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Elimina modello

Per eliminare un modello `delete_multivariate_model` , utilizzare e passare l'ID modello del modello che si desidera eliminare:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Prima di eseguire l'applicazione, è necessario aggiungere codice per chiamare le funzioni appena create.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Eseguire l'applicazione con il comando `python` e il nome del file.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
