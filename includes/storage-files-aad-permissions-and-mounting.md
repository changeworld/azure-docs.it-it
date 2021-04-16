---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512835"
---
## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Per accedere File di Azure risorse con autenticazione basata su identità, un'identità (un utente, un gruppo o un'entità servizio) deve disporre delle autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni di condivisione di Windows, in cui si specifica il tipo di accesso di un determinato utente a una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file. 

Sono stati introdotti tre ruoli predefiniti di Azure per la concessione di autorizzazioni a livello di condivisione agli utenti:

- **Il lettore di condivisione SMB** dei file di archiviazione consente l'accesso in lettura Archiviazione di Azure condivisioni file su SMB.
- **Il collaboratore alla condivisione SMB** dei file di archiviazione consente l'accesso in lettura, scrittura ed eliminazione Archiviazione di Azure condivisioni file su SMB.
- **Il collaboratore con** privilegi elevati della condivisione SMB dei file di archiviazione consente di leggere, scrivere, eliminare e modificare le autorizzazioni NTFS Archiviazione di Azure condivisioni file su SMB.

> [!IMPORTANT]
> Il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assumere la proprietà di un file, richiede l'uso della chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per assegnare i ruoli predefiniti all'identità Azure AD di un utente per concedere autorizzazioni a livello di condivisione. Tenere presente che l'assegnazione di ruolo di Azure a livello di condivisione può richiedere del tempo. 

> [!NOTE]
> Ricordarsi [di sincronizzare le](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) credenziali di Servizi di dominio Active Directory Azure AD se si prevede di usare Servizi di dominio Active Directory locale per l'autenticazione. La sincronizzazione dell'hash delle password da Servizi di dominio Active Directory Azure AD è facoltativa. L'autorizzazione a livello di condivisione verrà concessa Azure AD'identità sincronizzata da Servizi di dominio Active Directory locale.

È consigliabile usare l'autorizzazione a livello di condivisione per la gestione degli accessi di alto livello a un gruppo di Active Directory che rappresenta un gruppo di utenti e identità, quindi sfruttare le autorizzazioni NTFS per il controllo granulare degli accessi a livello di directory/file. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Assegnare un ruolo di Azure a un'identità di AD

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per assegnare un ruolo di Azure a un'identità Azure AD, [usando](https://portal.azure.com)il portale di Azure , seguire questa procedura:

1. Nel portale di Azure passare alla condivisione file o [Creare una condivisione file](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi un'assegnazione di ruolo**
4. Nel pannello **Aggiungi assegnazione di** ruolo selezionare il ruolo predefinito appropriato (Lettore condivisione SMB dei file di archiviazione, Collaboratore condivisione SMB dei file di archiviazione) dall'elenco Ruolo.  Lasciare **Assegna l'accesso a** per l'impostazione predefinita: Azure AD **utente, gruppo o entità servizio.** Selezionare l'identità Azure AD destinazione in base al nome o all'indirizzo di posta elettronica.
5. Selezionare **Salva per** completare l'operazione di assegnazione del ruolo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

L'esempio di PowerShell seguente illustra come assegnare un ruolo di Azure a un'identità Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con PowerShell, vedere Gestire l'accesso tramite il controllo degli accessi [in base al ruolo e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi quadre, con valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
  
Il comando seguente dell'interfaccia della riga di comando 2.0 illustra come assegnare un ruolo di Azure a un'identità Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con l'interfaccia della riga di comando di Azure, vedere Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'interfaccia della [riga di comando di Azure.](../articles/role-based-access-control/role-assignments-cli.md) 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, incluse le parentesi quadre, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Configurare le autorizzazioni NTFS tramite SMB

Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Si pensi alle autorizzazioni a livello di condivisione come al gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Mentre le autorizzazioni NTFS agiscono a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file.

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per directory e file in una condivisione file di Azure montando la condivisione e quindi usando Windows Esplora file o eseguendo il [comando icacls](/windows-server/administration/windows-commands/icacls) o [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) di Windows. 

Per configurare NTFS con autorizzazioni utente avanzato, è necessario montare la condivisione usando la chiave dell'account di archiviazione dalla macchina virtuale aggiunto al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare le autorizzazioni NTFS di conseguenza.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio di condivisioni file, vedere [Usare una condivisione file di Azure con Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se si verificano problemi di connessione a File di Azure, vedere lo strumento di risoluzione dei problemi pubblicato per File di Azure errori di [montaggio in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vengono inoltre fornite [indicazioni per](../articles/storage/files/storage-files-faq.md#on-premises-access) risolvere gli scenari in cui la porta 445 è bloccata. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurare le autorizzazioni NTFS con Windows Esplora file

Usare Windows Esplora file per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Windows Esplora file fare clic con il pulsante destro del mouse sul file o sulla directory e scegliere **Proprietà**.
2. Selezionare la scheda **Sicurezza**.
3. Selezionare **Modifica.** per modificare le autorizzazioni.
4. È possibile modificare le autorizzazioni degli utenti esistenti o selezionare **Aggiungi...** per concedere le autorizzazioni ai nuovi utenti.
5. Nella finestra di richiesta per l'aggiunta di nuovi utenti immettere  il nome utente di destinazione a cui si vuole concedere l'autorizzazione nella casella Immettere i nomi degli oggetti da selezionare e selezionare **Controlla** nomi per trovare il nome UPN completo dell'utente di destinazione.
7.    Selezionare **OK**.
8.    Nella scheda **Sicurezza** selezionare tutte le autorizzazioni da concedere al nuovo utente.
9.    Selezionare **Applica**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls

Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per altre informazioni su come usare icacl per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere le informazioni di riferimento sulla riga di comando [per icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montare una condivisione file da una macchina virtuale aggiunta a dominio

Il processo seguente verifica che la condivisione file e le autorizzazioni di accesso siano state configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale appartenente a un dominio. Tenere presente che l'assegnazione di ruolo di Azure a livello di condivisione può richiedere del tempo. 

Accedere alla macchina virtuale usando l'identità Azure AD a cui sono state concesse le autorizzazioni, come illustrato nell'immagine seguente. Se è stata abilitata l'autenticazione di Servizi di dominio Active Directory locale per File di Azure, usare le credenziali di Servizi di dominio Active Directory. Per Azure AD DS autenticazione, accedere con Azure AD credenziali.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Usare il comando seguente per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto con valori personalizzati. Poiché si è stati autenticati, non è necessario fornire la chiave dell'account di archiviazione, le credenziali di Servizi di dominio Active Directory locali o le credenziali Azure AD DS credenziali. L'esperienza Single Sign-On è supportata per l'autenticazione con Servizi di dominio Active Directory locale o Azure AD DS. Se si verificano problemi di montaggio con le credenziali di Servizi di dominio [Active Directory, vedere](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) Risolvere File di Azure problemi in Windows per istruzioni.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```
