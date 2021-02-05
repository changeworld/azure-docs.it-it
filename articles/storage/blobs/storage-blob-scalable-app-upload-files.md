---
title: Caricare grandi quantità di dati casuali in parallelo in Archiviazione di Azure
description: Informazioni su come usare la libreria client di Archiviazione di Azure per caricare grandi quantità di dati casuali in parallelo in un account di archiviazione di Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584466"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Caricare grandi quantità di dati casuali in parallelo in Archiviazione di Azure

Questa è la seconda di una serie di esercitazioni. Questa esercitazione illustra come distribuire un'applicazione che carica grandi quantità di dati casuali in un account di archiviazione di Azure.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare la stringa di connessione
> * Compilare l'applicazione
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Microsoft Azure archiviazione BLOB fornisce un servizio scalabile per l'archiviazione dei dati. Per garantire le migliori prestazioni possibili dell'applicazione, è consigliabile comprendere il funzionamento dell'archivio BLOB. È importante conoscere i limiti dei BLOB di Azure. Per altre informazioni, vedere: [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../blobs/scalability-targets.md)

Un altro fattore potenzialmente importante nella progettazione di un'applicazione ad alte prestazioni che usa BLOB è la [denominazione delle partizioni](../blobs/storage-performance-checklist.md#partitioning). Per dimensioni dei blocchi maggiori o uguali a 4 MiB, vengono usati [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) e la denominazione delle partizioni non influirà sulle prestazioni. Per dimensioni dei blocchi inferiori a 4 MiB, Archiviazione di Azure usa uno schema di partizionamento basato su intervalli per la scalabilità e il bilanciamento del carico. Con questa configurazione, i file con prefissi o convenzioni di denominazione simili vengono inseriti nella stessa partizione. Questa logica include il nome del contenitore in cui i file vengono caricati. In questa esercitazione si usano file con nomi costituiti da GUID e contenuto generato in modo casuale, che vengono quindi caricati in cinque diversi contenitori con nomi casuali.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario aver completato l'esercitazione di archiviazione precedente: [Creare una macchina virtuale e un account di archiviazione per un'applicazione scalabile][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Creare una sessione remota nella macchina virtuale

Usare il comando seguente nel computer locale per creare una sessione Desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo publicIPAddress della macchina virtuale. Quando richiesto, immettere le credenziali usate durante la creazione della macchina virtuale.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Configurare la stringa di connessione

Nel portale di Azure passare all'account di archiviazione. Nell'account di archiviazione selezionare **Chiavi di accesso** in **Impostazioni**. Copiare la **stringa di connessione** dalla chiave primaria o secondaria. Accedere alla macchina virtuale creata nell'esercitazione precedente. Aprire il **prompt dei comandi** come amministratore ed eseguire il comando `setx` con l'opzione `/m`. Questo comando salva una variabile di ambiente di impostazione del computer. La variabile di ambiente non è disponibile finché non si ricarica il **prompt dei comandi**. Sostituire **\<storageConnectionString\>** nell'esempio seguente:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Al termine, aprire un altro **prompt dei comandi**, passare a `D:\git\storage-dotnet-perf-scale-app` e digitare `dotnet build` per compilare l'applicazione.

## <a name="run-the-application"></a>Eseguire l'applicazione

Accedere a `D:\git\storage-dotnet-perf-scale-app`.

Digitare `dotnet run` per eseguire l'applicazione. Alla prima esecuzione di `dotnet` viene popolata la cache pacchetti locale, per migliorare la velocità di ripristino e consentire l'accesso offline. Questo comando viene eseguito una sola volta e il completamento richiede fino a un minuto.

```console
dotnet run
```

L'applicazione crea cinque contenitori con nome casuale e inizia a caricare i file della directory di staging nell'account di archiviazione.

Il `UploadFilesAsync` metodo è illustrato nell'esempio seguente:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Il numero minimo e massimo di thread è impostato su 100 per garantire che sia consentito un numero elevato di connessioni simultanee.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```
Oltre alle impostazioni relative ai limiti per connessioni e threading, viene configurata la classe [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) per il metodo [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) in modo da usare il parallelismo e disabilitare la convalida dell'hash MD5. I file vengono caricati in blocchi di 100 MB. Questa configurazione offre prestazioni superiori, ma può risultare costosa se si usa una rete con prestazioni scarse perché in caso di errore viene eseguito un nuovo tentativo per l'intero blocco di 100 MB.

|Proprietà|Valore|Descrizione|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Questa impostazione suddivide il BLOB in blocchi durante il caricamento. Per prestazioni ottimali, questo valore deve essere pari a otto volte il numero di core. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Questa proprietà disabilita la verifica dell'hash MD5 del contenuto caricato. La disabilitazione della convalida MD5 produce un trasferimento più veloce. Non viene tuttavia confermata la validità o l'integrità dei file trasferiti.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Questa proprietà determina se un hash MD5 verrà calcolato e archiviato con il file.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Backoff di 2 secondi con un massimo di 10 nuovi tentativi |Determina i criteri di ripetizione delle richieste. In caso di errori di connessione vengono eseguiti nuovi tentativi. In questo esempio viene configurato un criterio [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) con un backoff di 2 secondi e un numero massimo di nuovi tentativi pari a 10. Questa impostazione è importante quando l'applicazione sta per raggiungere gli obiettivi di scalabilità di Archiviazione BLOB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../blobs/scalability-targets.md).  |

---

Di seguito è riportato un esempio di output troncato dell'applicazione in esecuzione in un sistema Windows.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>Convalidare le connessioni

Durante il caricamento dei file, è possibile verificare il numero di connessioni simultanee all'account di archiviazione. Aprire una finestra della console e digitare `netstat -a | find /c "blob:https"` . Questo comando Mostra il numero di connessioni attualmente aperte. Come si può notare dall'esempio seguente, sono state aperte 800 connessioni quando si caricano i file casuali nell'account di archiviazione. Questo valore cambia nel corso dell'esecuzione del caricamento. Caricando blocchi in parallelo, la quantità di tempo necessaria per trasferire il contenuto si riduce considerevolmente.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie si è appreso come caricare grandi quantità di dati casuali in parallelo in un account di archiviazione, ad esempio come:

> [!div class="checklist"]
> * Configurare la stringa di connessione
> * Compilare l'applicazione
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Passare alla terza parte della serie per scaricare grandi quantità di dati da un account di archiviazione.

> [!div class="nextstepaction"]
> [Scaricare grandi quantità di dati casuali da Archiviazione di Azure](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
