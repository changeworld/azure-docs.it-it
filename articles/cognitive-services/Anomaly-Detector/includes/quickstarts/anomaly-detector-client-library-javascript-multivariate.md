---
title: guida introduttiva alla libreria client JavaScript multivariata Rilevamento anomalie
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 261dbb7cab2ac17a39777241d24e2c73cf550873
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800003"
---
Introduzione alla libreria Rilevamento anomalie client multivariate per JavaScript. Seguire questa procedura per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Le nuove API di rilevamento anomalie multivariate consentono agli sviluppatori di integrare facilmente l'intelligenza artificiale avanzata per rilevare anomalie da gruppi di metriche, senza la necessità di conoscenze di Machine Learning o dati etichettati. Le dipendenze e le interrelazioni tra segnali diversi vengono conteggiate automaticamente come fattori chiave. Ciò consente di proteggere in modo proattivo i sistemi complessi da errori.

Usare la Rilevamento anomalie client multivariata per JavaScript per:

* Rilevare anomalie a livello di sistema da un gruppo di serie tempore.
* Quando una serie temporale singola non indica molto ed è necessario esaminare tutti i segnali per rilevare un problema.
* Manutenzione predicativa di risorse fisiche costose con decine o centinaia di diversi tipi di sensori che misurano vari aspetti dell'integrità del sistema.

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Pacchetto (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)

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

Creare un file denominato `index.js` e importare le librerie seguenti: '
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

 Per usare Rilevamento anomalie API multivariate, è necessario eseguire il training del modello prima di usare il rilevamento. I dati usati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie tempore devono essere compresse in un unico file ZIP e caricate [nell'archivio BLOB di Azure.](../../../../storage/blobs/storage-blobs-introduction.md) Per impostazione predefinita, il nome file verrà usato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere un meta.jsaggiuntivo nel file ZIP se si vuole che il nome della variabile sia diverso dal nome del file ZIP. Dopo aver generato [l'URL](../../../../storage/common/storage-sas-overview.md)della firma di accesso condiviso blob, è possibile usare l'URL del file ZIP per il training.

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM `ms-rest-azure` e `azure-ai-anomalydetector`. In questo argomento di avvio rapido viene usata anche la libreria csv-parse:

```console
npm install @azure/ai-anomaly-detector csv-parse
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Node.js:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training di un modello](#train-a-model)
* [Rilevare le anomalie](#detect-anomalies)
* [Esportare il modello](#export-model)
* [Eliminare il modello](#delete-model)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare `AnomalyDetectorClient` un'istanza di un oggetto con l'endpoint e le credenziali.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Eseguire il training di un modello

### <a name="construct-a-model-result"></a>Costruire un risultato del modello

Prima di tutto è necessario costruire una richiesta di modello. Assicurarsi che l'ora di inizio e di fine sia allineata con l'origine dati.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Training di un nuovo modello

Sarà necessario passare la richiesta del modello al metodo Rilevamento anomalie `trainMultivariateModel` client.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Per verificare se il training del modello è stato completato, è possibile tenere traccia dello stato del modello:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Rilevare le anomalie

Usare le `detectAnomaly` funzioni `getDectectionResult` e per determinare se sono presenti anomalie all'interno dell'origine dati.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Esportare il modello

Per esportare il modello con training, usare la `exportModel` funzione .

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Eliminare il modello

Per eliminare un modello esistente disponibile per la risorsa corrente, usare la `deleteMultivariateModel` funzione .

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

* [Rilevamento anomalie procedure consigliate multivariate](../../concepts/best-practices-multivariate.md)
