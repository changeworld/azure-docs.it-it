---
title: Usare Python per gestire i dati in Azure Data Lake Storage Gen2
description: Usare Python per gestire directory e file negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652293"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usare Python per gestire directory e file in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Python per creare e gestire directory e file in account di archiviazione che hanno uno spazio dei nomi gerarchico.

Per informazioni su come ottenere, impostare e aggiornare gli elenchi di controllo di accesso (ACL) di directory e file, vedere [usare Python per gestire gli ACL in Azure Data Lake storage Gen2](data-lake-storage-acl-python.md).

[Pacchetto (indice pacchetto Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  di [Riferimento API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare la libreria client di Azure Data Lake Storage per Python usando [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Aggiungere queste istruzioni Import all'inizio del file di codice.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Sostituire il valore segnaposto `storage_account_name` con il nome del proprio account di archiviazione.

- Sostituire il `storage_account_key` valore del segnaposto con la chiave di accesso dell'account di archiviazione.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Connettersi tramite Azure Active Directory (Azure AD)

È possibile usare la [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) per autenticare l'applicazione con Azure ad.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne una chiamando il metodo **FileSystemDataLakeServiceClient.create_file_system** .

Questo esempio crea un contenitore denominato `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo **FileSystemClient.create_directory** .

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DataLakeDirectoryClient.rename_directory** . Passare il percorso della directory desiderata a un parametro. 

Questo esempio rinomina una sottodirectory con il nome `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DataLakeDirectoryClient.delete_directory** .

Questo esempio illustra come eliminare una directory denominata `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Per prima cosa, creare un riferimento a un file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient** . Caricare un file chiamando il metodo **DataLakeFileClient.append_data** . Assicurarsi di completare il caricamento chiamando il metodo **DataLakeFileClient.flush_data** .

Questo esempio consente di caricare un file di testo in una directory denominata `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Se le dimensioni del file sono elevate, il codice dovrà effettuare più chiamate al metodo **DataLakeFileClient.append_data** . Provare a usare invece il metodo **DataLakeFileClient.upload_data** . In questo modo, è possibile caricare l'intero file in una singola chiamata. 

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Usare il metodo **DataLakeFileClient.upload_data** per caricare file di grandi dimensioni senza dover eseguire più chiamate al metodo **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Scaricare da una directory 

Aprire un file locale per la scrittura. Quindi, creare un'istanza di **DataLakeFileClient** che rappresenti il file che si desidera scaricare. Chiamare il **DataLakeFileClient.read_file** per leggere i byte dal file e quindi scriverli nel file locale. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto della directory chiamando il metodo **FileSystemClient.get_paths** e quindi enumerando i risultati.

In questo esempio viene stampato il percorso di ogni sottodirectory e file che si trova in una directory denominata `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Vedi anche

- [Documentazione di riferimento delle API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacchetto (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)