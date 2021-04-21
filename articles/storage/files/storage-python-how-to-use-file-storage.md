---
title: Sviluppare per File di Azure con Python | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi Python che usano File di Azure per archiviare i dati dei file.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817793"
---
# <a name="develop-for-azure-files-with-python"></a>Sviluppare per File di Azure con Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Informazioni di base sull'uso di Python per sviluppare app o servizi che usano File di Azure per archiviare i dati dei file. Creare una semplice app console e imparare a eseguire azioni di base con Python e File di Azure:

- Creare condivisioni file di Azure
- Creare directory
- Enumerare file e directory in una condivisione file di Azure
- Caricare, scaricare ed eliminare un file
- Creare backup di condivisione file tramite snapshot

> [!NOTE]
> Poiché File di Azure è accessibile tramite SMB, è possibile scrivere semplici applicazioni che accedono alla condivisione file di Azure usando le classi e le funzioni di I/O standard di Python. Questo articolo descrive come scrivere app che usano File di Azure Storage Python SDK, che usa [l'API REST File di Azure](/rest/api/storageservices/file-service-rest-api) per parlare con File di Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, disinstallare l'SDK meno recente tramite `pip uninstall azure-storage` prima di installare il pacchetto più recente.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

La [libreria client di Archiviazione file di Azure v12.x per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) richiede Python 2.7 o 3.6+.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

[L Archiviazione di Azure SDK per Python](https://github.com/azure/azure-storage-python) richiede Python 2.7 o 3.6+.

---

## <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPI

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Visualizzare l'applicazione di esempio

Per visualizzare ed eseguire un'applicazione di esempio che illustra come usare Python con File di Azure, vedere [Archiviazione di Azure: Attività iniziali con File di Azure in Python.](https://github.com/Azure-Samples/storage-file-python-getting-started)

Per eseguire l'applicazione di esempio, assicurarsi di aver installato entrambi i `azure-storage-file` pacchetti `azure-storage-common` e .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Configurare l'applicazione per usare File di Azure

Aggiungere quanto segue nella parte superiore di un file di origine Python per usare i frammenti di codice in questo articolo.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Configurare una connessione a File di Azure

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

[ShareServiceClient consente](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) di usare condivisioni, directory e file. Il codice seguente crea un oggetto `ShareServiceClient` usando la stringa di connessione dell'account di archiviazione.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

[L'oggetto FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) consente di usare condivisioni, directory e file. Il codice seguente crea un oggetto `FileService` usando il nome e la chiave dell'account di archiviazione. Sostituire `<myaccount>` e `<mykey>` con il nome e la chiave dell'account.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Nell'esempio di codice seguente viene utilizzato [un oggetto ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) per creare la condivisione, se non esiste.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Nell'esempio di codice seguente viene utilizzato [un oggetto FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) per creare la condivisione, se non esiste.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Creare una directory

È possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Il metodo seguente crea una directory nella radice della condivisione file specificata usando un [oggetto ShareDirectoryClient.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Il codice seguente creerà una sottodirectory denominata *sampledir* nella directory radice.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Caricare un file

In questa sezione si apprenderà come caricare un file dall'archiviazione locale in Archiviazione file di Azure.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Il metodo seguente carica il contenuto del file specificato nella directory specificata nella condivisione file di Azure specificata.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Una condivisione file di Azure contiene almeno una directory radice in cui possono risiedere i file. Per creare un file e caricare i dati, usare i [metodi](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-)create_file_from_path , [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)o [create_file_from_text.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) Si tratta di metodi di alto livello che eseguono la suddivisione in blocchi necessaria quando le dimensioni dei dati superano i 64 MB.

`create_file_from_path` carica i contenuti di un file dal percorso specificato, mentre `create_file_from_stream` carica i contenuti da un file/flusso già aperto. `create_file_from_bytes` carica un array di byte, mentre `create_file_from_text` carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file *sunset.png* nel file **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Per elencare i file e le directory in una sottodirectory, usare il [metodo list_directories_and_files.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) Questo metodo restituisce un'operazione di paging automatico iterabile. Il codice seguente restituisce il **nome di** ogni file e sottodirectory nella directory specificata nella console.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Per elencare i file e le directory in una condivisione, usare il [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) seguente. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni file e directory in una condivisione.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Scaricare un file

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Per scaricare i dati da un file, [usare download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Nell'esempio seguente viene illustrato l'utilizzo di per ottenere il contenuto del file specificato e archiviarlo localmente con `download_file` **DOWNLOADED-** anteposto al nome del file.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Per scaricare dati da un file, [usare get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)o [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Si tratta di metodi di alto livello che eseguono la suddivisione in blocchi necessaria quando le dimensioni dei dati superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di `get_file_to_path` per scaricare il contenuto del file **myfile** e archiviarlo nel file *out-sunset.png*.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Creare uno snapshot della condivisione

È possibile creare una copia temporizzata dell'intera condivisione file.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Creare uno snapshot di condivisione con metadati**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Elencare condivisioni e snapshot

È possibile elencare tutti gli snapshot per una particolare condivisione.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Esplorare uno snapshot di condivisione

È possibile esplorare ogni snapshot di condivisione per recuperare file e directory da quel punto nel tempo.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Ottenere file da uno snapshot di condivisione

È possibile scaricare un file da uno snapshot di condivisione. In questo modo è possibile ripristinare una versione precedente di un file.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Eliminare un singolo snapshot di condivisione
È possibile eliminare un singolo snapshot di condivisione.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Eliminare un file

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Per eliminare un file, chiamare [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Per eliminare un file, chiamare [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Eliminare una condivisione in presenza di snapshot di condivisione

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Per eliminare una condivisione che contiene snapshot, [chiamare](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) delete_share con `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Una condivisione contenente snapshot non può essere eliminata se prima non vengono eliminati tutti gli snapshot.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come interagire con File di Azure con Python, selezionare i collegamenti seguenti per altre informazioni.

- [Centro per sviluppatori Python](/azure/developer/python/)
- [API REST dei servizi di archiviazione di Azure](/rest/api/azure/)
- [Microsoft Azure Storage SDK per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
