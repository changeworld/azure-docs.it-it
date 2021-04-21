---
title: Abilitare l'autenticazione di Servizi di dominio Active Directory per le condivisioni file di Azure
description: Informazioni su come abilitare l'Active Directory Domain Services tramite SMB per le condivisioni file di Azure. Le macchine virtuali Windows appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando le credenziali di Servizi di dominio Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 7187563824fd7e371d6352510b9ab71c920fc1ef
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835111"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Prima parte: abilitare l'autenticazione di Servizi di dominio Active Directory per le condivisioni file di Azure 

Prima di abilitare Active Directory Domain Services(AD DS), assicurarsi di aver [](storage-files-identity-auth-active-directory-enable.md) letto l'articolo di panoramica per comprendere gli scenari e i requisiti supportati.

Questo articolo descrive il processo necessario per abilitare l'autenticazione Active Directory Domain Services (AD DS) nell'account di archiviazione. Dopo aver abilitato la funzionalità, è necessario configurare l'account di archiviazione e Servizi di dominio Active Directory per usare le credenziali di Servizi di dominio Active Directory per l'autenticazione nella condivisione file di Azure. Per abilitare l'autenticazione di Servizi di dominio Active Directory tramite SMB per le condivisioni file di Azure, è necessario registrare l'account di archiviazione con Servizi di dominio Active Directory e quindi impostare le proprietà di dominio necessarie nell'account di archiviazione.

Per registrare l'account di archiviazione con Servizi di dominio Active Directory, creare un account che lo rappresenti in Servizi di dominio Active Directory. È possibile pensare a questo processo come se fosse come creare un account che rappresenta un'istanza di Windows file server in Servizi di dominio Active Directory. Quando la funzionalità è abilitata nell'account di archiviazione, si applica a tutte le condivisioni file nuove ed esistenti nell'account.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opzione 1 (scelta consigliata): usare il modulo AzFilesHybrid di PowerShell

I cmdlet nel modulo AzFilesHybrid di PowerShell apportano le modifiche necessarie e abilitano la funzionalità per l'utente. Poiché alcune parti dei cmdlet interagiscono con Servizi di dominio Active Directory locale, viene illustrato il funzionamento dei cmdlet, in modo da determinare se le modifiche sono allineate ai criteri di conformità e sicurezza e assicurarsi di disporre delle autorizzazioni appropriate per eseguire i cmdlet. Anche se è consigliabile usare il modulo AzFilesHybrid, se non è possibile eseguire questa operazione, vengono forniti i passaggi necessari per eseguirli manualmente.

### <a name="download-azfileshybrid-module"></a>Scaricare il modulo AzFilesHybrid

- [Scaricare e decomprimere il modulo AzFilesHybrid (modulo ga: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) Si noti che la crittografia Kerberos AES 256 è supportata nella versione 0.2.2 o successive. Se la funzionalità è stata abilitata con una versione AzFilesHybrid precedente alla versione 0.2.2 e si vuole eseguire l'aggiornamento per supportare la crittografia Kerberos AES 256, vedere questo [articolo.](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption) 
- Installare ed eseguire il modulo in un dispositivo aggiunto a un dominio di Servizi di dominio Active Directory locale con credenziali di Servizi di dominio Active Directory che dispongono delle autorizzazioni per creare un account di accesso al servizio o un account computer nell'Istanza di Active Directory di destinazione.
-  Eseguire lo script usando una credenziale di Servizi di dominio Active Directory locale sincronizzata con il Azure AD. Le credenziali di Servizi di dominio Active Directory locali devono avere il proprietario dell'account di archiviazione o le autorizzazioni del ruolo di Azure collaboratore.

### <a name="run-join-azstorageaccountforauth"></a>Eseguire Join-AzStorageAccountForAuth

Il `Join-AzStorageAccountForAuth` cmdlet esegue l'equivalente di un aggiunta a un dominio offline per conto dell'account di archiviazione specificato. Lo script usa il cmdlet per creare un [account computer](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) nel dominio di Active Directory. Se per qualsiasi motivo non è possibile usare un account computer, è possibile modificare lo script per creare un [account di accesso al](/windows/win32/ad/about-service-logon-accounts) servizio. Se si sceglie di eseguire il comando manualmente, è necessario selezionare l'account più adatto per l'ambiente.

L'account di Servizi di dominio Active Directory creato dal cmdlet rappresenta l'account di archiviazione. Se l'account di Servizi di dominio Active Directory viene creato in un'unità organizzativa che impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password. Se non si aggiorna la password dell'account prima di tale data, si verificano errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per informazioni su come aggiornare la password, vedere Aggiornare la password [dell'account di Servizi di dominio Active Directory.](storage-files-identity-ad-ds-update-password.md)

Sostituire i valori segnaposto con i propri nei parametri seguenti prima di eseguirlo in PowerShell.
> [!IMPORTANT]
> Il cmdlet di aggiunta al dominio creerà un account AD per rappresentare l'account di archiviazione (condivisione file) in AD. È possibile scegliere di eseguire la registrazione come account computer o account di accesso al servizio. Per [informazioni](./storage-files-faq.md#security-authentication-and-access-control) dettagliate, vedere Domande frequenti. Per gli account computer, è impostata una validità di scadenza della password predefinita in Active Directory a 30 giorni. Analogamente, l'account di accesso al servizio può avere una validità di scadenza della password predefinita impostata nel dominio di Active Directory o nell'unità organizzativa (OU).
> Per entrambi i tipi di account, è consigliabile controllare la scadenza della password configurata nell'ambiente AD e pianificare l'aggiornamento della password dell'identità [dell'account](storage-files-identity-ad-ds-update-password.md) di archiviazione dell'account AD prima della validità massima della password. È possibile prendere in considerazione la creazione di una nuova unità organizzativa [(OU)](/powershell/module/addsadministration/new-adorganizationalunit) di Active Directory in AD e la disabilitazione dei criteri di scadenza delle password per gli [account computer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) o gli account di accesso al servizio di conseguenza. 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters, $StorageAccountName currently has a maximum limit of 15 characters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opzione 2: eseguire manualmente le azioni di abilitazione

Se lo script precedente è già stato eseguito `Join-AzStorageAccountForAuth` correttamente, passare alla sezione [Confermare che la funzionalità è abilitata.](#confirm-the-feature-is-enabled) Non è necessario eseguire i passaggi manuali seguenti.

### <a name="checking-environment"></a>Controllo dell'ambiente

Prima di tutto, è necessario controllare lo stato dell'ambiente. In particolare, è necessario verificare [se Active Directory PowerShell](/powershell/module/addsadministration/) è installato e se la shell viene eseguita con privilegi di amministratore. Verificare quindi se è installato il [modulo Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0), se non lo è installarlo. Dopo aver completato questi controlli, controllare in Servizi di dominio Active Directory se è presente un [account computer](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (impostazione predefinita) o un [account](/windows/win32/ad/about-service-logon-accounts) di accesso al servizio già creato con SPN/UPN come "cifs/your-storage-account-name-here.file.core.windows.net". Se l'account non esiste, crearne uno come descritto nella sezione seguente.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Creazione manuale di un'identità che rappresenta l'account di archiviazione in Active Directory

Per creare manualmente questo account, creare una nuova chiave Kerberos per l'account di archiviazione. Usare quindi la chiave Kerberos come password per l'account con i cmdlet di PowerShell riportati di seguito. Questa chiave viene usata solo durante l'installazione e non può essere usata per operazioni di controllo o piano dati sull'account di archiviazione. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Dopo aver creato la chiave, creare un account del servizio o del computer nell'unità organizzativa. Usare la specifica seguente (ricordarsi di sostituire il testo di esempio con il nome dell'account di archiviazione):

SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Password: chiave Kerberos per l'account di archiviazione.

Se l'unità organizzativa impone la scadenza della password, è necessario aggiornare la password prima della validità massima della password per evitare errori di autenticazione durante l'accesso alle condivisioni file di Azure. Per [informazioni dettagliate, vedere Aggiornare la password dell'identità dell'account](storage-files-identity-ad-ds-update-password.md) di archiviazione in Active Directory.

Mantenere il SID dell'identità appena creata. Sarà necessario per il passaggio successivo. L'identità creata che rappresenta l'account di archiviazione non deve essere sincronizzata con Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Abilitare la funzionalità nell'account di archiviazione

È ora possibile abilitare la funzionalità nell'account di archiviazione. Specificare alcuni dettagli di configurazione per le proprietà del dominio nel comando seguente, quindi eseguirlo. Il SID dell'account di archiviazione richiesto nel comando seguente è il SID dell'identità creata in Servizi di dominio Active [Directory nella sezione precedente.](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Debug

È possibile eseguire il cmdlet Debug-AzStorageAccountAuth per eseguire un set di controlli di base sulla configurazione di ACTIVE Directory con l'utente ad Active Directory connesso. Questo cmdlet è supportato nella versione AzFilesHybrid v 0.1.2+. Per altre informazioni sui controlli eseguiti in questo cmdlet, vedere [Unable to mount File di Azure with AD credentials](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) nella guida alla risoluzione dei problemi per Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Verificare che la funzionalità sia abilitata

È possibile verificare se la funzionalità è abilitata nell'account di archiviazione con lo script seguente:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

In caso di esito positivo, l'output sarà simile al seguente:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Passaggi successivi

A questo punto la funzionalità è stata abilitata nell'account di archiviazione. Per usare la funzionalità, è necessario assegnare autorizzazioni a livello di condivisione. Passare alla sezione successiva.

[Seconda parte: assegnare autorizzazioni a livello di condivisione a un'identità](storage-files-identity-ad-ds-assign-permissions.md)