---
title: Assegnare ruoli personalizzati usando Azure AD PowerShell-Azure AD | Microsoft Docs
description: Gestire i membri di un ruolo personalizzato amministratore di Azure AD con Azure AD PowerShell.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0fb81a4daa57b473e8b2b4b937426eafbf903d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014537"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Assegnare ruoli personalizzati con ambito di risorse usando PowerShell in Azure Active Directory

Questo articolo descrive come creare un'assegnazione di ruolo a livello di organizzazione in Azure Active Directory (Azure AD). L'assegnazione di un ruolo a livello di organizzazione concede l'accesso all'intera organizzazione Azure AD. Per creare un'assegnazione di ruolo con un ambito di una singola risorsa di Azure AD, vedere [come creare un ruolo personalizzato e assegnarlo all'ambito della risorsa](custom-create.md). Questo articolo usa il modulo [Azure Active Directory PowerShell versione 2](/powershell/module/azuread/#directory_roles) .

Per ulteriori informazioni sui ruoli di amministratore di Azure AD, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Connettersi all'organizzazione Azure AD usando un account di amministratore globale per assegnare o rimuovere i ruoli.

## <a name="prepare-powershell"></a>Preparare PowerShell

Installare il modulo Azure AD PowerShell dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview). Importare quindi il modulo di anteprima di Azure AD PowerShell usando questo comando:

``` PowerShell
Import-Module -Name AzureADPreview
```

Per verificare che il modulo sia pronto per l'uso, la versione restituita dal comando seguente deve corrispondere a quella elencata qui:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADMSAdministrati...}
```

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa dei cmdlet nel modulo Azure AD, vedere la documentazione di riferimento online per il [modulo Azure ad Preview](https://www.powershellgallery.com/packages/AzureADPreview).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Assegnare un ruolo della directory a un utente o a un'entità servizio con ambito di risorsa

1. Caricare il modulo Azure AD PowerShell (anteprima).
1. Eseguire l'accesso eseguendo il comando `Connect-AzureAD` .
1. Creare un nuovo ruolo usando lo script di PowerShell seguente.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Per assegnare il ruolo a un'entità servizio anziché a un utente, usare il cmdlet [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

## <a name="role-definitions"></a>Definizioni dei ruoli

Gli oggetti di definizione dei ruoli contengono la definizione del ruolo predefinito o personalizzato, insieme alle autorizzazioni concesse da tale assegnazione di ruolo. Questa risorsa consente di visualizzare le definizioni di ruolo personalizzate e i ruoli della directory predefiniti (che vengono visualizzati in formato equivalente roleDefinition). Attualmente, un'organizzazione Azure AD può avere un massimo di 30 definizioni di ruolo personalizzate univoche definite.

### <a name="create-a-role-definition"></a>Creare una definizione di ruolo

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Lettura ed elenco di definizioni di ruolo

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Aggiornare una definizione di ruolo

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Eliminare una definizione di ruolo

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Assegnazioni di ruoli

Le assegnazioni di ruolo contengono informazioni che collegano una determinata entità di sicurezza (un'entità servizio dell'utente o dell'applicazione) a una definizione di ruolo. Se necessario, è possibile aggiungere un ambito di una singola risorsa Azure AD per le autorizzazioni assegnate.  La limitazione dell'ambito di un'assegnazione di ruolo è supportata per i ruoli predefiniti e personalizzati.

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Leggere ed elencare le assegnazioni di ruolo

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Eliminare un'assegnazione di ruolo

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Passaggi successivi

- Condividi con noi nel [Forum sui ruoli amministrativi Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Per ulteriori informazioni sui ruoli e sulle assegnazioni di ruolo di amministratore di Azure AD, vedere [assegnare ruoli di amministratore](permissions-reference.md)
- Per le autorizzazioni utente predefinite, vedere [confronto tra le autorizzazioni utente Guest e membro predefinito](../fundamentals/users-default-permissions.md)
