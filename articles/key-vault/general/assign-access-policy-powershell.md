---
title: Assegnare un criterio Azure Key Vault di accesso
description: Come usare l'portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell per assegnare un criterio Key Vault di accesso a un'entità di sicurezza o a un'identità dell'applicazione.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751419"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Assegnare un Key Vault di accesso tramite Azure PowerShell

I Key Vault di accesso di sicurezza determinano se un'entità di sicurezza specifica, in modo [](../secrets/index.yml)specifico [](../keys/index.yml)un utente, un'applicazione o un gruppo di utenti, può eseguire operazioni diverse su segreti, chiavi e certificati di [Key Vault.](../certificates/index.yml) È possibile assegnare i criteri di accesso usando il [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md)o Azure PowerShell (questo articolo).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per altre informazioni sulla creazione di gruppi in Azure Active Directory usando Azure PowerShell, vedere [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) e [Add-AzADGroupMember.](/powershell/module/az.resources/add-azadgroupmember)

## <a name="configure-powershell-and-sign-in"></a>Configurare PowerShell e accedere

1. Per eseguire comandi in locale, [installare Azure PowerShell,](/powershell/azure/) se non è già stato fatto.

    Per eseguire i comandi direttamente nel cloud, usare il [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Solo PowerShell locale:

    1. Installare il [modulo Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Accedere ad Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Acquisire l'ID oggetto

Determinare l'ID oggetto dell'applicazione, del gruppo o dell'utente a cui si vogliono assegnare i criteri di accesso:

- Applicazioni e altre entità servizio: usare il cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) con il parametro per filtrare i risultati in base al nome `-SearchString` dell'entità servizio desiderata:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Gruppi: usare il cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) con il parametro per filtrare i risultati `-SearchString` in base al nome del gruppo desiderato:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Nell'output l'ID oggetto è elencato come `Id` .

- Utenti: usare il cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser) passando l'indirizzo di posta elettronica dell'utente al `-UserPrincipalName` parametro .

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Nell'output l'ID oggetto è elencato come `Id` .

## <a name="assign-the-access-policy"></a>Assegnare i criteri di accesso

Usare il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per assegnare i criteri di accesso:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

È necessario includere solo `-PermissionsToSecrets` , e quando si `-PermissionsToKeys` `-PermissionsToCertificates` assegnano autorizzazioni a questi tipi specifici. I valori consentiti per `<secret-permissions>` , e sono specificati nella documentazione `<key-permissions>` `<certificate-permissions>` [set-AzKeyVaultAccessPolicy - Parameters.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](security-overview.md#identity-management)
- [Proteggere l'insieme di credenziali delle chiavi.](security-overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)