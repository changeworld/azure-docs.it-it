---
title: Distribuzione sicura dei webhook con Azure AD in griglia di eventi di Azure
description: Viene descritto come recapitare gli eventi agli endpoint HTTPS protetti da Azure Active Directory tramite griglia di eventi di Azure
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076037"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Pubblicare eventi per endpoint protetti di Azure Active Directory
Questo articolo descrive come usare Azure Active Directory (Azure AD) per proteggere la connessione tra la **sottoscrizione di eventi** e l' **endpoint del webhook**. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Questo articolo usa la portale di Azure per la dimostrazione, tuttavia la funzionalità può anche essere abilitata usando l'interfaccia della riga di comando, PowerShell o gli SDK.


## <a name="create-an-azure-ad-application"></a>Creare un'applicazione Azure AD
Registrare il webhook con Azure AD creando un'applicazione Azure AD per l'endpoint protetto. Vedere [scenario: API Web protetta](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Configurare l'API protetta affinché venga chiamata da un'app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Abilitare griglia di eventi per l'uso dell'applicazione Azure AD
Questa sezione illustra come abilitare griglia di eventi per l'uso dell'applicazione Azure AD. 

> [!NOTE]
> Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](../active-directory/roles/permissions-reference.md#all-roles).

### <a name="connect-to-your-azure-tenant"></a>Connettersi al tenant di Azure
Per prima cosa, connettersi al tenant di Azure usando il `Connect-AzureAD` comando. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Creare un'entità servizio Microsoft. EventGrid
Eseguire lo script seguente per creare l'entità servizio per **Microsoft. EventGrid** , se non esiste già. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Creazione di un ruolo per l'applicazione   
Eseguire lo script seguente per creare un ruolo per l'applicazione Azure AD. In questo esempio, il nome del ruolo è: **AzureEventGridSecureWebhookSubscriber**. Modificare gli script di PowerShell `$myTenantId` in modo da usare l'ID Tenant Azure ad e `$myAzureADApplicationObjectId` con l'ID oggetto dell'applicazione Azure ad

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
}
else
{      
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

### <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo
L'assegnazione di ruolo deve essere creata nel webhook App Azure AD per l'app AAD o l'utente AAD che crea la sottoscrizione dell'evento. Usare uno degli script seguenti a seconda che un'app AAD o un utente AAD crei la sottoscrizione di eventi.

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>Opzione A. creare un'assegnazione di ruolo per l'app AAD per la sottoscrizione di eventi 

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
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>Opzione B. creare un'assegnazione di ruolo per l'utente AAD della sottoscrizione di eventi 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Aggiungere un'entità servizio griglia di eventi al ruolo
Eseguire il comando New-AzureADServiceAppRoleAssignment per assegnare l'entità servizio griglia di eventi al ruolo creato nel passaggio precedente.

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Eseguire i comandi seguenti per restituire le informazioni che verranno usate in un secondo momento.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Configurare la sottoscrizione di eventi
Quando si crea una sottoscrizione di eventi, attenersi alla seguente procedura:

1. Selezionare il tipo di endpoint come **Web Hook**. 
1. Specificare l' **URI** dell'endpoint.

    ![Selezionare il webhook del tipo di endpoint](./media/secure-webhook-delivery/select-webhook.png)
1. Selezionare la scheda **funzionalità aggiuntive** nella parte superiore della pagina **crea sottoscrizioni di eventi** .
1. Nella scheda **funzionalità aggiuntive** eseguire le operazioni seguenti:
    1. Selezionare **Usa autenticazione di AAD** e configurare l'ID tenant e l'ID applicazione:
    1. Copiare l'ID tenant Azure AD dall'output dello script e immetterlo nel campo **ID tenant di AAD** .
    1. Copiare l'ID dell'applicazione Azure AD dall'output dello script e immetterlo nel campo dell' **ID applicazione di AAD** .

        ![Usare l'azione Webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
