---
title: Elencare le assegnazioni di ruolo Azure AD
description: È ora possibile visualizzare e gestire i membri di un ruolo amministratore Azure Active Directory nell'interfaccia di amministrazione di Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467655"
---
# <a name="list-azure-ad-role-assignments"></a>Elencare le assegnazioni di ruolo Azure AD

Questo articolo descrive come elencare i ruoli assegnati in Azure Active Directory (Azure AD). In Azure Active Directory (Azure AD) i ruoli possono essere assegnati a un ambito a livello di organizzazione o con un ambito di applicazione singola.

- Le assegnazioni di ruolo a livello di organizzazione vengono aggiunte a e possono essere visualizzate nell'elenco delle singole assegnazioni di ruolo applicazione.
- Le assegnazioni di ruolo nell'ambito della singola applicazione non vengono aggiunte a e non possono essere visualizzate nell'elenco di assegnazioni con ambito a livello di organizzazione.

## <a name="list-role-assignments-in-the-azure-portal"></a>Elencare le assegnazioni di ruolo nel portale di Azure

In questa procedura viene descritto come elencare le assegnazioni di ruolo con ambito a livello di organizzazione.

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **Azure Active Directory**, selezionare **ruoli e amministratori**, quindi selezionare un ruolo per aprirlo e visualizzarne le proprietà.
1. Selezionare **assegnazioni** per elencare le assegnazioni di ruolo.

    ![Elencare le assegnazioni di ruolo e le autorizzazioni quando si apre un ruolo dall'elenco](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Elencare le assegnazioni di ruolo

È facile elencare anche le proprie autorizzazioni. Selezionare **Your Role** (Ruolo dell'utente) sulla pagina **Roles and administrators** (Ruoli e amministratori) per visualizzare i ruoli attualmente assegnati all'utente.

## <a name="download-role-assignments"></a>Scaricare le assegnazioni di ruolo

Per scaricare tutte le assegnazioni per un ruolo specifico, nella pagina **ruoli e amministratori** selezionare un ruolo e quindi selezionare **Scarica assegnazioni di ruolo**. Viene scaricato un file CSV in cui sono elencate le assegnazioni in tutti gli ambiti per tale ruolo.

![Scarica tutte le assegnazioni per un ruolo](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Elencare le assegnazioni di ruolo con Azure AD PowerShell

In questa sezione viene descritta la visualizzazione delle assegnazioni di un ruolo con ambito a livello di organizzazione. Questo articolo usa il modulo [Azure Active Directory PowerShell versione 2](/powershell/module/azuread/#directory_roles) . Per visualizzare le assegnazioni con ambito applicazione singola usando PowerShell, è possibile usare i cmdlet in [assegnare ruoli personalizzati con PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparare PowerShell

Per prima cosa, è necessario [scaricare il modulo PowerShell di Azure ad Preview](https://www.powershellgallery.com/packages/AzureAD/).

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>Elencare le assegnazioni di ruolo

Esempio di elenco delle assegnazioni di ruolo.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Elencare le assegnazioni di ruolo usando l'API Microsoft Graph

In questa sezione viene descritto come elencare le assegnazioni di ruolo con ambito a livello di organizzazione.  Per elencare le assegnazioni di ruolo con ambito applicazione singolo utilizzando API Graph, è possibile utilizzare le operazioni in [assegnare ruoli personalizzati con API Graph](custom-assign-graph.md).

Richiesta HTTP per ottenere un'assegnazione di ruolo per una definizione del ruolo specifica.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Risposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="list-role-assignments-with-single-application-scope"></a>Elencare le assegnazioni di ruolo con ambito di applicazione singola

In questa sezione viene descritto come elencare le assegnazioni di ruolo con ambito di applicazione singola. Questa funzionalità è attualmente in anteprima pubblica.

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **registrazioni app**, quindi selezionare la registrazione dell'app per visualizzarne le proprietà. Potrebbe essere necessario selezionare **Tutte le applicazioni** per visualizzare l'elenco completo delle registrazioni di app presenti nell'organizzazione di Azure AD.

    ![Creare o modificare le registrazioni di app dalla pagina Registrazioni app](./media/view-assignments/app-reg-all-apps.png)

1. Nella registrazione dell'app selezionare **ruoli e amministratori** e quindi selezionare un ruolo per visualizzarne le proprietà.

    ![Elencare le assegnazioni dei ruoli di registrazione delle app dalla pagina Registrazioni app](./media/view-assignments/app-reg-assignments.png)

1. Selezionare **assegnazioni** per elencare le assegnazioni di ruolo. L'apertura della pagina assegnazioni dall'interno della registrazione dell'app mostra le assegnazioni di ruolo che hanno come ambito questa risorsa Azure AD.

    ![Elencare le assegnazioni dei ruoli di registrazione delle app dalle proprietà di una registrazione dell'app](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](permissions-reference.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
