---
title: Scaricare grandi quantità di dati casuali da Archiviazione di Azure | Microsoft Docs
description: Informazioni su come usare Azure SDK per scaricare grandi quantità di dati casuali da un account di Archiviazione di Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584975"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Scaricare grandi quantità di dati casuali da Archiviazione di Azure

Questa è la terza di una serie di esercitazioni. In questa esercitazione viene illustrato come scaricare grandi quantità di dati da Archiviazione di Azure.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Aggiornare l'applicazione
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario aver completato la precedente esercitazione sull'archiviazione: [Caricare grandi quantità di dati casuali in parallelo con Archiviazione di Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Creare una sessione remota nella macchina virtuale

 Per creare una sessione desktop remota con la macchina virtuale, usare il comando seguente nel computer locale. Sostituire l'indirizzo IP con l'indirizzo publicIPAddress della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nell'esercitazione precedente sono solo stati caricati file nell'account di archiviazione. Aprire `D:\git\storage-dotnet-perf-scale-app\Program.cs` in un editor di testo. Sostituire il metodo `Main` con l'esempio seguente. Questo esempio aggiunge un commento all'attività di caricamento e rimuove il commento dall'attività di scaricamento e dall'attività di eliminazione del contenuto nell'account di archiviazione al completamento dell'operazione.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Dopo aver aggiornato l'applicazione, è necessario compilare di nuovo l'applicazione. Aprire un `Command Prompt` e passare a `D:\git\storage-dotnet-perf-scale-app`. Ricompilare l'applicazione eseguendo `dotnet build` come illustrato nell'esempio seguente:

```console
dotnet build
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Dopo aver ricompilato l'applicazione, eseguire l'applicazione con il codice aggiornato. Se non è già aperto, aprire un `Command Prompt` e passare a `D:\git\storage-dotnet-perf-scale-app`.

Digitare `dotnet run` per eseguire l'applicazione.

```console
dotnet run
```

L'attività `DownloadFilesAsync` viene visualizzata nell'esempio seguente:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

L'applicazione legge i contenitori nell'account di archiviazione specificato in **storageconnectionstring**. Scorre i BLOB usando il metodo [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) e li scarica nel computer locale usando il metodo [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

L'applicazione legge i contenitori nell'account di archiviazione specificato in **storageconnectionstring**. Scorre i BLOB 10 alla volta usando il metodo [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) nei contenitori e li scarica nel computer locale usando il metodo [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) .

Nella tabella seguente vengono illustrati i [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) definiti per ogni BLOB mentre vengono scaricati.

|Proprietà|Valore|Descrizione|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Questa proprietà disabilita la verifica dell'hash MD5 del contenuto caricato. La disabilitazione della convalida MD5 produce un trasferimento più veloce. Non viene tuttavia confermata la validità o l'integrità dei file trasferiti. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Questa proprietà determina se un hash MD5 viene calcolato e archiviato.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Convalidare le connessioni

Mentre vengono scaricati i file è possibile verificare il numero di connessioni simultanee all'account di archiviazione. Aprire una finestra della console e digitare `netstat -a | find /c "blob:https"` . Questo comando Mostra il numero di connessioni attualmente aperte. Come si può notare dall'esempio seguente, durante il download dei file dall'account di archiviazione sono state aperte oltre 280 connessioni.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Passaggi successivi

Nella terza parte della serie si è appreso come scaricare grandi quantità di dati da un account di archiviazione, tra cui:

> [!div class="checklist"]
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Passare alla quarta parte della serie per verificare la velocità effettiva e la metrica di latenza nel portale.

> [!div class="nextstepaction"]
> [Verificare metriche di velocità effettiva e latenza nel portale](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
