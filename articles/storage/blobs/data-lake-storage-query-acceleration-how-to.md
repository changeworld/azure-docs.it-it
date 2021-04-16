---
title: Filtrare i dati usando l'accelerazione Azure Data Lake Storage query | Microsoft Docs
description: Usare l'accelerazione delle query per recuperare un subset di dati dall'account di archiviazione.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 58b8cdef604861342a6489ef4e57ff1d057cd3f4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377735"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrare i dati usando l'accelerazione Azure Data Lake Storage query

Questo articolo illustra come usare l'accelerazione delle query per recuperare un subset di dati dall'account di archiviazione. 

L'accelerazione delle query consente alle applicazioni e ai framework di analisi di ottimizzare notevolmente l'elaborazione dei dati recuperando solo i dati necessari per eseguire una determinata operazione. Per altre informazioni, vedere Accelerazione [Azure Data Lake Storage query](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Prerequisiti

- Per accedere ad Archiviazione di Azure è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un **account di archiviazione per utilizzo generico v2.** vedere [Creare un account di archiviazione](../common/storage-account-create.md).

- Scegliere una scheda per visualizzare i prerequisiti specifici dell'SDK.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Non applicabile

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) versione 8 o successiva

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Questo articolo presuppone che sia stato creato un progetto Java usando Apache Maven. Per un esempio di come creare un progetto usando Apache Maven, vedere [Configurazione di](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 o versione successiva.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Non sono necessari prerequisiti aggiuntivi per usare Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Abilitare l'accelerazione delle query

Per usare l'accelerazione delle query, è necessario registrare la funzionalità di accelerazione delle query con la sottoscrizione. Dopo aver verificato che la funzionalità è stata registrata, è necessario registrare il provider Archiviazione di Azure di risorse. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Passaggio 1: Registrare la funzionalità di accelerazione delle query

Per usare l'accelerazione delle query, è prima di tutto necessario registrare la funzionalità di accelerazione delle query con la sottoscrizione. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Aprire una finestra dei comandi di Windows PowerShell.

1. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

2. Se l'identità è associata a più sottoscrizioni, impostare la sottoscrizione attiva.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Registrare la funzionalità di accelerazione delle query [usando il comando Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Aprire [Azure Cloud Shell](../../cloud-shell/overview.md)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Se l'identità è associata a più sottoscrizioni, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Registrare la funzionalità di accelerazione delle query usando [il comando az feature register.](/cli/azure/feature#az-feature-register)

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Passaggio 2: Verificare che la funzionalità sia registrata

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che la registrazione sia stata completata, usare [il comando Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che la registrazione sia stata completata, usare il [comando az feature.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Passaggio 3: Registrare il provider Archiviazione di Azure risorse

Dopo l'approvazione della registrazione, è necessario registrare nuovamente il provider Archiviazione di Azure risorse. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Per registrare il provider di risorse, usare [il comando Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per registrare il provider di risorse, usare [il comando az provider register.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Configurare l'ambiente

### <a name="step-1-install-packages"></a>Passaggio 1: Installare i pacchetti 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installare il modulo Az versione 4.6.0 o successiva.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Per eseguire l'aggiornamento da una versione precedente di Az, eseguire il comando seguente:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Aprire un prompt dei comandi e modificare la directory ( `cd` ) nella cartella del progetto, ad esempio:

   ```console
   cd myProject
   ```

2. Installare la versione o successiva della libreria client di Archiviazione BLOB di Azure per il pacchetto `12.5.0-preview.6` .NET usando il `dotnet add package` comando . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Gli esempi visualizzati in questo articolo analizzano un file CSV usando la [libreria CsvHelper.](https://www.nuget.org/packages/CsvHelper/) Per usare tale libreria, usare il comando seguente.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Aprire il *pom.xml* del progetto in un editor di testo. Aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Installare la Azure Data Lake Storage client per Python usando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installare la libreria client di Data Lake per JavaScript aprendo una finestra del terminale e quindi digitando il comando seguente.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Passaggio 2: Aggiungere istruzioni

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Non applicabile

#### <a name="net"></a>[.NET](#tab/dotnet)

Aggiungere queste `using` istruzioni all'inizio del file di codice.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

L'accelerazione delle query recupera i dati in formato CSV e Json. Assicurarsi quindi di aggiungere istruzioni using per tutte le librerie di analisi CSV o Json che si sceglie di usare. Gli esempi visualizzati in questo articolo analizzano un file CSV usando la libreria [CsvHelper](https://www.nuget.org/packages/CsvHelper/) disponibile in NuGet. Queste istruzioni vengono quindi `using` sommate all'inizio del file di codice.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Per compilare gli esempi presentati in questo articolo, è necessario aggiungere anche `using` queste istruzioni.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Aggiungere queste `import` istruzioni all'inizio del file di codice.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Aggiungere queste istruzioni import all'inizio del file di codice.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Includere il `storage-blob` modulo inserendo questa istruzione all'inizio del file di codice. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

L'accelerazione delle query recupera i dati in formato CSV e Json. Assicurarsi quindi di aggiungere istruzioni per tutti i moduli di analisi CSV o Json che si sceglie di usare. Gli esempi visualizzati in questo articolo analizzano un file CSV usando il [modulo fast-csv.](https://www.npmjs.com/package/fast-csv) Di conseguenza, questa istruzione verrà aggiunta all'inizio del file di codice.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperare dati usando un filtro

È possibile usare SQL per specificare i predicati del filtro di riga e le proiezioni di colonna in una richiesta di accelerazione della query. Il codice seguente esegue una query su un file CSV nell'archiviazione e restituisce tutte le righe di dati in cui la terza colonna corrisponde al valore `Hemingway, Ernest` . 

- Nella query SQL la parola chiave viene usata per indicare il file su cui `BlobStorage` viene eseguita una query.

- I riferimenti di colonna vengono specificati `_N` come dove la prima colonna è `_1` . Se il file di origine contiene una riga di intestazione, è possibile fare riferimento alle colonne in base al nome specificato nella riga di intestazione. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Il metodo asincrono invia la query all'API di accelerazione della query e quindi trasmette i risultati all'applicazione `BlobQuickQueryClient.QueryAsync` come [oggetto Stream.](/dotnet/api/system.io.stream)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Il metodo invia la query all'API di accelerazione della query e quindi trasmette i risultati all'applicazione come oggetto che può essere letto come `BlobQuickQueryClient.openInputStream()` `InputStream` qualsiasi altro oggetto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Questo esempio invia la query all'API di accelerazione della query e quindi trasmette i risultati. `blob`L'oggetto passato alla funzione helper è di tipo `queryHemingway` [BlockBlobClient.](/javascript/api/@azure/storage-blob/blockblobclient) Per altre informazioni su come ottenere un oggetto [BlockBlobClient,](/javascript/api/@azure/storage-blob/blockblobclient) vedere Guida introduttiva: Gestire BLOB con [JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperare colonne specifiche

È possibile impostare l'ambito dei risultati su un subset di colonne. In questo modo si recuperano solo le colonne necessarie per eseguire un determinato calcolo. In questo modo si migliorano le prestazioni dell'applicazione e si riducono i costi perché i dati vengono trasferiti in rete. 

Questo codice recupera solo la `BibNum` colonna per tutti i libri nel set di dati. Usa inoltre le informazioni della riga di intestazione nel file di origine per fare riferimento alle colonne nella query.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Il codice seguente combina il filtro di riga e le proiezioni di colonna nella stessa query. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Azure Data Lake Storage accelerazione delle query](data-lake-storage-query-acceleration.md)
- [Informazioni di riferimento sul linguaggio SQL per l'accelerazione delle query](query-acceleration-sql-reference.md)
