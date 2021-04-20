---
title: Rilevamento anomalie guida introduttiva alla libreria client multivariata .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: b3acea520859de10825468a4d37c3030f9b862bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732582"
---
Introduzione alla libreria Rilevamento anomalie client multivariate per .NET. Seguire questa procedura per installare il pacchetto e iniziare a usare gli algoritmi forniti dal servizio. Le nuove API di rilevamento anomalie multivariate consentono agli sviluppatori di integrare facilmente l'intelligenza artificiale avanzata per rilevare anomalie da gruppi di metriche, senza la necessità di conoscenze di Machine Learning o dati etichettati. Le dipendenze e le interrelazioni tra segnali diversi vengono conteggiate automaticamente come fattori chiave. Ciò consente di proteggere in modo proattivo i sistemi complessi da errori.

Usare la Rilevamento anomalie client multivariata per .NET per:

* Rilevare anomalie a livello di sistema da un gruppo di serie tempore.
* Quando una serie temporale singola non indica molto ed è necessario esaminare tutti i segnali per rilevare un problema.
* Manutenzione predicativa di risorse fisiche costose con decine o centinaia di diversi tipi di sensori che misurano vari aspetti dell'integrità del sistema.

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie </a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita, quindi selezionare pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. Incollare la chiave e l'endpoint nel codice seguente più avanti nella guida introduttiva.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-net-core-application"></a>Creare una nuova applicazione .NET Core

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `anomaly-detector-quickstart-multivariate`. Questo comando crea un semplice progetto "Hello World" con un singolo file di origine C#: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```dotnetcli
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client

All'interno della directory dell'applicazione, installare la libreria client di Rilevamento anomalie per .NET con il comando seguente:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

Dalla directory del progetto aprire il file *program.cs* e aggiungere quanto segue usando `directives`:

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

Nel metodo dell'applicazione creare variabili per l'endpoint di Azure della risorsa, la chiave `main()` API e un'origine dati personalizzata.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Per usare Rilevamento anomalie API multivariate, è necessario eseguire il training del modello prima di usare il rilevamento. I dati usati per il training sono un batch di serie temporali, ogni serie temporale deve essere in formato CSV con due colonne, timestamp e valore. Tutte le serie tempore devono essere compresse in un unico file ZIP e caricate in [Archiviazione BLOB di Azure.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Per impostazione predefinita, il nome file verrà usato per rappresentare la variabile per la serie temporale. In alternativa, è possibile includere un meta.jsaggiuntivo nel file ZIP se si vuole che il nome della variabile sia diverso dal nome del file ZIP. Dopo aver generato un URL di firma di accesso condiviso [blob,](../../../../storage/common/storage-sas-overview.md)è possibile usare l'URL del file ZIP per il training.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice illustrano come eseguire le operazioni seguenti con la Rilevamento anomalie client multivariata per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training del modello](#train-the-model)
* [Rilevare le anomalie](#detect-anomalies)
* [Esportare il modello](#export-model)
* [Eliminare il modello](#delete-model)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'Rilevamento anomalie client con l'endpoint e la chiave.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Eseguire il training del modello

Creare una nuova attività asincrona privata come indicato di seguito per gestire il training del modello. Si userà `TrainMultivariateModel` per eseguire il training del modello e per verificare quando il training è stato `GetMultivariateModelAysnc` completato.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Rilevare le anomalie

Per rilevare le anomalie usando il modello appena con training, creare un `private async Task` denominato `detectAsync` . Si creerà un nuovo `DetectionRequest` oggetto e lo si passerà come parametro a `DetectAnomalyAsync` .

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Esportare il modello

Per esportare il modello di cui è stato precedentemente training, creare un `private async Task` denominato `exportAysnc` . Si userà `ExportModelAsync` e si passerà l'ID modello del modello da esportare.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Response model_response = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        Stream model;
        if (model_response.ContentStream != null)
        {
            model = model_response.ContentStream;
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Eliminare il modello

Per eliminare un modello creato in precedenza, usare e passare l'ID modello `DeleteMultivariateModelAsync` del modello da eliminare. Per recuperare un ID modello, è possibile `getModelNumberAsync` :

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Metodo Main

Ora che sono disponibili tutte le parti del componente, è necessario aggiungere altro codice al metodo main per chiamare le attività appena create.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Passaggi successivi

* [Rilevamento anomalie procedure consigliate multivariate](../../concepts/best-practices-multivariate.md)
