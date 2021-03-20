---
title: Usare Java per gestire i dati in Azure Data Lake Storage Gen2
description: Usare le librerie di archiviazione di Azure per Java per gestire directory e file negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 10debe7bb870ddd9f8711e73ccb4b690d7011b62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650187"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usare Java per gestire directory e file in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Java per creare e gestire directory e file in account di archiviazione che hanno uno spazio dei nomi gerarchico.

Per informazioni su come ottenere, impostare e aggiornare gli elenchi di controllo di accesso (ACL) di directory e file, vedere [usare. Java per gestire gli ACL in Azure Data Lake Storage Gen2](data-lake-storage-acl-java.md).

[Pacchetto (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  di [Riferimento API](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, aprire [Questa pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e trovare la versione più recente della libreria Java. Aprire quindi il file di *pom.xml* nell'editor di testo. Aggiungere un elemento dependency che faccia riferimento a tale versione.

Se si prevede di autenticare l'applicazione client usando Azure Active Directory (Azure AD), aggiungere una dipendenza alla libreria client di Azure Secret. Vedere  [aggiunta del pacchetto di librerie client segrete al progetto](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Aggiungere quindi queste istruzioni Imports al file di codice.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Connettersi tramite Azure Active Directory (Azure AD)

È possibile usare la [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) per autenticare l'applicazione con Azure ad.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne una chiamando il metodo **DataLakeServiceClient. createFileSystem** .

Questo esempio crea un contenitore denominato `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo **DataLakeFileSystemClient. CreateDirectory** .

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore, quindi viene aggiunta una sottodirectory denominata `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DataLakeDirectoryClient. Rename** . Passare il percorso della directory desiderata a un parametro. 

Questo esempio rinomina una sottodirectory con il nome `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

In questo esempio viene spostata una directory denominata `my-subdirectory-renamed` in una sottodirectory di una directory denominata `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DataLakeDirectoryClient. deleteWithResponse** .

Questo esempio illustra come eliminare una directory denominata `my-directory`.   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Per prima cosa, creare un riferimento a un file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient** . Caricare un file chiamando il metodo **DataLakeFileClient. Append** . Assicurarsi di completare il caricamento chiamando il metodo **DataLakeFileClient. FlushAsync** .

Questo esempio consente di caricare un file di testo in una directory denominata `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Se le dimensioni del file sono elevate, il codice dovrà effettuare più chiamate al metodo **DataLakeFileClient. Append** . Provare a usare invece il metodo **DataLakeFileClient. uploadFromFile** . In questo modo, è possibile caricare l'intero file in una singola chiamata. 
>
> Vedere la sezione successiva per un esempio.

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Usare il metodo **DataLakeFileClient. uploadFromFile** per caricare file di grandi dimensioni senza dover eseguire più chiamate al metodo **DataLakeFileClient. Append** .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Scaricare da una directory

Per prima cosa, creare un'istanza di **DataLakeFileClient** che rappresenti il file che si desidera scaricare. Usare il metodo **DataLakeFileClient. Read** per leggere il file. Usare qualsiasi API di elaborazione di file .NET per salvare i byte dal flusso a un file. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

In questo esempio vengono stampati i nomi di ogni file che si trova in una directory denominata `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Vedi anche

* [Documentazione di riferimento delle API](/java/api/overview/azure/storage-file-datalake-readme)
* [Pacchetto (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia feedback](https://github.com/Azure/azure-sdk-for-java/issues)