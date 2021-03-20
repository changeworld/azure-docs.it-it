---
title: Usare l'interfaccia della riga di comando di Azure per gestire i dati (Azure Data Lake Storage Gen2)
description: Usare l'interfaccia della riga di comando di Azure per gestire directory e file negli account di archiviazione che hanno uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650355"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usare l'interfaccia della riga di comando di Azure per gestire directory e file in Azure Data Lake Storage Gen2

Questo articolo illustra come usare l'interfaccia della riga di comando di [Command-Line Azure (CLI)](/cli/azure/) per creare e gestire directory e file in account di archiviazione che hanno uno spazio dei nomi gerarchico.

Per informazioni su come ottenere, impostare e aggiornare gli elenchi di controllo di accesso (ACL) di directory e file, vedere [usare l'interfaccia della riga di comando di Azure per gestire gli ACL in Azure Data Lake storage Gen2](data-lake-storage-acl-cli.md).

[Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  di [Invia commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Assicurarsi che sia installata la versione corretta dell'interfaccia della riga di comando di Azure

1. Aprire [Azure Cloud Shell](../../cloud-shell/overview.md)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Verificare con il comando seguente che la versione dell'interfaccia della riga di comando di Azure installata corrisponda a `2.6.0` o successive.

   ```azurecli
    az --version
   ```

   Se la versione dell'interfaccia della riga di comando di Azure fosse inferiore a `2.6.0`, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando di accesso.

   ```azurecli
   az login
   ```

   Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

   In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale. Quindi, accedere con le credenziali dell'account nel browser.

   Per altre informazioni sui diversi metodi di autenticazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](./authorize-data-operations-cli.md).

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva alla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

> [!NOTE]
> Nell'esempio presentato in questo articolo viene illustrata l'autorizzazione Azure Active Directory (Azure AD). Per altre informazioni sui metodi di autorizzazione, vedere [Autorizzare l'accesso ai dati BLOB o ai dati della coda con l'interfaccia della riga di comando di Azure](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne uno con il comando `az storage fs create`. 

Questo esempio crea un contenitore denominato `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Mostra proprietà contenitore

È possibile stampare le proprietà di un contenitore nella console usando il `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Elencare il contenuto del contenitore

Elencare il contenuto di una directory con il comando `az storage fs file list`.

Questo esempio elenca il contenuto di un contenitore denominato `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Eliminare un contenitore

Eliminare un contenitore usando il `az storage fs delete` comando.

In questo esempio viene eliminato un contenitore denominato `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory con il comando `az storage fs directory create`. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore denominato `my-file-system` che si trova in un account denominato `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mostrare le proprietà della directory

È possibile stampare nella console le proprietà di una directory con il comando `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory con il comando `az storage fs directory move`.

Questo esempio rinomina una directory dal nome `my-directory` al nome `my-new-directory` nello stesso contenitore.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Questo esempio Mostra come spostare una directory in un contenitore denominato `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory con il comando `az storage fs directory delete`.

Questo esempio illustra come eliminare una directory denominata `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Controllare se esista una directory

Determinare se una directory specifica esiste nel contenitore tramite il `az storage fs directory exists` comando.

Questo esempio Mostra se una directory denominata `my-directory` esiste nel `my-file-system` contenitore. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Scaricare da una directory

Scaricare un file da una directory con il comando `az storage fs file download`.

Questo esempio illustra come scaricare un file denominato `upload.txt` da una directory denominata `my-directory`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory con il comando `az storage fs file list`.

Questo esempio elenca il contenuto di una directory denominata `my-directory` che si trova nel `my-file-system` contenitore di un account di archiviazione denominato `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory con il comando `az storage fs directory upload`.

Questo esempio illustra come caricare un file denominato `upload.txt` in una directory denominata `my-directory`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrare le proprietà file

È possibile stampare nella console le proprietà di un file con il comando `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Rinominare o spostare un file

Rinominare o spostare un file con il comando `az storage fs file move`.

Questo esempio illustra come rinominare un file da `my-file.txt` a `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file con il comando `az storage fs file delete`.

Questo esempio illustra come eliminare un file denominato `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Vedi anche

- [Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Usare l'interfaccia della riga di comando di Azure per gestire ACL in Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)