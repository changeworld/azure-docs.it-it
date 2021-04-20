---
title: Rilevamento anomalie avvio rapido sulla libreria client Java multivariata
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: f2e227b2a589955191a2e602495cf0ffbb3f6d8b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732200"
---
Introduzione alla libreria client Rilevamento anomalie multivariata per Java. Seguire questi passaggi per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Le nuove API di rilevamento anomalie multivariate consentono agli sviluppatori di integrare facilmente l'intelligenza artificiale avanzata per il rilevamento di anomalie da gruppi di metriche, senza la necessità di conoscere l'apprendimento automatico o assegnare etichette ai dati. Le dipendenze e le interrelazioni tra segnali diversi vengono conteggiate automaticamente come fattori chiave. Ciò consente di proteggere in modo proattivo i sistemi complessi da errori.

Usare la Rilevamento anomalie client multivariata per Java per:

* Rilevare anomalie a livello di sistema da un gruppo di serie tempori.
* Quando una serie temporale singola non indica molto ed è necessario esaminare tutti i segnali per rilevare un problema.
* Manutenzione predicativa di risorse fisiche costose con decine o centinaia di diversi tipi di sensori che misurano vari aspetti dell'integrità del sistema.

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pacchetto (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie </a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. Per altre informazioni sulla libreria client, vedere [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts*, che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

### <a name="install-the-client-library"></a>Installare la libreria client

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare questa configurazione della build. Assicurarsi di includere le dipendenze del progetto.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Creare un file Java

Creare una cartella per l'app di esempio. Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *MetricsAdvisorQuickstarts.Java.* Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Creare le variabili per l'endpoint e per la chiave di Azure della risorsa. Creare un'altra variabile per il file di dati di esempio.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Per usare il Rilevamento anomalie API multivariate, è necessario eseguire il training del modello prima di usare il rilevamento. I dati usati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie tempose devono essere compresse in un unico file ZIP e caricate [nell'archivio BLOB di Azure.](../../../../storage/blobs/storage-blobs-introduction.md) Per impostazione predefinita, il nome file verrà usato per rappresentare la variabile per la serie temporale. In alternativa, un meta.jsaggiuntivo sul file può essere incluso nel file ZIP se si vuole che il nome della variabile sia diverso dal nome del file ZIP. Dopo aver generato [l'URL di firma](../../../../storage/common/storage-sas-overview.md)di accesso condiviso del BLOB , è possibile usare l'URL del file ZIP per il training.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Node.js:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training di un modello](#train-a-model)
* [Rilevare le anomalie](#detect-anomalies)
* [Esportare il modello](#export-model)
* [Eliminare il modello](#delete-model)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare `anomalyDetectorClient` un'istanza di un oggetto con l'endpoint e le credenziali.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Eseguire il training di un modello

### <a name="construct-a-model-result-and-train-model"></a>Costruire un risultato del modello ed esere il training del modello

Prima di tutto è necessario costruire una richiesta di modello. Assicurarsi che l'ora di inizio e di fine sia allineata all'origine dati.

 Per usare Rilevamento anomalie API multivariate, è necessario eseguire il training del modello prima di usare il rilevamento. I dati usati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie tempore devono essere compresse in un unico file ZIP e caricate [nell'archivio BLOB di Azure.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Per impostazione predefinita, il nome file verrà usato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere un meta.jsaggiuntivo nel file ZIP se si vuole che il nome della variabile sia diverso dal nome del file ZIP. Dopo aver generato [l'URL della firma](../../../../storage/common/storage-sas-overview.md)di accesso condiviso blob, è possibile usare l'URL del file ZIP per il training.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Rilevare le anomalie

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Esportare il modello

Per esportare il modello con training, usare `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Eliminare un modello

Per eliminare un modello esistente disponibile per la risorsa corrente, usare la `deleteMultivariateModelWithResponse` funzione .

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

```console
gradle build
```
### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `run`:

```console
gradle run
```

## <a name="next-steps"></a>Passaggi successivi

* [Rilevamento anomalie procedure consigliate multivariate](../../concepts/best-practices-multivariate.md)
