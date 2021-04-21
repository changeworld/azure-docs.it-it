---
title: Eseguire lo sviluppo per File di Azure con .NET | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi .NET che usano File di Azure per archiviare i dati.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817378"
---
# <a name="develop-for-azure-files-with-net"></a>Eseguire lo sviluppo per File di Azure con .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Informazioni di base sullo sviluppo di applicazioni .NET che usano File di Azure [per](storage-files-introduction.md) archiviare i dati. Questo articolo illustra come creare una semplice applicazione console per eseguire le operazioni seguenti con .NET e File di Azure:

- Ottenere il contenuto di un file.
- Impostare le dimensioni massime, o quota, per una condivisione file.
- Creare una firma di accesso condiviso per un file.
- Copiare un file in un altro file nello stesso account di archiviazione.
- Copiare un file in un BLOB nello stesso account di archiviazione.
- Creare uno snapshot di una condivisione file.
- Ripristinare un file da uno snapshot di condivisione.
- Usare Archiviazione di Azure metriche per la risoluzione dei problemi.

Per altre informazioni sui File di Azure, vedere [Informazioni File di Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Informazioni sulle API .NET

File di Azure offre due ampi approcci alle applicazioni client: Server Message Block (SMB) e REST. All'interno di .NET, `System.IO` le `Azure.Storage.Files.Shares` API e astrarre questi approcci.

API | Utilizzo | Note
----|-------------|------
[System.IO](/dotnet/api/system.io) | L'applicazione: <ul><li>Deve leggere/scrivere file usando SMB</li><li>È in esecuzione su un dispositivo che ha accesso tramite la porta 445 all'account File di Azure</li><li>Non deve gestire le impostazioni amministrative della condivisione file</li></ul> | L'I/O di file implementato con File di Azure su SMB è in genere lo stesso di I/O con qualsiasi condivisione file di rete o dispositivo di archiviazione locale. Per un'introduzione a una serie di funzionalità in .NET, inclusa l'I/O di file, vedere [l'esercitazione sull'applicazione](/dotnet/csharp/tutorials/console-teleprompter) console.
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | L'applicazione: <ul><li>Non è possibile accedere File di Azure usando SMB sulla porta 445 a causa di vincoli del firewall o dell'ISP</li><li>Richiede funzionalità amministrative, ad esempio la possibilità di impostare la quota di una condivisione file o di creare una firma di accesso condiviso</li></ul> | Questo articolo illustra l'uso di `Azure.Storage.Files.Shares` per l'I/O di file usando REST anziché SMB e la gestione della condivisione file.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Creare l'applicazione console e ottenere l'assembly

È possibile usare la libreria File di Azure client in qualsiasi tipo di app .NET. Queste app includono app cloud, Web, desktop e per dispositivi mobili di Azure. In questa guida viene creata un'applicazione console per semplicità.

In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2019. La procedura è simile per le altre versioni di Visual Studio.

1. Avviare Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** scegliere App console **(.NET Framework)** per C# e quindi selezionare **Avanti.**
1. In **Configura il nuovo progetto** immettere un nome per l'app e selezionare **Crea.**

Aggiungere tutti gli esempi di codice in questo articolo `Program` alla classe nel file *Program.cs.*

## <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari

Fare riferimento a questi pacchetti nel progetto:

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

- [Libreria principale di Azure per .NET:](https://www.nuget.org/packages/Azure.Core/)questo pacchetto è l'implementazione della pipeline client di Azure.
- [BLOB del servizio di archiviazione di Azure libreria client per .NET:](https://www.nuget.org/packages/Azure.Storage.Blobs/)questo pacchetto fornisce l'accesso a livello di codice alle risorse BLOB nell'account di archiviazione.
- [Archiviazione di Azure client files per .NET:](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)questo pacchetto consente l'accesso a livello di codice alle risorse file nell'account di archiviazione.
- [Libreria Gestione configurazione di sistema per .NET:](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)questo pacchetto fornisce una classe che archivia e recupera i valori in un file di configurazione.

È possibile usare NuGet per ottenere i pacchetti. Seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e **scegliere Gestisci pacchetti NuGet.**
1. In **Gestione pacchetti NuGet**, selezionare **Sfoglia**. Cercare e scegliere **Azure.Core** e quindi selezionare **Installa.**

   Questo passaggio installa il pacchetto e le relative dipendenze.

1. Cercare e installare i pacchetti seguenti:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

- [Archiviazione di Microsoft Azure libreria comune per .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)questo pacchetto fornisce l'accesso a livello di codice alle risorse comuni nell'account di archiviazione.
- [Archiviazione di Microsoft Azure libreria BLOB per .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)questo pacchetto fornisce l'accesso a livello di codice alle risorse BLOB nell'account di archiviazione.
- [Archiviazione di Microsoft Azure file per .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)questo pacchetto consente l'accesso a livello di codice alle risorse file nell'account di archiviazione.
- [Microsoft Azure Gestione configurazione libreria per .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, ovunque venga eseguita l'applicazione.

È possibile usare NuGet per ottenere i pacchetti. Seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e **scegliere Gestisci pacchetti NuGet**.
1. In **Gestione pacchetti NuGet**, selezionare **Sfoglia**. Cercare e scegliere **Microsoft.Azure.Storage.BLOB** e quindi selezionare **Installa**.

   Questo passaggio installa il pacchetto e le relative dipendenze.
1. Cercare e installare i pacchetti seguenti:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvare le credenziali dell'account di archiviazione nel file App.config archiviazione

Salvare quindi le credenziali nel file diApp.config *progetto.* In **Esplora soluzioni** fare doppio clic e modificare il file in modo che `App.config` sia simile all'esempio seguente.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Sostituire `myaccount` con il nome dell'account di archiviazione e con la chiave `mykey` dell'account di archiviazione.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Sostituire `myaccount` con il nome dell'account di archiviazione e con la chiave `StorageAccountKeyEndingIn==` dell'account di archiviazione.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> L'emulatore di archiviazione Azurite non supporta attualmente File di Azure. La stringa di connessione deve avere come destinazione un account di archiviazione di Azure nel cloud per funzionare con File di Azure.

## <a name="add-using-directives"></a>Aggiungere le direttive using

In **Esplora soluzioni** aprire il file *Program.cs* e aggiungere le direttive using seguenti all'inizio del file.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Accedere alla condivisione file a livello di programmazione

Nel file *Program.cs* aggiungere il codice seguente per accedere alla condivisione file a livello di codice.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Il metodo seguente crea una condivisione file, se non esiste già. Il metodo inizia creando un [oggetto ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) da una stringa di connessione. L'esempio tenta quindi di scaricare un file creato in precedenza. Chiamare questo metodo da `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Aggiungere quindi il contenuto seguente al metodo , dopo il codice illustrato `Main()` in precedenza, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e ne restituisce il contenuto.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Eseguire l'applicazione console per visualizzare l'output.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Impostare la dimensione massima per una condivisione file

A partire dalla versione 5.x della libreria client File di Azure, è possibile impostare la quota (dimensione massima) per una condivisione file. È anche possibile controllare la quantità di dati archiviata attualmente nella condivisione.

L'impostazione della quota per una condivisione limita le dimensioni totali dei file archiviati nella condivisione. Se le dimensioni totali dei file nella condivisione superano la quota, i client non possono aumentare le dimensioni dei file esistenti. I client non possono anche creare nuovi file, a meno che tali file non siano vuoti.

L'esempio seguente illustra come controllare l'uso corrente per una condivisione e come impostare la quota per la condivisione.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generare la firma di accesso condiviso per un file o una condivisione file

A partire dalla versione 5.x della libreria client di File di Azure, è possibile generare una firma di accesso condiviso per una condivisione file o per un singolo file.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Il metodo di esempio seguente restituisce una firma di accesso condiviso in un file nella condivisione specificata.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

È anche possibile creare criteri di accesso archiviati in una condivisione file per gestire le firme di accesso condiviso. È consigliabile creare criteri di accesso archiviati perché consentono di revocare la firma di accesso condiviso in caso di compromissione. Nell'esempio seguente vengono creati criteri di accesso archiviati in una condivisione. Nell'esempio vengono utilizzati i criteri per fornire i vincoli per una firma di accesso condiviso in un file nella condivisione.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Per altre informazioni sulla creazione e sull'uso delle firme di accesso condiviso, vedere Funzionamento di una [firma di accesso condiviso.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Copiare i file

A partire dalla versione 5.x della libreria client File di Azure, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file.

È anche possibile usare AzCopy per copiare un file in un altro o per copiare un BLOB in un file o al contrario. Vedere [Introduzione ad AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se si copia un BLOB in un file o un file in un BLOB, è necessario usare una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine, anche se la copia viene eseguita nello stesso account di archiviazione.

### <a name="copy-a-file-to-another-file"></a>Copiare un file in un altro file

Nell'esempio seguente viene copiato un file in un altro file nella stessa condivisione. È possibile usare [l'autenticazione con chiave](/rest/api/storageservices/authorize-with-shared-key) condivisa per eseguire la copia perché questa operazione copia i file all'interno dello stesso account di archiviazione.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Copiare un file in un BLOB

Nell'esempio seguente viene creato un file che viene copiato in un BLOB nello stesso account di archiviazione. L'esempio crea una firma di accesso condiviso per il file di origine, che il servizio usa per autorizzare l'accesso al file di origine durante l'operazione di copia.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

È possibile copiare un BLOB in un file nello stesso modo. Se l'oggetto di origine è un BLOB, creare una firma di accesso condiviso per autorizzare l'accesso al BLOB durante l'operazione di copia.

## <a name="share-snapshots"></a>Snapshot di condivisione

A partire dalla versione 8.5 della File di Azure client, è possibile creare uno snapshot di condivisione. nonché elencare, esplorare ed eliminare snapshot di condivisione. Dopo la creazione, gli snapshot di condivisione sono di sola lettura.

### <a name="create-share-snapshots"></a>Creare snapshot di condivisione

L'esempio seguente crea uno snapshot di condivisione file.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione

Nell'esempio seguente vengono elencati gli snapshot in una condivisione.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Elencare file e directory all'interno di snapshot di condivisione

Nell'esempio seguente vengono esplorati file e directory all'interno di snapshot di condivisione.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Ripristinare condivisioni file o file da snapshot di condivisione

La creazione di uno snapshot di una condivisione file consente di ripristinare singoli file o l'intera condivisione file.

È possibile ripristinare un file da uno snapshot di condivisione file eseguendo una query sugli snapshot di condivisione di una condivisione file. È quindi possibile recuperare un file che appartiene a uno snapshot di condivisione specifico. Usare tale versione per leggere o ripristinare direttamente il file.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Eliminare snapshot di condivisione

L'esempio seguente elimina uno snapshot di condivisione file.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Risolvere i File di Azure usando le metriche<a name="troubleshooting-azure-files-using-metrics"></a>

Analisi archiviazione di Azure supporta le metriche per File di Azure. Grazie ai dati di metrica, è possibile monitorare le richieste e diagnosticare i problemi.

È possibile abilitare le metriche per File di Azure dal [portale di Azure](https://portal.azure.com). È anche possibile abilitare le metriche a livello di codice chiamando l'operazione [Set File Service Properties](/rest/api/storageservices/set-file-service-properties) con l'API REST o una delle relative analogie nella libreria client File di Azure file.

L'esempio di codice seguente illustra come usare la libreria client .NET per abilitare le metriche per File di Azure.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Aggiungere prima di tutto `using` le direttive seguenti al file *Program.cs,* insieme a quelle aggiunte in precedenza:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Anche se BLOB di Azure, tabelle di Azure e code di Azure usano il tipo condiviso nello spazio dei nomi, File di Azure usa il proprio tipo, il tipo nello `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` spazio dei nomi `FileServiceProperties` `Microsoft.Azure.Storage.File.Protocol` . È tuttavia necessario fare riferimento a entrambi gli spazi dei nomi dal codice per la compilazione del codice seguente.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Se si verificano problemi, è possibile fare riferimento a Risolvere [File di Azure problemi in Windows.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui File di Azure, vedere le risorse seguenti:

### <a name="conceptual-articles-and-videos"></a>Articoli concettuali e video

- [File di Azure: un file system SMB nel cloud senza conflitti per Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Usare File di Azure con Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Supporto degli strumenti per Archiviazione file

- [Introduzione ad AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Risolvere i problemi di File di Azure in Windows](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Riferimento

- [API di Archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage)
- [API REST del servizio file](/rest/api/storageservices/File-Service-REST-API)