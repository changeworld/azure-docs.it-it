---
title: Rimuovere le assegnazioni di ruolo da un gruppo in Azure Active Directory | Microsoft Docs
description: Anteprima dei ruoli di Azure AD personalizzati per la delega della gestione delle identità. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ed23f563fce9760768a99e5bbf58f68500d665
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012021"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Rimuovere le assegnazioni di ruolo da un gruppo in Azure Active Directory

Questo articolo descrive come un amministratore IT può rimuovere Azure AD ruoli assegnati ai gruppi. Nel portale di Azure è ora possibile rimuovere le assegnazioni di ruolo dirette e indirette a un utente. Se a un utente viene assegnato un ruolo da un'appartenenza a un gruppo, rimuovere l'utente dal gruppo per rimuovere l'assegnazione di ruolo.

## <a name="using-azure-admin-center"></a>Uso dell'interfaccia di amministrazione di Azure

1. Accedere al centro di [amministrazione di Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.

1. Selezionare **ruoli e amministratori**  >  **_nome ruolo_**.

1. Selezionare il gruppo da cui si desidera rimuovere l'assegnazione di ruolo e selezionare **Rimuovi assegnazione**.

   ![Rimuovere un'assegnazione di ruolo da un gruppo selezionato.](./media/groups-remove-assignment/remove-assignment.png)

1. Quando viene richiesto di confermare l'azione, selezionare **Sì**.

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creare un gruppo che può essere assegnato al ruolo

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Ottenere la definizione di ruolo a cui si vuole assegnare il gruppo

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Rimuovere l'assegnazione di ruolo

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Creare un gruppo a cui è possibile assegnare un ruolo Azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Ottenere la definizione del ruolo

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Creare l'assegnazione di ruolo

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Elimina assegnazione ruolo

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi cloud per gestire le assegnazioni di ruoli](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
