---
title: Sviluppare funzioni di Azure con servizi multimediali V3
description: Questo argomento illustra come iniziare a sviluppare funzioni di Azure con servizi multimediali v3 usando il portale di Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 389ad34bb856675dfabd761507ed07cc722c032a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281967"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Sviluppare funzioni di Azure con servizi multimediali V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In questo articolo viene illustrato come iniziare a creare le Funzioni di Azure che usano i Servizi multimediali. La funzione di Azure definita in questo articolo consente di monitorare un contenitore di account di archiviazione denominato **input** per i nuovi file MP4. Una volta rilasciato un file nel contenitore di archiviazione, il trigger BLOB esegue la funzione. Per rivedere funzioni di Azure, vedere  [Panoramica](../../azure-functions/functions-overview.md) e altri argomenti nella sezione **funzioni di Azure** .

Se si vuole esplorare e distribuire le Funzioni di Azure esistenti che usano i Servizi multimediali di Azure, estrarre [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration) (Funzioni di Azure di Servizi multimediali). Questo repository contiene esempi che usano Servizi multimediali per visualizzare i flussi di lavoro correlati all'inserimento di contenuto direttamente dall'archiviazione BLOB, alla codifica e alla scrittura del contenuto nell'archiviazione BLOB.

## <a name="prerequisites"></a>Prerequisiti

- Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).
- Se si intende creare le Funzioni di Azure per eseguire azioni sull'account dei Servizi multimediali di Azure o ascoltare gli eventi inviati dai Servizi multimediali, è necessario creare un account AMS, come descritto [qui](account-create-how-to.md).

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

1. Passare al [portale di Azure](https://portal.azure.com) e accedere con il proprio account Azure.
2. Creare un'app per le funzioni come descritto [qui](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Un account di archiviazione specificato deve trovarsi nella stessa area dell'app.

## <a name="configure-function-app-settings"></a>Configurare le impostazioni dell'app per le funzioni

Quando si sviluppano le funzioni di Servizi multimediali, è utile aggiungere variabili di ambiente che verranno usati nelle funzioni. Per configurare le impostazioni dell'app, fare clic sul collegamento Configurare le impostazioni dell'app. Per altre informazioni, vedere [Come configurare le impostazioni dell'app per le funzioni di Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-a-function"></a>Creare una funzione

In seguito alla distribuzione dell'app per le funzioni, questa verrà visualizzata tra le Funzioni di Azure dei **Servizi app**.

1. Selezionare l'app per le funzioni e fare clic su **Nuova funzione**.
1. Scegliere il linguaggio **C#** e lo scenario **Elaborazione dati**.
1. Scegliere il modello **BlobTrigger**. Questa funzione è attivata ogni volta che viene caricato un BLOB nel contenitore di **input**. Il nome **input** è specificato nel **percorso**, nel passaggio successivo.
1. Dopo aver selezionato **BlobTrigger**, altri controlli verranno visualizzati nella pagina.
1. Fare clic su **Crea**.

## <a name="files"></a>File

La funzione di Azure viene associata ai file di codice e agli altri file descritti in questa sezione. Quando si usa il portale di Azure per creare una funzione, **function.json** e **run.csx** vengono creati automaticamente. Sarà necessario aggiungere o caricare un file **project.json**. Il resto di questa sezione fornisce una breve spiegazione di ogni file e mostra le relative definizioni.

### <a name="functionjson"></a>function.json

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. Per altre informazioni, vedere [binding HTTP e webhook di funzioni di Azure](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Impostare la proprietà **disattivato** su **vero** per impedire l'esecuzione della funzione.

Sostituire il contenuto del file .json della funzione esistente con il codice seguente:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Il file project.json contiene dipendenze. Di seguito è riportato un esempio di **project.jssu** file che include i pacchetti di servizi multimediali di Azure per .NET richiesti da NuGet. Si noti che i numeri di versione cambiano con gli aggiornamenti più recenti per i pacchetti, pertanto è consigliabile confermare le versioni più recenti.

Aggiungere la seguente definizione .json.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```

### <a name="runcsx"></a>run.csx

Questo è il codice C# per la funzione.  La funzione definita di seguito monitora un contenitore dell'account di archiviazione denominato **input** (cioè quello specificato nel percorso) per i nuovi file MP4. Una volta rilasciato un file nel contenitore di archiviazione, il trigger BLOB esegue la funzione.

Nell'esempio definito in questa sezione viene illustrato quanto segue:

1. Come inserire un asset in un account di servizi multimediali (eseguendo il coping di un BLOB in un asset AMS)
2. Come inviare un processo di codifica che usa il set di impostazioni "flusso adattivo" di Media Encoder Standard

Sostituire il contenuto del file run.csx esistente con il codice seguente: Al termine dell'operazione di definizione della funzione fare clic su **Salva ed esegui**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testare la funzione

Per testare la funzione, è necessario caricare un file MP4 nel contenitore **input** dell'account di archiviazione specificato nella stringa di connessione.  

1. Selezionare l'account di archiviazione specificato.
2. Fare clic su **BLOB**.
3. Fare clic su **+ Contenitore**. Denominare il contenitore **input**.
4. Premere **Carica** e passare al file MP4 che si desidera caricare.

>[!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per altre informazioni, vedere [Trigger e associazioni di archiviazione BLOB](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali.

Per altri dettagli ed esempi o soluzioni complete di uso di funzioni di Azure e app per la logica con servizi multimediali di Azure per creare flussi di lavoro di creazione di contenuto personalizzati, vedere [funzioni di Azure di servizi multimediali](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)
