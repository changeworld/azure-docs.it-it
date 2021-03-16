---
title: 'Usare PowerShell per gestire i dati: Azure Data Lake Storage Gen2'
description: Usare i cmdlet di PowerShell per gestire directory e file negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573864"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usare PowerShell per gestire directory e file in Azure Data Lake Storage Gen2

Questo articolo illustra come usare PowerShell per creare e gestire directory e file in account di archiviazione che hanno uno spazio dei nomi gerarchico.

Per informazioni su come ottenere, impostare e aggiornare gli elenchi di controllo di accesso (ACL) di directory e file, vedere [usare PowerShell per gestire gli ACL in Azure Data Lake storage Gen2](data-lake-storage-acl-powershell.md).

Informazioni di [riferimento](/powershell/module/Az.Storage/)  |  Mapping tra Gen1 [e Gen2](#gen1-gen2-map)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- .NET Framework è installato 4.7.2 o versione successiva. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- Versione di PowerShell `5.1` o successiva.

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Installare **AZ. storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Scegliere il modo in cui si vuole che i comandi ottengano l'autorizzazione per l'account di archiviazione. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Opzione 1: ottenere l'autorizzazione utilizzando Azure Active Directory (Azure AD)

Con questo approccio, il sistema garantisce che l'account utente disponga delle assegnazioni appropriate di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e delle autorizzazioni ACL.

1. Aprire una finestra di comando di Windows PowerShell e accedere alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione in cui si vogliono creare e gestire le directory. In questo esempio, sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Ottenere il contesto dell'account di archiviazione.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opzione 2: ottenere l'autorizzazione usando la chiave dell'account di archiviazione

Con questo approccio, il sistema non controlla le autorizzazioni RBAC o ACL di Azure. Ottenere il contesto dell'account di archiviazione usando una chiave dell'account.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne uno usando il `New-AzStorageContainer` cmdlet. 

Questo esempio crea un contenitore denominato `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il `New-AzDataLakeGen2Item` cmdlet. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Questo esempio aggiunge la stessa directory, ma imposta anche le autorizzazioni, umask, i valori delle proprietà e i valori dei metadati. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrare le proprietà della directory

In questo esempio viene ottenuta una directory utilizzando il `Get-AzDataLakeGen2Item` cmdlet, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Per ottenere la directory radice del contenitore, omettere il `-Path` parametro.

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory utilizzando il `Move-AzDataLakeGen2Item` cmdlet.

Questo esempio rinomina una directory dal nome `my-directory` con il nome `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Utilizzare il `-Force` parametro se si desidera sovrascrivere senza prompt.

In questo esempio viene spostata una directory denominata `my-directory` in una sottodirectory di `my-directory-2` denominata `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory utilizzando il `Remove-AzDataLakeGen2Item` cmdlet.

Questo esempio illustra come eliminare una directory denominata `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

È possibile usare il `-Force` parametro per rimuovere il file senza una richiesta.

## <a name="download-from-a-directory"></a>Scaricare da una directory

Scaricare un file da una directory utilizzando il `Get-AzDataLakeGen2ItemContent` cmdlet.

Questo esempio illustra come scaricare un file denominato `upload.txt` da una directory denominata `my-directory`.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory utilizzando il `Get-AzDataLakeGen2ChildItem` cmdlet. È possibile usare il parametro facoltativo `-OutputUserPrincipalName` per ottenere il nome, anziché l'ID oggetto, degli utenti.

Questo esempio elenca il contenuto di una directory denominata `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Nell'esempio seguente vengono elencate `ACL` le `Permissions` proprietà,, `Group` e `Owner` di ogni elemento nella directory. Il `-FetchProperty` parametro è necessario per ottenere i valori per la `ACL` Proprietà. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Per elencare il contenuto della directory radice del contenitore, omettere il `-Path` parametro.

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory utilizzando il `New-AzDataLakeGen2Item` cmdlet.

Questo esempio illustra come caricare un file denominato `upload.txt` in una directory denominata `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In questo esempio viene caricato lo stesso file, ma vengono quindi impostati le autorizzazioni, umask, i valori delle proprietà e i valori dei metadati del file di destinazione. Questo esempio visualizza anche questi valori nella console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Per caricare un file nella directory radice del contenitore, omettere il `-Path` parametro.

## <a name="show-file-properties"></a>Mostrare le proprietà file

Questo esempio Mostra come ottenere un file usando il `Get-AzDataLakeGen2Item` cmdlet, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il `Remove-AzDataLakeGen2Item` cmdlet.

In questo esempio viene eliminato un file denominato `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

È possibile usare il `-Force` parametro per rimuovere il file senza una richiesta.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Mapping tra Gen1 e Gen2

Nella tabella seguente viene illustrato il modo in cui i cmdlet utilizzati per Data Lake Storage Gen1 vengono mappati ai cmdlet di per Data Lake Storage Gen2.

|Cmdlet Data Lake Storage Gen1| Cmdlet Data Lake Storage Gen2| Note |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Per impostazione predefinita, il cmdlet Get-AzDataLakeGen2ChildItem elenca solo gli elementi figlio di primo livello. Il parametro-Recurse elenca gli elementi figlio in modo ricorsivo. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Gli elementi di output del cmdlet Get-AzDataLakeGen2Item dispongono di queste proprietà: ACL, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Il cmdlet Get-AzDataLakeGen2FileContent scaricare il contenuto del file nel file locale.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Questo cmdlet carica il nuovo contenuto del file da un file locale.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Il cmdlet Update-AzDataLakeGen2Item aggiorna un solo elemento e non in modo ricorsivo. Se si vuole aggiornare in modo ricorsivo, elencare gli elementi usando il cmdlet Get-AzDataLakeStoreChildItem, quindi eseguire la pipeline al cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Il cmdlet Get-AzDataLakeGen2Item segnalerà un errore se l'elemento non esiste.|

## <a name="see-also"></a>Vedi anche

- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage)
