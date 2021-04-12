---
title: Usare .NET per impostare gli ACL in Azure Data Lake Storage Gen2
description: Usare .NET per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654233"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usare .NET per gestire gli ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare .NET per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file.

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. Tuttavia, è anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio.

[Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  di Esempio di ACL [ricorsivo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [Riferimento API](/dotnet/api/azure.storage.files.datalake)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - [Entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, installare il pacchetto NuGet [Azure. storage. files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

1. Aprire una finestra di comando (ad esempio: Windows PowerShell).

2. Dalla directory del progetto, installare il pacchetto di anteprima di Azure. storage. files. datalake usando il `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Aggiungere quindi queste istruzioni using all'inizio del file di codice.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare l'accesso, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere il  [modello di controllo di accesso in Azure Data Lake storage Gen2](./data-lake-storage-access-control-model.md).

È possibile usare la [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) per autenticare l'applicazione con Azure ad.

Al termine dell'installazione del pacchetto, aggiungere questa istruzione using all'inizio del file di codice.

```csharp
using Azure.Identity;
```

Ottenere un ID client, un segreto client e un ID tenant. Per eseguire questa operazione, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

Questo esempio crea un'istanza di [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando un ID client, un segreto client e un ID tenant.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account.

Questo esempio crea un'istanza di [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando una chiave dell'account.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Imposta ACL

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [Update ACL](#update-acls) di questo articolo.  

Se si sceglie di *impostare* l'ACL, è necessario aggiungere una voce per l'utente proprietario, una voce per il gruppo proprietario e una voce per tutti gli altri utenti. Per ulteriori informazioni sull'utente proprietario, il gruppo proprietario e tutti gli altri utenti, vedere [utenti e identità](data-lake-storage-access-control.md#users-and-identities).

Questa sezione illustra come eseguire le operazioni seguenti:

- Impostare l'ACL di una directory
- Impostare l'ACL di un file
- Impostare ricorsivamente gli elenchi di controllo di accesso

### <a name="set-the-acl-of-a-directory"></a>Impostare l'ACL di una directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e impostare l'ACL chiamando il metodo [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Questo esempio Mostra come ottenere e impostare l'ACL di una directory denominata `my-directory` . La stringa `user::rwx,group::r-x,other::rw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, passare una stringa vuota ( `""` ) nel metodo [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>Impostare l'ACL di un file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e impostare l'ACL chiamando il metodo [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

Questo esempio Mostra come ottenere e impostare l'ACL di un file denominato `my-file.txt` . La stringa `user::rwx,group::r-x,other::rw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Impostare ricorsivamente gli elenchi di controllo di accesso

Impostare gli ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL.

Se si vuole impostare una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**. 

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se impostare l'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="update-acls"></a>Aggiornamenti ACL

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [Set ACL](#set-acls) di questo articolo.

Questa sezione illustra come eseguire le operazioni seguenti:

- Aggiornare un ACL
- Aggiornare gli ACL in modo ricorsivo

### <a name="update-an-acl"></a>Aggiornare un ACL

Per prima cosa, ottenere l'ACL di una directory chiamando il metodo [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Copiare l'elenco di voci ACL in un nuovo [elenco](/dotnet/api/system.collections.generic.list-1) di oggetti [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Individuare quindi la voce che si desidera aggiornare e sostituirla nell'elenco. Impostare l'ACL chiamando il metodo [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Questo esempio aggiorna l'ACL radice di un contenitore sostituendo la voce ACL per tutti gli altri utenti. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Aggiornare gli ACL in modo ricorsivo

Per aggiornare un ACL in modo ricorsivo, creare un nuovo oggetto ACL con la voce ACL che si desidera aggiornare e quindi utilizzare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare.

Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL.

Se si desidera aggiornare una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**.

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se aggiornare l'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Per un esempio in cui gli ACL vengono aggiornati in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="remove-acl-entries"></a>Rimuovi voci ACL

È possibile rimuovere una o più voci ACL. Questa sezione illustra come eseguire le operazioni seguenti:

- Rimuovere una voce ACL
- Rimuovi le voci ACL in modo ricorsivo

### <a name="remove-an-acl-entry"></a>Rimuovere una voce ACL

Per prima cosa, ottenere l'ACL di una directory chiamando il metodo [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Copiare l'elenco di voci ACL in un nuovo [elenco](/dotnet/api/system.collections.generic.list-1) di oggetti [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Individuare quindi la voce che si desidera rimuovere e chiamare il metodo [Remove](/dotnet/api/system.collections.ilist.remove) della raccolta. Impostare l'ACL aggiornato chiamando il metodo [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Questo esempio aggiorna l'ACL radice di un contenitore sostituendo la voce ACL per tutti gli altri utenti. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

Per rimuovere le voci ACL in modo ricorsivo, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare tale oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere. 

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Passare questo metodo a un [elenco](/dotnet/api/system.collections.generic.list-1) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ogni [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definisce una voce ACL. 

Se si vuole rimuovere una voce ACL **predefinita** , è possibile impostare la proprietà [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) su **true**. 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se rimuovere la voce dall'ACL predefinito. Tale parametro viene utilizzato nel costruttore di [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Per un esempio in cui gli ACL vengono rimossi in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione quando si modificano gli ACL in modo ricorsivo. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore `null` per il parametro del token di continuazione. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

Per assicurarsi che il processo venga completato senza interruzioni, passare un oggetto **AccessControlChangedOptions** e impostare la proprietà **ContinueOnFailure** dell'oggetto su ``true`` .

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Per un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l' [esempio](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Vedi anche

- [Documentazione di riferimento delle API](/dotnet/api/azure.storage.files.datalake)
- [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-net/issues)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)