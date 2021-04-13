---
title: Guida introduttiva alla libreria client JavaScript multivariate per il rilevamento anomalie
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316025"
---
Introduzione alla libreria client multivariata per il rilevatore di anomalie per JavaScript. Attenersi alla seguente procedura per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Le nuove API di rilevamento delle anomalie multivariate consentono agli sviluppatori di integrare in modo semplice l'intelligenza artificiale avanzata per il rilevamento di anomalie da gruppi di metriche, senza la necessità di informazioni di apprendimento automatico o dati con etichetta. Le dipendenze e le correlazioni tra segnali diversi vengono conteggiate automaticamente come fattori chiave. Ciò consente di proteggere in modo proattivo i sistemi complessi dagli errori.

Usare la libreria client multivariata del rilevatore di anomalie per JavaScript per:

* Rilevare le anomalie a livello di sistema da un gruppo di serie temporali.
* Quando una singola serie temporale non comunica molto ed è necessario esaminare tutti i segnali per rilevare un problema.
* Predicativo la manutenzione di risorse fisiche costose con decine a centinaia di diversi tipi di sensori che misurano diversi aspetti dell'integrità del sistema.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie </a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

Creare un file denominato `index.js` e importare le librerie seguenti:'
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Creare le variabili per l'endpoint e per la chiave di Azure della risorsa. Creare un'altra variabile per il file di dati di esempio.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Per usare le API multivariate del rilevatore di anomalie, è necessario eseguire il training del modello prima di usare il rilevamento. I dati utilizzati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie temporali devono essere compresse in un unico file zip e caricate nell' [archiviazione BLOB di Azure](../../../../storage/blobs/storage-blobs-introduction.md). Per impostazione predefinita, il nome del file verrà utilizzato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere nel file zip un meta.jsaggiuntivo sul file se si desidera che il nome della variabile sia diverso dal nome del file con estensione zip. Una volta generato l' [URL della firma di accesso condiviso (SAS) BLOB](../../../../storage/common/storage-sas-overview.md), è possibile usare l'URL del file zip per il training.

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM `ms-rest-azure` e `azure-ai-anomalydetector`. In questo argomento di avvio rapido viene usata anche la libreria csv-parse:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Node.js:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training di un modello](#train-a-model)
* [Rilevare le anomalie](#detect-anomalies)
* [Esporta modello](#export-model)
* [Elimina modello](#delete-model)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di un `AnomalyDetectorClient` oggetto con l'endpoint e le credenziali.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Eseguire il training di un modello

### <a name="construct-a-model-result"></a>Costruisci risultato modello

Prima di tutto è necessario creare una richiesta di modello. Assicurarsi che l'ora di inizio e di fine siano allineate con l'origine dati.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Eseguire il training di un nuovo modello

È necessario passare la richiesta del modello al metodo client del rilevatore di anomalie `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Per verificare se il training del modello è stato completato, è possibile tenere traccia dello stato del modello:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Rilevare le anomalie

Usare le `detectAnomaly` `getDectectionResult` funzioni e per determinare se sono presenti anomalie all'interno dell'origine dati.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Esporta modello

Per esportare il modello sottoposto a training, usare la `exportModel` funzione.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Elimina modello

Per eliminare un modello esistente disponibile per la risorsa corrente, utilizzare la `deleteMultivariateModel` funzione.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```

## <a name="next-steps"></a>Passaggi successivi

* [Procedure consigliate per il rilevatore di anomalie](../../concepts/best-practices-multivariate.md)
