---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 50596ac331ff47876af3fd0243f7ce8725135b6f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725319"
---
Iniziare a usare la libreria client di Visione personalizzata per Java per creare un modello di classificazione delle immagini. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usare questo esempio come modello per la creazione di un'applicazione di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si vuole creare un modello di classificazione ed eseguirne il training _senza_ scrivere codice, vedere invece le [istruzioni basate sul browser](../../getting-started-build-a-classifier.md).

Usare la libreria client di Visione personalizzata per Java per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Pubblicare l'iterazione corrente
* Testare l'endpoint di stima

[Documentazione di riferimento](/java/api/overview/azure/cognitiveservices/client/customvision) | Codice sorgente della libreria [(training)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(previsione)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Artefatto (Maven) [(training)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(previsione)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[Esempi](/samples/browse/?products=azure&terms=custom%20vision)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* La versione corrente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="creare una risorsa Visione personalizzata"  target="_blank">creare una risorsa Visione personalizzata </a> nel portale di Azure per creare la risorsa di training e di previsione e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

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

Individuare il file *build.gradle.kts* e aprirlo con un IDE o un editor di testo a scelta. Quindi copiare la configurazione di compilazione seguente. Questa configurazione definisce il progetto come applicazione Java il cui punto di ingresso è la classe **CustomVisionQuickstart**. Importa le librerie di Visione personalizzata.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Creare un file Java


Dalla directory di lavoro eseguire il comando seguente per creare una cartella di origine del progetto:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *CustomVisionQuickstart.java*. Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), che contiene gli esempi di codice di questo argomento.


Nella classe **CustomVisionQuickstart** dell'applicazione creare variabili per l'endpoint e le chiavi della risorsa.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Accedere al portale di Azure. Se le risorse Visione personalizzata create nella sezione **Prerequisiti** sono state distribuite correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. È possibile trovare le chiavi e l'endpoint nelle pagine della **chiave e dell'endpoint delle** risorse. È necessario ottenere le chiavi sia per le risorse di training che per le risorse di stima, insieme all'endpoint API per la risorsa di training.
>
> È possibile trovare il valore dell'ID risorsa di stima nella scheda **Proprietà** della risorsa, elencato come **ID sottoscrizione**.
> 
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo Sulla sicurezza di Servizi [cognitivi.](../../../cognitive-services-security.md)

Nel metodo **main** dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno definiti in un secondo momento.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls)]

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di Visione personalizzata per Java.

|Nome|Description|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Questa classe gestisce la creazione, il training e la pubblicazione dei modelli. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Questa classe gestisce l'esecuzione di query sui modelli per le previsioni di classificazione immagini.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Questa classe definisce una singola previsione in una singola immagine. Include le proprietà per l'ID e il nome dell'oggetto e un punteggio di attendibilità.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione personalizzata per Java:

* [Autenticare il client](#authenticate-the-client)
* [Creare un nuovo progetto Visione personalizzata](#create-a-new-custom-vision-project)
* [Aggiungere tag al progetto](#add-tags-to-the-project)
* [Caricare e contrassegnare le immagini](#upload-and-tag-images)
* [Eseguire il training del progetto](#train-the-project)
* [Pubblicare l'iterazione corrente](#publish-the-current-iteration)
* [Testare l'endpoint di stima](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticare il client

Nel metodo **principale**, creare un'istanza dei client di training e di previsione usando l'endpoint e le chiavi.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]


## <a name="create-a-custom-vision-project"></a>Creare un progetto di Visione personalizzata

T## Creare un nuovo progetto di Visione personalizzata

Il metodo successivo crea un progetto di classificazione immagini. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](../../get-started-build-detector.md) nella guida al portale Web, vedere gli overload del metodo [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="add-tags-to-your-project"></a>Aggiungere i tag al progetto

Questo metodo definisce i tag su cui si eseguirà il training del modello.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare prima di tutto le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) nel dispositivo locale.

> [!NOTE]
> È necessario un set più ampio di immagini per completare il training? Trove, un progetto Microsoft Garage, consente di raccogliere e acquistare set di immagini a scopo di training. Una volta raccolte le immagini, è possibile scaricarle e quindi importarle nel progetto di Visione personalizzata nel modo consueto. Per altre informazioni, visitare la [pagina di Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Il frammento di codice precedente usa due funzioni helper che recuperano le immagini come flussi di risorse e le caricano nel servizio. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Eseguire il training del progetto

Questo metodo crea la prima iterazione di training nel progetto. Esegue una query sul servizio fino al completamento del training.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

Questo metodo rende disponibile l'iterazione corrente del modello per l'esecuzione di query. È possibile usare il nome del modello come riferimento per inviare richieste di previsione. È necessario immettere il valore corrente per `predictionResourceId`. È possibile trovare l'ID della risorsa di previsione nella scheda **Panoramica** della risorsa nel portale di Azure, indicato come **ID sottoscrizione**.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Questo metodo carica l'immagine di test, esegue query sull'endpoint del modello e restituisce i dati di stima nella console.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Eseguire l'applicazione

È possibile compilare l'app con:

```console
gradle build
```

Eseguire l'applicazione con il comando `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di classificazione delle immagini nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
