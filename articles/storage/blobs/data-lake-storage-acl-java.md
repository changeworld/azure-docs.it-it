---
title: Usare Java per impostare gli ACL in Azure Data Lake Storage Gen2
description: Usare le librerie di archiviazione di Azure per Java per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654226"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utilizzare Java per gestire gli ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Java per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file. 

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. Tuttavia, è anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio. 

[Pacchetto (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  di [Riferimento API](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - [Entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, aprire [Questa pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e trovare la versione più recente della libreria Java. Aprire quindi il file di *pom.xml* nell'editor di testo. Aggiungere un elemento dependency che faccia riferimento a tale versione.

Se si prevede di autenticare l'applicazione client usando Azure Active Directory (AD), aggiungere una dipendenza alla libreria client di Azure Secret. Vedere  [aggiunta del pacchetto di librerie client segrete al progetto](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Connettersi tramite Azure Active Directory (Azure AD)

È possibile usare la [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) per autenticare l'applicazione con Azure ad.

Ottenere un ID client, un segreto client e un ID tenant. Per eseguire questa operazione, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Imposta ACL

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [Update ACL](#update-acls) di questo articolo.  

Se si sceglie di *impostare* l'ACL, è necessario aggiungere una voce per l'utente proprietario, una voce per il gruppo proprietario e una voce per tutti gli altri utenti. Per ulteriori informazioni sull'utente proprietario, il gruppo proprietario e tutti gli altri utenti, vedere [utenti e identità](data-lake-storage-access-control.md#users-and-identities).

Questa sezione illustra come eseguire le operazioni seguenti:

- Impostare l'ACL di una directory
- Impostare l'ACL di un file
- Impostare ricorsivamente gli elenchi di controllo di accesso 

### <a name="set-the-acl-of-a-directory"></a>Impostare l'ACL di una directory

Questo esempio ottiene e imposta l'ACL di una directory denominata `my-directory` . Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, passare una stringa vuota ( `""` ) nel metodo **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>Impostare l'ACL di un file

Questo esempio ottiene e imposta l'ACL di un file denominato `upload-file.txt` . Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Impostare ricorsivamente gli elenchi di controllo di accesso

Impostare gli ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. setAccessControlRecursive** . Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL.

Se si vuole impostare una voce ACL **predefinita** , è possibile chiamare il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**. 

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se impostare l'ACL predefinito. Tale parametro viene utilizzato in ogni chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Aggiornamenti ACL

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [Set ACL](#set-acls) di questo articolo.

Questa sezione illustra come eseguire le operazioni seguenti:

- Aggiornare un ACL
- Aggiornare gli ACL in modo ricorsivo

### <a name="update-an-acl"></a>Aggiornare un ACL

Per prima cosa, ottenere l'ACL di una directory chiamando il metodo [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Copiare l'elenco di voci ACL in un nuovo oggetto **elenco** di tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Individuare quindi la voce che si desidera aggiornare e sostituirla nell'elenco. Impostare l'ACL chiamando il metodo [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

In questo esempio viene aggiornato l'ACL di una directory denominata `my-parent-directory` sostituendo la voce per tutti gli altri utenti. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, passare una stringa vuota ( `""` ) nel metodo **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Aggiornare gli ACL in modo ricorsivo

Per aggiornare un ACL in modo ricorsivo, creare un nuovo oggetto ACL con la voce ACL che si desidera aggiornare e quindi utilizzare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare.

Aggiornare gli ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient. updateAccessControlRecursive** .  Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL. 

Se si desidera aggiornare una voce ACL **predefinita** , è possibile utilizzare il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**. 

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura. Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se aggiornare l'ACL predefinito. Tale parametro viene utilizzato nella chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Rimuovi voci ACL

È possibile rimuovere una o più voci ACL. Questa sezione illustra come eseguire le operazioni seguenti:

- Rimuovere una voce ACL
- Rimuovi le voci ACL in modo ricorsivo

### <a name="remove-an-acl-entry"></a>Rimuovere una voce ACL

Per prima cosa, ottenere l'ACL di una directory chiamando il metodo [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Copiare l'elenco di voci ACL in un nuovo oggetto **elenco** di tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Individuare quindi la voce che si desidera rimuovere e chiamare il metodo **Remove** dell'oggetto **List** . Impostare l'ACL aggiornato chiamando il metodo [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

Per rimuovere le voci ACL in modo ricorsivo, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare tale oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere.

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient. removeAccessControlRecursive** . Passare questo metodo a un [elenco](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) di oggetti [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Ogni [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definisce una voce ACL. 

Se si vuole rimuovere una voce ACL **predefinita** , è possibile il metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**.  

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `isDefaultScope` che specifica se rimuovere la voce dall'ACL predefinito. Tale parametro viene utilizzato nella chiamata al metodo **setDefaultScope** di [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore `null` per il parametro del token di continuazione.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

Per assicurarsi che il processo venga completato senza interruzioni, chiamare il metodo **setContinueOnFailure** di un oggetto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passare il valore **true**.

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Vedi anche

- [Documentazione di riferimento delle API](/java/api/overview/azure/storage-file-datalake-readme)
- [Pacchetto (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)