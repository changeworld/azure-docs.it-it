---
title: Usare Python per gestire gli ACL in Azure Data Lake Storage Gen2
description: Usare Python per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654223"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usare Python per gestire gli ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Python per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file. 

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. Tuttavia, è anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio. 

[Pacchetto (indice pacchetto Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  di Esempi di ACL [ricorsivi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [Riferimento API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - [Entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare l'accesso, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere il  [modello di controllo di accesso in Azure Data Lake storage Gen2](./data-lake-storage-access-control-model.md).

È possibile usare la [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) per autenticare l'applicazione con Azure ad.

Ottenere un ID client, un segreto client e un ID tenant. Per eseguire questa operazione, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md). Come parte di questo processo, è necessario assegnare uno dei ruoli di controllo degli [accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md) per l'entità di sicurezza. 

|Ruolo|Funzionalità impostazione ACL|
|--|--|
|[Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tutte le directory e i file nell'account.|
|[Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Solo le directory e i file di proprietà dell'entità di sicurezza.|

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Sostituire il valore segnaposto `storage_account_name` con il nome del proprio account di archiviazione.

- Sostituire il `storage_account_key` valore del segnaposto con la chiave di accesso dell'account di archiviazione.

## <a name="set-acls"></a>Imposta ACL

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [Update ACL](#update-acls-recursively) di questo articolo.  

Questa sezione illustra come eseguire le operazioni seguenti:

- Impostare l'ACL di una directory
- Impostare l'ACL di un file

### <a name="set-the-acl-of-a-directory"></a>Impostare l'ACL di una directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo **DataLakeDirectoryClient.get_access_control** e impostare l'ACL chiamando il metodo **DataLakeDirectoryClient.set_access_control** .

Questo esempio Mostra come ottenere e impostare l'ACL di una directory denominata `my-directory` . La stringa `rwxr-xrw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, chiamare il metodo **FileSystemClient._get_root_directory_client** .

### <a name="set-the-acl-of-a-file"></a>Impostare l'ACL di un file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo **DataLakeFileClient.get_access_control** e impostare l'ACL chiamando il metodo **DataLakeFileClient.set_access_control** .

Questo esempio Mostra come ottenere e impostare l'ACL di un file denominato `my-file.txt` . La stringa `rwxr-xrw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Impostare ricorsivamente gli elenchi di controllo di accesso

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [aggiornare gli ACL in modo ricorsivo](#update-acls-recursively) di questo articolo.

Impostare gli ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.set_access_control_recursive** .

Se si desidera impostare una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio di ogni stringa di voce ACL.

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` .

Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se impostare l'ACL predefinito. Se il parametro è `True` , l'elenco di voci ACL è preceduto dalla stringa `default:` .

Le voci dell'ACL forniscono le autorizzazioni di lettura, scrittura ed esecuzione da parte dell'utente proprietario, assegna al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli utenti nessun accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione. Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "autorizzazioni di lettura ed esecuzione.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

## <a name="update-acls-recursively"></a>Aggiornare gli ACL in modo ricorsivo

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [Set ACL](#set-acls) di questo articolo.

Per aggiornare un ACL in modo ricorsivo, creare un nuovo oggetto ACL con la voce ACL che si desidera aggiornare e quindi utilizzare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare. Aggiornare un ACL in modo ricorsivo chiamando il metodo **DataLakeDirectoryClient.update_access_control_recursive** . Se si desidera aggiornare una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio di ogni stringa di voce ACL.

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura.

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se aggiornare l'ACL predefinito. Se il parametro è `True` , la voce ACL aggiornata è preceduta dalla stringa `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

## <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

È possibile rimuovere una o più voci ACL. Per rimuovere le voci ACL in modo ricorsivo, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare tale oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere. 

Rimuovere le voci ACL chiamando il metodo **DataLakeDirectoryClient.remove_access_control_recursive** . Se si desidera rimuovere una voce ACL **predefinita** , aggiungere la stringa `default:` all'inizio della stringa di immissione dell'ACL. 

In questo esempio viene rimossa una voce ACL dall'ACL della directory denominata `my-parent-directory` . Questo metodo accetta un parametro booleano denominato `is_default_scope` che specifica se rimuovere la voce dall'ACL predefinito. Se il parametro è `True` , la voce ACL aggiornata è preceduta dalla stringa `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

In questo esempio viene restituito un token di continuazione in caso di errore. L'applicazione può chiamare nuovamente questo metodo di esempio dopo che l'errore è stato risolto e passare il token di continuazione. Se questo metodo di esempio viene chiamato per la prima volta, l'applicazione può passare un valore ``None`` per il parametro del token di continuazione.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

Per assicurarsi che il processo venga completato senza interruzioni, non passare un token di continuazione al metodo **DataLakeDirectoryClient.set_access_control_recursive** .

In questo esempio le voci ACL vengono impostate in modo ricorsivo. Se il codice rileva un errore di autorizzazione, registra l'errore e continua l'esecuzione. In questo esempio viene stampato il numero di errori nella console.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Per un esempio che elabora gli ACL in modo ricorsivo in batch specificando una dimensione del batch, vedere l' [esempio](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)Python.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Vedi anche

- [Documentazione di riferimento delle API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacchetto (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)