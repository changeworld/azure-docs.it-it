---
title: 'Guida introduttiva: libreria di archiviazione BLOB di Azure V12-Python'
description: Questa Guida introduttiva illustra come usare la libreria client di archiviazione BLOB di Azure versione 12 per Python per creare un contenitore e un BLOB nell'archiviazione BLOB (oggetto). Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096407"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Avvio rapido: Gestire i BLOB con Python v12 SDK

In questo argomento di avvio rapido viene illustrato come gestire i BLOB con Python. I BLOB sono oggetti che possono contenere grandi quantità di dati di testo o binari, tra cui immagini, documenti, flussi multimediali e dati di archiviazione. Verranno caricati, scaricati ed elencati i BLOB e verranno creati ed eliminati i contenitori.

Altre risorse:

* [Documentazione di riferimento delle API](/python/api/azure-storage-blob)
* [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pacchetto (Python Package Index)](https://pypi.org/project/azure-storage-blob/)
* [Esempi](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un account dell'Archiviazione di Azure. [Creare un account di archiviazione](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurazione

Questa sezione illustra la preparazione di un progetto per l'uso con la libreria client di archiviazione BLOB di Azure V12 per Python.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione Python denominata *blob-quickstart-v12*.

1. In una finestra della console, ad esempio cmd, PowerShell o Bash, creare una nuova directory per il progetto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Passare alla directory *blob-quickstart-v12* appena creata.

    ```console
    cd blob-quickstart-v12
    ```

1. Nella directory *blob-quickstart-v12* creare un'altra directory denominata *data*. Questa directory è la posizione in cui verranno creati e archiviati i file di dati BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installare il pacchetto

Nella directory dell'applicazione installare la libreria client di archiviazione BLOB di Azure per il pacchetto python usando il `pip install` comando.

```console
pip install azure-storage-blob
```

Questo comando installa la libreria client di archiviazione BLOB di Azure per il pacchetto Python e tutte le librerie da cui dipende. In questo caso, si tratta solo della libreria principale di Azure per Python.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un nuovo file di testo nell'editor del codice
1. Aggiungere le istruzioni `import`
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

    Ecco il codice:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Salvare il nuovo file come *blob-quickstart-v12.py* nella directory *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Il servizio Archiviazione BLOB di Azure è ottimizzato per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blobs-introduction/blob1.png)

Per interagire con queste risorse, usare le classi Python seguenti:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): la classe `ContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le attività seguenti con la libreria client di archiviazione BLOB di Azure per Python:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione dell'account di archiviazione dalla variabile di ambiente creata nella sezione [configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string) .

Aggiungere questo codice all'interno del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore UUID al nome del contenitore per assicurarsi che sia univoco.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chiamando il metodo [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Infine, chiamare il metodo [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) per creare effettivamente il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Crea una directory locale in cui conservare i file di dati.
1. Creare un file di testo nella directory locale.
1. Ottenere un riferimento a un oggetto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chiamando il metodo [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) su [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare il file di testo locale nel BLOB chiamando il metodo [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Aggiungere questo codice alla fine del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB presenti nel contenitore chiamando il metodo [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice alla fine del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). Il codice di esempio aggiunge il suffisso "DOWNLOAD" al nome del file in modo da consentire la visualizzazione di entrambi i file nel file system locale.

Aggiungere questo codice alla fine del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente pulisce le risorse create dall'app eliminando l'intero contenitore tramite il metodo [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). È anche possibile eliminare i file locali, se si vuole.

L'app viene sospesa per l'input dell'utente chiamando `input()` prima dell'eliminazione di BLOB, contenitore e file locali. Verificare che le risorse siano state create correttamente, prima di essere eliminate.

Aggiungere questo codice alla fine del blocco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea un file di test nella cartella locale e lo carica nell'archivio BLOB di Azure. L'esempio elenca quindi i BLOB nel contenitore e Scarica il file con un nuovo nome. È possibile confrontare i file vecchi e nuovi.

Passare alla directory contenente il file *blob-quickstart-v12.py*, quindi usare questo comando `python` per eseguire l'app.

```console
python blob-quickstart-v12.py
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Prima di iniziare il processo di pulizia, controllare la cartella *dei dati* per i due file. È possibile aprirli e osservare che sono identici.

Dopo aver verificato i file, premere **INVIO** per eliminare i file di test e completare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come caricare, scaricare ed elencare i BLOB con Python.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Esempi di Python per Azure BLOB Storage SDK V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Per altre informazioni, vedere le [librerie client di Archiviazione di Azure per Python](/azure/developer/python/sdk/storage/overview).
* Per esercitazioni, esempi, guide di avvio rapido e altre documentazioni, vedere [Azure per sviluppatori Python](/azure/python/).
