---
title: Usare Azure AD Domain Services per autorizzare l'accesso ai dati dei file tramite SMB
description: Informazioni su come abilitare l'autenticazione basata su identità su Server Message Block (SMB) per File di Azure tramite Azure Active Directory Domain Services. Le macchine virtuali (VM) Windows appartenenti a un dominio possono quindi accedere alle condivisioni file di Azure usando Azure AD credenziali.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: e82ac4e1fdb8e5f88c9d83924da50b1ff5c659cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777924"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Abilitare Azure Active Directory Domain Services'autenticazione in File di Azure

[File di Azure](storage-files-introduction.md)   supporta l'autenticazione basata su identità su Server Message Block (SMB) tramite due tipi di Servizi di dominio: Active Directory locale Domain Services (AD DS) e Azure Active Directory Domain Services (Azure AD DS). È consigliabile esaminare la sezione [Funzionamento](./storage-files-active-directory-overview.md#how-it-works) per selezionare il servizio di dominio più opportuno per l'autenticazione. La configurazione è diversa a seconda del servizio di dominio scelto. Questo articolo è in particolare sull'abilitazione e la configurazione di Azure AD DS per l'autenticazione con le condivisioni file di Azure.

Se non si ha di nuovo una condivisione file di Azure, è consigliabile leggere la guida alla [pianificazione](storage-files-planning.md) prima di leggere la serie di articoli seguenti.

> [!NOTE]
> File di Azure supporta l'autenticazione Kerberos Azure AD DS solo con RC4-HMAC. La crittografia Kerberos AES non è ancora supportata.
> File di Azure supporta l'autenticazione per Azure AD DS con sincronizzazione completa con Azure AD. Se è stata abilitata la sincronizzazione con ambito in Azure AD DS che sincronizza solo un set limitato di identità da Azure AD, l'autenticazione e l'autorizzazione non sono supportate.

## <a name="prerequisites"></a>Prerequisiti

Prima di abilitare Azure AD su SMB per le condivisioni file di Azure, assicurarsi di aver completato i prerequisiti seguenti:

1.  **Selezionare o creare un tenant di Azure AD.**

    È possibile usare un tenant nuovo o esistente per l'autenticazione di Azure AD tramite SMB. Il tenant e la condivisione file a cui si vuole accedere devono essere associati alla stessa sottoscrizione.

    Per creare un nuovo tenant di Azure AD, è possibile [aggiungere un tenant di Azure AD e una sottoscrizione di Azure AD](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se si dispone di un tenant Azure AD ma si vuole creare un nuovo tenant da usare con le condivisioni file di Azure, vedere Creare un [tenant Azure Active Directory.](/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Abilitare Azure AD Domain Services nel tenant di Azure AD.**

    Per supportare l'autenticazione con credenziali di Azure AD, è necessario abilitare Azure AD Domain Services per il tenant di Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    In genere sono necessari circa 15 minuti per completare Azure AD DS distribuzione. Prima di procedere con il passaggio successivo, verificare che lo stato di integrità Azure AD DS sia In esecuzione **,** con la sincronizzazione dell'hash delle password abilitata.

1.  **Aggiungere a un dominio una macchina virtuale di Azure con Azure AD DS.**

    Per accedere a una condivisione file usando Azure AD credenziali da una macchina virtuale, la macchina virtuale deve essere unita a un dominio per Azure AD DS. Per altre informazioni su come aggiungere una macchina virtuale a un dominio, vedere [Aggiungere una macchina virtuale Windows Server a un dominio gestito](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS'autenticazione tramite SMB con condivisioni file di Azure è supportata solo nelle macchine virtuali di Azure in esecuzione in versioni del sistema operativo precedenti a Windows 7 o Windows Server 2008 R2.

1.  **Selezionare o creare una condivisione file di Azure.**

    Selezionare una condivisione file nuova o esistente associata alla stessa sottoscrizione del tenant di Azure AD. Per informazioni sulla creazione di una nuova condivisione file, vedere [Creare una condivisione file in File di Azure](storage-how-to-create-file-share.md).
    Per prestazioni ottimali, è consigliabile che la condivisione file si presenti nella stessa area della macchina virtuale da cui si prevede di accedere alla condivisione.

1.  **Verificare la connettività di File di Azure tramite il montaggio di condivisioni file di Azure usando la chiave dell'account di archiviazione.**

    Per verificare che la macchina virtuale e la condivisione file siano configurate correttamente, provare a montare la condivisione file usando la chiave dell'account di archiviazione. Per altre informazioni, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilità a livello di area

File di Azure'autenticazione con Azure AD DS è disponibile in tutte le aree [Pubbliche, Gov e Cina di Azure.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview-of-the-workflow"></a>Panoramica del flusso di lavoro

Prima di abilitare l Azure AD DS Authentication over SMB per le condivisioni file di Azure, verificare che gli ambienti Azure AD e Archiviazione di Azure siano configurati correttamente. È consigliabile seguire i [prerequisiti](#prerequisites) per assicurarsi di aver completato tutti i passaggi necessari.

Eseguire quindi le operazioni seguenti per concedere l'accesso File di Azure risorse con Azure AD credenziali:

1. Abilitare Azure AD DS'autenticazione tramite SMB per l'account di archiviazione per registrare l'account di archiviazione con la distribuzione Azure AD DS di archiviazione associata.
2. Assegnare le autorizzazioni di accesso per una condivisione a un'identità di Azure AD (utente, gruppo o entità servizio).
3. Configurare le autorizzazioni NTFS su SMB per file e directory.
4. Montare una condivisione file di Azure da una macchina virtuale aggiunta a dominio.

Il diagramma seguente illustra il flusso di lavoro end-to-end per l'abilitazione dell'autenticazione Azure AD DS su SMB per File di Azure.

![Diagramma che mostra il flusso di lavoro per l'autenticazione di Azure AD tramite SMB per File di Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Abilitare Azure AD DS'autenticazione per l'account

Per abilitare Azure AD DS'autenticazione tramite SMB per File di Azure, è possibile impostare una proprietà negli account di archiviazione usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. L'impostazione di questa proprietà in modo implicito "aggiunge" l'account di archiviazione all'Azure AD DS distribuzione. Azure AD DS'autenticazione tramite SMB viene quindi abilitata per tutte le condivisioni file nuove ed esistenti nell'account di archiviazione.

Tenere presente che è possibile abilitare l'autenticazione Azure AD DS su SMB solo dopo aver distribuito Azure AD DS nel tenant Azure AD servizio. Per altre informazioni, vedere i [prerequisiti](#prerequisites).

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per abilitare Azure AD DS autenticazione tramite SMB con il portale di Azure [,](https://portal.azure.com)seguire questa procedura:

1. Nel portale di Azure passare all'account di archiviazione esistente o [creare un account di archiviazione](../common/storage-account-create.md).
1. Nella sezione **Impostazioni** selezionare **Configurazione**.
1. In **Identity-based access for file shares** (Accesso basato sull'identità per condivisioni file) impostare l'interruttore per Azure Active Directory Domain Service **(AAD DS)** su **Abilitato.**
1. Selezionare **Salva**.

L'immagine seguente illustra come abilitare l'Azure AD DS tramite SMB per l'account di archiviazione.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Screenshot del pannello di configurazione nell'account di archiviazione. I servizi Doman di Azure Active Directory sono abilitati." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare Azure AD DS'autenticazione tramite SMB con Azure PowerShell, installare il modulo Az più recente (2.4 o versione più recente) o il modulo Az.Storage (1.5 o versione più recente). Per altre informazioni sull'installazione di PowerShell, vedere Installare Azure PowerShell [in Windows con PowerShellGet.](/powershell/azure/install-Az-ps)

Per creare un nuovo account di archiviazione, chiamare [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)e quindi impostare il parametro **EnableAzureActiveDirectoryDomainServicesForFile** su **true.** Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con valori personalizzati. Se si usa il modulo di anteprima precedente, il parametro per abilitare la funzionalità è **EnableAzureFilesAadIntegrationForSMB.**

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Per abilitare questa funzionalità per gli account di archiviazione esistenti, usare il comando seguente:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per abilitare l Azure AD tramite SMB con l'interfaccia della riga di comando di Azure, installare la versione più recente dell'interfaccia della riga di comando (versione 2.0.70 o successiva). Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, [vedere Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)

Per creare un nuovo account di archiviazione, chiamare [az storage account create](/cli/azure/storage/account#az_storage_account_create)e impostare `--enable-files-aadds` la proprietà su **true**. Nell'esempio seguente ricordarsi di sostituire i valori segnaposto con valori personalizzati. Se si usa il modulo di anteprima precedente, il parametro per l'abilitazione delle funzionalità è **file-aad.**

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Per abilitare questa funzionalità per gli account di archiviazione esistenti, usare il comando seguente:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

A questo punto è stata abilitata Azure AD DS'autenticazione tramite SMB ed è stato assegnato un ruolo personalizzato che fornisce l'accesso a una condivisione file di Azure con un'Azure AD identità. Per concedere ad altri utenti l'accesso alla [](#assign-access-permissions-to-an-identity) condivisione file, seguire le istruzioni nelle sezioni Assegnare le autorizzazioni di accesso per l'uso di un'identità e Configurare le autorizzazioni [NTFS su SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui File di Azure e su come usare Azure AD su SMB, vedere queste risorse:

- [Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB](storage-files-active-directory-overview.md)
- [Domande frequenti](storage-files-faq.md)
