---
title: Usare PowerShell per gestire gli ACL in Azure Data Lake Storage Gen2
description: Usare i cmdlet di PowerShell per gestire gli elenchi di controllo di accesso (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104702542"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usare PowerShell per gestire gli ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare PowerShell per ottenere, impostare e aggiornare gli elenchi di controllo di accesso di directory e file. 

L'ereditarietà ACL è già disponibile per i nuovi elementi figlio creati in una directory padre. Tuttavia, è anche possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio. 

Informazioni di [riferimento](/powershell/module/Az.Storage/)  |  Esempi di ACL [ricorsivi](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](create-data-lake-storage-account.md) istruzioni.

- Interfaccia della riga di comando di Azure versione `2.6.0` o successiva.

- Una delle autorizzazioni di sicurezza seguenti:

  - [Entità di sicurezza](../../role-based-access-control/overview.md#security-principal) con provisioning Azure Active Directory (ad) a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore di destinazione, del gruppo di risorse padre o della sottoscrizione.  

  - Utente proprietario del contenitore o della directory di destinazione a cui si intende applicare le impostazioni ACL. Per impostare gli ACL in modo ricorsivo, sono inclusi tutti gli elementi figlio nel contenitore o nella directory di destinazione.
  
  - Chiave dell'account di archiviazione.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opzione 1: ottenere l'autorizzazione utilizzando Azure Active Directory (AD)

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare l'accesso, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere il  [modello di controllo di accesso in Azure Data Lake storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Ottieni ACL

Ottenere l'ACL di una directory o di un file usando il `Get-AzDataLakeGen2Item` cmdlet.

Questo esempio Mostra come ottenere l'ACL della directory radice di un **contenitore** e quindi stampare l'ACL sulla console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Questo esempio Mostra come ottenere l'ACL di una **directory** e quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Questo esempio Mostra come ottenere l'ACL di un **file** e quindi stamparlo nella console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Nell'immagine seguente illustra l'output dopo aver recuperato l'ACL di una directory.

![Ottenere l'output ACL per la directory](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In questo esempio, l'utente proprietario è provvisto delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario è provvisto delle sole autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Imposta ACL

Quando si *imposta* un ACL, si **sostituisce** l'intero ACL, incluse tutte le voci. Se si desidera modificare il livello di autorizzazione di un'entità di sicurezza o aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre voci esistenti, è necessario *aggiornare* invece l'ACL. Per aggiornare un ACL anziché sostituirlo, vedere la sezione [Update ACL](#update-acls) di questo articolo.  

Se si sceglie di *impostare* l'ACL, è necessario aggiungere una voce per l'utente proprietario, una voce per il gruppo proprietario e una voce per tutti gli altri utenti. Per ulteriori informazioni sull'utente proprietario, il gruppo proprietario e tutti gli altri utenti, vedere [utenti e identità](data-lake-storage-access-control.md#users-and-identities).

Questa sezione illustra come eseguire le operazioni seguenti:

- Impostare un ACL
- Impostare ricorsivamente gli elenchi di controllo di accesso

### <a name="set-an-acl"></a>Impostare un ACL

Usare il `set-AzDataLakeGen2ItemAclObject` cmdlet per creare un ACL per l'utente proprietario, il gruppo proprietario o altri utenti. Usare quindi il `Update-AzDataLakeGen2Item` cmdlet per eseguire il commit dell'ACL.

Questo esempio imposta l'ACL nella directory radice di un **contenitore** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Questo esempio imposta l'ACL in una **directory** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Se si vuole impostare una voce ACL **predefinita** , usare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

In questo esempio viene impostato l'ACL in un **file** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Per impostare l'ACL di un gruppo o di un utente specifico, utilizzare i rispettivi ID oggetto. Ad esempio, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Nell'immagine seguente illustra l'output dopo aver impostato l'ACL di un file.

![Ottenere l'output ACL per il file](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In questo esempio, l'utente proprietario e il gruppo proprietario sono provvisti delle sole autorizzazioni di lettura e scrittura. Tutti gli altri utenti sono provvisti di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Impostare ricorsivamente gli elenchi di controllo di accesso

Impostare gli ACL in modo ricorsivo utilizzando il cmdlet **set-AzDataLakeGen2AclRecursive** .

In questo esempio viene impostato l'ACL di una directory denominata `my-parent-directory` . Queste voci forniscono le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e offre a tutti gli altri utenti l'accesso. L'ultima voce ACL in questo esempio fornisce a un utente specifico l'ID oggetto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" autorizzazioni di lettura ed esecuzione.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se si vuole impostare una voce ACL **predefinita** , usare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

## <a name="update-acls"></a>Aggiornamenti ACL

Quando si *Aggiorna* un ACL, si modifica l'ACL anziché sostituire l'ACL. Ad esempio, è possibile aggiungere una nuova entità di sicurezza all'ACL senza influire sulle altre entità di sicurezza elencate nell'ACL.  Per sostituire l'ACL anziché aggiornarlo, vedere la sezione [Set ACL](#set-acls) di questo articolo.

Per aggiornare un ACL, creare un nuovo oggetto ACL con la voce ACL che si vuole aggiornare e quindi usare tale oggetto in Update ACL Operation. Non ottenere l'ACL esistente, fornire solo le voci ACL da aggiornare.

Questa sezione illustra come eseguire le operazioni seguenti:

- Aggiornare un ACL
- Aggiornare gli ACL in modo ricorsivo

### <a name="update-an-acl"></a>Aggiornare un ACL

Per prima cosa, ottenere l'ACL. Usare quindi il `set-AzDataLakeGen2ItemAclObject` cmdlet per aggiungere o aggiornare una voce ACL. Usare il `Update-AzDataLakeGen2Item` cmdlet per eseguire il commit dell'ACL.

In questo esempio viene creato o aggiornato l'ACL in una **directory** per un utente.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Se si desidera aggiornare una voce ACL **predefinita** , utilizzare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Aggiornare gli ACL in modo ricorsivo

Aggiornare gli ACL in modo ricorsivo usando il cmdlet  **Update-AzDataLakeGen2AclRecursive** .

Questo esempio aggiorna una voce ACL con autorizzazione di scrittura.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Per impostare l'ACL di un gruppo o di un utente specifico, utilizzare i rispettivi ID oggetto. Ad esempio, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` o `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Per un esempio in cui gli ACL vengono aggiornati in modo ricorsivo in batch specificando una dimensione del batch, vedere l'articolo di riferimento [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

## <a name="remove-acl-entries"></a>Rimuovi voci ACL

Questa sezione illustra come eseguire le operazioni seguenti:

- Rimuovere una voce ACL
- Rimuovi le voci ACL in modo ricorsivo

### <a name="remove-an-acl-entry"></a>Rimuovere una voce ACL

In questo esempio viene rimossa una voce da un ACL esistente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Rimuovi le voci ACL in modo ricorsivo

È possibile rimuovere una o più voci ACL in modo ricorsivo. Per rimuovere una voce ACL, creare un nuovo oggetto ACL per la voce ACL da rimuovere, quindi utilizzare l'oggetto in Rimuovi operazione ACL. Non ottenere l'ACL esistente, fornire solo le voci ACL da rimuovere.

Rimuovere le voci ACL usando il cmdlet **Remove-AzDataLakeGen2AclRecursive** .

In questo esempio viene rimossa una voce ACL dalla directory radice del contenitore.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se si vuole rimuovere una voce ACL **predefinita** , usare il parametro **-DefaultScope** quando si esegue il comando **set-AzDataLakeGen2ItemAclObject** . Ad esempio: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Per un esempio in cui gli ACL vengono rimossi in modo ricorsivo in batch specificando una dimensione del batch, vedere l'articolo di riferimento [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

## <a name="recover-from-failures"></a>Ripristino da errori

Potrebbero verificarsi errori di runtime o di autorizzazione quando si modificano gli ACL in modo ricorsivo. 

Per gli errori di runtime, riavviare il processo dall'inizio. Possono verificarsi errori di autorizzazione se l'entità di sicurezza non dispone di autorizzazioni sufficienti per modificare l'ACL di una directory o di un file che si trova nella gerarchia di directory da modificare. Risolvere il problema di autorizzazione, quindi scegliere di riprendere il processo dal punto in cui si è verificato l'errore usando un token di continuazione oppure riavviare il processo dall'inizio. Non è necessario usare il token di continuazione se si preferisce riavviare dall'inizio. È possibile riapplicare le voci ACL senza alcun impatto negativo.

In questo esempio vengono restituiti i risultati alla variabile, quindi le voci non riuscite di pipe in una tabella formattata.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

In base all'output della tabella, è possibile correggere eventuali errori di autorizzazione e quindi riprendere l'esecuzione usando il token di continuazione.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

Se si desidera che il processo venga completato senza interruzioni da errori di autorizzazione, è possibile specificarlo.

In questo esempio viene usato il `ContinueOnFailure` parametro in modo che l'esecuzione continui anche se l'operazione rileva un errore di autorizzazione. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Per vedere un esempio che imposta gli ACL in modo ricorsivo nei batch specificando una dimensione del batch, vedere l'articolo di riferimento [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Vedi anche

- [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage)
- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
