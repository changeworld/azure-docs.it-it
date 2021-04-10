---
title: Usare l'interfaccia della riga di comando di Azure per impostare gli elenchi Azure Data Lake Storage Gen2 di
description: Usare l'interfaccia della riga di comando di Azure per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione con uno spazio dei nomi gerarchico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563165"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usare l'interfaccia della riga di comando di Azure per gestire ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare l'interfaccia della riga di comando di [Command-Line Azure (CLI)](/cli/azure/) per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file.

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. Tuttavia, è anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio.

Informazioni di [riferimento](/cli/azure/storage/fs/access)  |  [Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  di [Invia commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.14.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - Un' [entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (Azure ad) a cui è stato assegnato il ruolo di [proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Assicurarsi che sia installata la versione corretta dell'interfaccia della riga di comando di Azure

1. Aprire [Azure Cloud Shell](../../cloud-shell/overview.md)o aprire un'applicazione console comando come Windows PowerShell, se è stata [installata](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure in locale.

2. Verificare con il comando seguente che la versione dell'interfaccia della riga di comando di Azure installata corrisponda a `2.14.0` o successive.

   ```azurecli
    az --version
   ```

   Se la versione dell'interfaccia della riga di comando di Azure fosse inferiore a `2.14.0`, installare una versione successiva. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>Ottieni ACL

Recuperare l'ACL da una **directory** con il comando `az storage fs access show`.

Questo esempio illustra come recuperare l'ACL di una directory e quindi come stampare l'ACL nella console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ottenere le autorizzazioni di accesso di un **file** con il comando `az storage fs access show`. 

Questo esempio illustra come recuperare l'ACL di un file e quindi come stampare l'ACL nella console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Nell'immagine seguente illustra l'output dopo aver recuperato l'ACL di una directory.

![Recuperare l'output di un ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In questo esempio, l'utente proprietario è provvisto delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario è provvisto delle sole autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Imposta ACL

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [Update ACL](#update-acls) di questo articolo.  

Se si sceglie di *impostare* l'ACL, è necessario aggiungere una voce per l'utente proprietario, una voce per il gruppo proprietario e una voce per tutti gli altri utenti. Per ulteriori informazioni sull'utente proprietario, il gruppo proprietario e tutti gli altri utenti, vedere [utenti e identità](data-lake-storage-access-control.md#users-and-identities).

Questa sezione illustra come eseguire le operazioni seguenti:

- Impostare un ACL
- Impostare ricorsivamente gli elenchi di controllo di accesso

### <a name="set-an-acl"></a>Impostare un ACL

Usare il comando `az storage fs access set` per impostare l'ACL da una **directory**. 

Questo esempio illustra come impostare l'ACL in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come impostare l'ACL *predefinito* in una directory per l'utente proprietario, il gruppo proprietario o altri utenti, infine stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Usare il comando `az storage fs access set` per impostare l'ACL da un **file**. 

Questo esempio illustra come impostare l'ACL in un file per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Per impostare l'ACL di un gruppo o di un utente specifico, utilizzare i rispettivi ID oggetto. Ad esempio, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Nell'immagine seguente illustra l'output dopo aver impostato l'ACL di un file.

![Ottenere l'output ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In questo esempio, l'utente proprietario e il gruppo proprietario sono provvisti delle sole autorizzazioni di lettura e scrittura. Tutti gli altri utenti sono provvisti di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Impostare ricorsivamente gli elenchi di controllo di accesso

Impostare gli ACL in modo ricorsivo usando il comando [AZ storage FS Access set-ricorsivo](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera impostare una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio, `default:user::rwx` o `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Aggiornamenti ACL

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [Set ACL](#set-acls) di questo articolo.

Per aggiornare un ACL, creare un nuovo oggetto ACL con la voce ACL che si vuole aggiornare e quindi usare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare.

Questa sezione illustra come eseguire le operazioni seguenti:

- Aggiornare un ACL
- Aggiornare gli ACL in modo ricorsivo

### <a name="update-an-acl"></a>Aggiornare un ACL

Un altro modo di impostare questa autorizzazione consiste nell'usare il comando `az storage fs access set`. 

Aggiornare l'ACL di una directory o di un file impostando il parametro `-permissions` sulla dettatura breve di un ACL.

Questo esempio illustra come aggiornare l'ACL di una **directory**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come aggiornare l'ACL di un **file**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Per aggiornare l'ACL di un gruppo o di un utente specifico, usare i rispettivi ID oggetto. Ad esempio, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

È anche possibile aggiornare l'utente proprietario e il gruppo di una directory o di un file impostando i parametri `--owner` o `group` sull'ID entità oppure sul nome dell'entità utente (UPN) di un utente.

Questo esempio illustra come modificare il proprietario di una directory.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Questo esempio illustra come modificare il proprietario di un file. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Aggiornare gli ACL in modo ricorsivo

Aggiornare gli ACL in modo ricorsivo usando il comando  [AZ storage FS Access Update-ricorsivo](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera aggiornare una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

È possibile rimuovere una o più voci ACL in modo ricorsivo. Per rimuovere una voce ACL, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare l'oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere.

Rimuovere le voci ACL usando il comando [AZ storage FS Access Remove-ricorsivo](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) .

In questo esempio viene rimossa una voce ACL dalla directory radice del contenitore.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se si desidera rimuovere una voce ACL **predefinita** , aggiungere il prefisso `default:` a ogni voce. Ad esempio: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione quando si modificano gli ACL in modo ricorsivo. Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

In caso di errore, è possibile restituire un token di continuazione impostando il `--continue-on-failure` parametro su `false` . Dopo aver indirizzato gli errori, è possibile riprendere il processo dal punto in cui si è verificato l'errore eseguendo di nuovo il comando e quindi impostando il `--continuation` parametro sul token di continuazione.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

Per assicurarsi che il processo venga completato senza interruzioni, impostare il `--continue-on-failure` parametro su `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Vedi anche

- [Esempi](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Commenti e suggerimenti](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)