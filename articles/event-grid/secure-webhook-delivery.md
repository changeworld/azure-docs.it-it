---
title: Proteggere il recapito di webhook con Azure AD in Griglia di eventi di Azure
description: Viene descritto come recapitare eventi agli endpoint HTTPS protetti da Azure Active Directory tramite Griglia di eventi di Azure
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575548"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Pubblicare eventi per endpoint protetti di Azure Active Directory
Questo articolo descrive come usare Azure Active Directory (Azure AD) per proteggere la connessione  tra la sottoscrizione di eventi e l'endpoint **del webhook.** Per una panoramica delle Azure AD e delle entità servizio, vedere Panoramica di [Microsoft Identity Platform (v2.0).](../active-directory/develop/v2-overview.md)

Questo articolo usa il portale di Azure a scopo dimostrativo, ma la funzionalità può essere abilitata anche tramite l'interfaccia della riga di comando, PowerShell o gli SDK.

> [!IMPORTANT]
> Il controllo di accesso aggiuntivo è stato introdotto come parte della creazione o dell'aggiornamento della sottoscrizione di eventi il 30 marzo 2021 per risolvere una vulnerabilità di sicurezza. L'entità servizio del client sottoscrittore deve essere un proprietario o avere un ruolo assegnato nell'entità servizio dell'applicazione di destinazione. Riconfigurare l'applicazione AAD seguendo le nuove istruzioni riportate di seguito.


## <a name="create-an-azure-ad-application"></a>Creare un'Azure AD applicazione
Registrare il webhook con Azure AD creando un'applicazione Azure AD per l'endpoint protetto. Vedere [Scenario: API Web protetta.](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview) Configurare l'API protetta affinché venga chiamata da un'app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Abilitare Griglia di eventi per l'uso dell Azure AD appalto
Questa sezione illustra come abilitare Griglia di eventi per l'uso dell Azure AD app Azure AD eventi. 

> [!NOTE]
> Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

### <a name="connect-to-your-azure-tenant"></a>Connettersi al tenant di Azure
Prima di tutto, connettersi al tenant di Azure usando il `Connect-AzureAD` comando . 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Creare un'entità servizio Microsoft.EventGrid
Eseguire lo script seguente per creare l'entità servizio **per Microsoft.EventGrid,** se non esiste già. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Creare un ruolo per l'applicazione   
Eseguire lo script seguente per creare un ruolo per l'Azure AD applicazione. In questo esempio il nome del ruolo è **AzureEventGridSecureWebhookSubscriber.** Modificare lo script di PowerShell per usare l'ID tenant Azure AD e con `$myTenantId` l'ID oggetto dell'applicazione Azure AD `$myAzureADApplicationObjectId` dati

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Creare un'assegnazione di ruolo per la sottoscrizione di eventi di creazione client
L'assegnazione di ruolo deve essere creata nel webhook App Azure AD per l'app AAD o l'utente di AAD che crea la sottoscrizione di eventi. Usare uno degli script seguenti a seconda che un'app AAD o un utente di AAD crei la sottoscrizione di eventi.

> [!IMPORTANT]
> È stato introdotto un controllo di accesso aggiuntivo come parte della creazione o dell'aggiornamento della sottoscrizione di eventi il 30 marzo 2021 per risolvere una vulnerabilità di sicurezza. L'entità servizio del client sottoscrittore deve essere un proprietario o avere un ruolo assegnato nell'entità servizio dell'applicazione di destinazione. Riconfigurare l'applicazione AAD seguendo le nuove istruzioni riportate di seguito.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Creare un'assegnazione di ruolo per un'app AAD della sottoscrizione di eventi 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Creare un'assegnazione di ruolo per un utente di AAD della sottoscrizione di eventi 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Creare un'assegnazione di ruolo per l'entità servizio Griglia di eventi
Eseguire il New-AzureADServiceAppRoleAssignment comando per assegnare l'entità servizio Griglia di eventi al ruolo creato nel passaggio precedente.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Eseguire i comandi seguenti per ottenere informazioni che verranno usate in un secondo momento.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Configurare la sottoscrizione di eventi
Quando si crea una sottoscrizione di eventi, seguire questa procedura:

1. Selezionare il tipo di endpoint **come Web Hook.** 
1. Specificare **l'URI dell'endpoint.**

    ![Selezionare il webhook del tipo di endpoint](./media/secure-webhook-delivery/select-webhook.png)
1. Selezionare la **scheda Funzionalità** aggiuntive nella parte superiore della pagina Crea **sottoscrizioni eventi.**
1. Nella scheda **Funzionalità aggiuntive** seguire questa procedura:
    1. Selezionare **Usa autenticazione AAD** e configurare l'ID tenant e l'ID applicazione:
    1. Copiare Azure AD'ID tenant dall'output dello script e immetterlo nel campo **AAD Tenant ID (ID tenant AAD).**
    1. Copiare Azure AD'ID applicazione dall'output dello script e immetterlo nel campo **AAD Application ID (ID applicazione AAD).** In alternativa, è possibile usare l'URI ID applicazione AAD. Per altre informazioni sull'URI DELL'ID applicazione, vedere [questo articolo.](../app-service/configure-authentication-provider-aad.md)

        ![Usare l'azione Webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
