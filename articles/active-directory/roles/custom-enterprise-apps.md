---
title: Creare ruoli personalizzati per gestire le app aziendali in Azure Active Directory
description: Creare e assegnare ruoli Azure AD personalizzati per l'accesso alle app aziendali in Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534708"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Creare ruoli personalizzati per gestire le app aziendali in Azure Active Directory

Questo articolo illustra come creare un ruolo personalizzato con autorizzazioni per gestire le assegnazioni di app aziendali per utenti e gruppi in Azure Active Directory (Azure AD). Per gli elementi delle assegnazioni di ruoli e il significato di termini come sottotipo, autorizzazione e set di proprietà, vedere la panoramica [dei ruoli personalizzati.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Autorizzazioni per il ruolo dell'app aziendale

In questo articolo sono descritte due autorizzazioni per le app aziendali. Tutti gli esempi usano l'autorizzazione di aggiornamento.

* Per leggere le assegnazioni di utenti e gruppi nell'ambito, concedere `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` l'autorizzazione
* Per gestire le assegnazioni di utenti e gruppi nell'ambito, concedere `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` l'autorizzazione

La concessione dell'autorizzazione di aggiornamento consente all'assegnatare di gestire le assegnazioni di utenti e gruppi alle app aziendali. L'ambito delle assegnazioni di utenti e/o gruppi può essere concesso per una singola applicazione o per tutte le applicazioni. Se concesso a livello di organizzazione, l'assegnatare può gestire le assegnazioni per tutte le applicazioni. Se viene effettuato a livello di applicazione, l'assegnatare può gestire le assegnazioni solo per l'applicazione specificata.

La concessione dell'autorizzazione di aggiornamento avviene in due passaggi:

1. Creare un ruolo personalizzato con autorizzazione `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Concedere a utenti o gruppi le autorizzazioni per gestire le assegnazioni di utenti e gruppi alle app aziendali. Questo è il momento in cui è possibile impostare l'ambito a livello di organizzazione o su una singola applicazione.

## <a name="use-the-azure-ad-admin-center"></a>Usare l'Azure AD di amministrazione

### <a name="create-a-new-custom-role"></a>Creare un nuovo ruolo personalizzato

>[!NOTE]
> I ruoli personalizzati vengono creati e gestiti a livello di organizzazione e sono disponibili solo nella pagina Panoramica dell'organizzazione.

1. Accedere all'interfaccia [di Azure AD con](https://aad.portal.azure.com) autorizzazioni di amministratore dei ruoli con privilegi o amministratore globale nell'organizzazione.
1. Selezionare **Azure Active Directory**, selezionare **Ruoli e amministratori** e quindi selezionare **Nuovo ruolo personalizzato**.

    ![Aggiungere un nuovo ruolo personalizzato dall'elenco dei ruoli in Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Nella **scheda** Informazioni di base specificare "Gestisci assegnazioni di utenti e gruppi" per il nome del ruolo e "Concedi autorizzazioni per gestire le assegnazioni di utenti e gruppi" per la descrizione del ruolo e quindi selezionare **Avanti.**

    ![Specificare un nome e una descrizione per il ruolo personalizzato](./media/custom-enterprise-apps/role-name-and-description.png)

1. Nella scheda **Autorizzazioni** immettere "microsoft.directory/servicePrincipals/appRoleAssignedTo/update" nella casella di ricerca, selezionare le caselle di controllo accanto alle autorizzazioni desiderate e quindi selezionare **Avanti.**

    ![Aggiungere le autorizzazioni al ruolo personalizzato](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Nella scheda **Rivedi e crea** rivedere le autorizzazioni e selezionare **Crea**.

    ![A questo punto è possibile creare il ruolo personalizzato](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Assegnare il ruolo a un utente usando il portale di Azure AD

1. Accedere all'interfaccia [di Azure AD con autorizzazioni](https://aad.portal.azure.com) di ruolo amministratore dei ruoli con privilegi.
1. Selezionare **Azure Active Directory** e quindi selezionare **Ruoli e amministratori**.
1. Selezionare il **ruolo Concedi autorizzazioni per gestire le assegnazioni di utenti e** gruppi.

    ![Aprire Ruoli e amministratori e cercare il ruolo personalizzato](./media/custom-enterprise-apps/select-custom-role.png)

1. Selezionare **Aggiungi assegnazione,** selezionare l'utente desiderato e quindi fare clic su **Seleziona** per aggiungere un'assegnazione di ruolo all'utente.

    ![Aggiungere un'assegnazione per il ruolo personalizzato all'utente](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Suggerimenti per l'assegnazione

* **Per** concedere agli utenti assegnati le autorizzazioni per gestire gli utenti e l'accesso  ai gruppi per tutte le app aziendali a livello di organizzazione, iniziare dall'elenco Ruoli e amministratori a livello di organizzazione nella pagina Panoramica di Azure AD per l'organizzazione.
* Per concedere agli utenti assegnati le autorizzazioni per gestire gli utenti e l'accesso ai gruppi  per un'app aziendale specifica, passare a tale app in Azure AD e aprire l'elenco Ruoli e amministratori per l'app. Selezionare il nuovo ruolo personalizzato e completare l'assegnazione di utenti o gruppi. Gli assegnatari possono gestire gli utenti e l'accesso ai gruppi solo per l'app specifica.
* Per testare l'assegnazione di ruolo personalizzata, accedere come  assegnatare e aprire la  pagina Utenti e gruppi di un'applicazione per verificare che l'opzione Aggiungi utente sia abilitata.

    ![Verificare le autorizzazioni utente](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Usare Azure AD PowerShell

Per altre informazioni, vedere [Creare e assegnare un ruolo personalizzato](custom-create.md) e Assegnare ruoli personalizzati con ambito risorsa usando [PowerShell.](custom-assign-powershell.md)

Installare innanzitutto il modulo Azure AD PowerShell [dal](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)PowerShell Gallery . Importare quindi il modulo di anteprima di Azure AD PowerShell usando questo comando:

```powershell
Import-Module -Name AzureADPreview
```

Per verificare che il modulo sia pronto per l'uso, la versione restituita dal comando seguente deve corrispondere a quella elencata qui:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Creare un nuovo ruolo con lo script PowerShell seguente:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Assegnare il ruolo personalizzato

Assegnare il ruolo usando questo script di PowerShell.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>Usare l'API Microsoft Graph

Creare un ruolo personalizzato usando l'esempio fornito nell'API Microsoft Graph. Per altre informazioni, vedere Creare e [assegnare un ruolo personalizzato](custom-create.md) e Assegnare ruoli di amministratore personalizzati usando l'API [Microsoft Graph .](custom-assign-graph.md)

Richiesta HTTP per creare il ruolo personalizzato.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Assegnare il ruolo personalizzato usando l'API Microsoft Graph

L'assegnazione di ruolo combina un ID entità di sicurezza (che può essere un utente o un'entità servizio), un ID definizione del ruolo e un ambito Azure AD risorsa. Per altre informazioni sugli elementi di un'assegnazione di ruolo, vedere la panoramica [dei ruoli personalizzati](custom-overview.md)

Richiesta HTTP per assegnare un ruolo personalizzato.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le autorizzazioni del ruolo personalizzato disponibili per le app aziendali](custom-enterprise-app-permissions.md)
