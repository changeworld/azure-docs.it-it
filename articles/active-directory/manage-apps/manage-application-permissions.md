---
title: Gestire le autorizzazioni di utenti e amministratori - Azure Active Directory | Microsoft Docs
description: Informazioni su come esaminare e gestire le autorizzazioni per l'applicazione in Azure AD. Ad esempio, revocare tutte le autorizzazioni concesse a un'applicazione.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373979"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Intervenire su applicazioni sospette o con privilegi in Azure Active Directory

Informazioni su come esaminare e gestire le autorizzazioni dell'applicazione. Questo articolo illustra diverse azioni che è possibile eseguire per proteggere l'applicazione in base allo scenario. Queste azioni si applicano a tutte le applicazioni aggiunte al tenant Azure Active Directory (Azure AD) tramite il consenso dell'utente o dell'amministratore.

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le azioni seguenti, è necessario accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud.

Per limitare l'accesso alle applicazioni è necessario richiedere l'assegnazione dell'utente, quindi assegnare utenti o gruppi all'applicazione.  Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](./assign-user-or-group-access-portal.md).

È possibile accedere al portale Azure AD per ottenere script di PowerShell contestuali per eseguire le azioni.
 
1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali.**
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Autorizzazioni**. Nella barra dei comandi selezionare **Rivedi autorizzazioni.**

![Screenshot della finestra di verifica delle autorizzazioni.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Controllare l'accesso a un'applicazione

È consigliabile limitare l'accesso all'applicazione attivando **l'impostazione Assegnazione** utente.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali.**
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi impostare **Requisito utente richiesto** su **Sì.**
5. Selezionare **Utenti e gruppi** e quindi rimuovere gli utenti indesiderati assegnati all'applicazione.
6. Assegnare utenti o gruppi all'applicazione.

Facoltativamente, è possibile rimuovere tutti gli utenti assegnati all'applicazione tramite PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Revocare tutte le autorizzazioni per un'applicazione

L'uso dello script di PowerShell revoca tutte le autorizzazioni concesse a questa applicazione.

> [!NOTE]
> La revoca dell'autorizzazione concessa corrente non impedirà agli utenti di acconsentire nuovamente all'applicazione. Per impedire agli utenti di ottenere il consenso, vedere Configurare il modo in cui [gli utenti conseggono le applicazioni](configure-user-consent.md).

Facoltativamente, è possibile disabilitare l'applicazione per evitare che gli utenti possano accedere all'app e per evitare che l'applicazione possa accedere ai dati.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni Enterprise.**
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi impostare **Abilitato per l'accesso degli utenti?** su **No.**

## <a name="investigate-a-suspicious-application"></a>Analizzare un'applicazione sospetta

È consigliabile limitare l'accesso all'applicazione attivando **l'impostazione Assegnazione** utente. Esaminare quindi le autorizzazioni concesse all'applicazione da utenti e amministratori.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
3. Selezionare **Azure Active Directory**  >  **applicazioni Aziendali.**
5. Selezionare l'applicazione a cui si vuole limitare l'accesso.
6. Selezionare **Proprietà** e quindi impostare **Requisito utente richiesto** su **Sì.**
7. Selezionare **Autorizzazioni** ed esaminare le autorizzazioni concesse dall'amministratore e dall'utente.

Facoltativamente, usando PowerShell è possibile:

- Rimuovere tutti gli utenti assegnati per impedire loro di accedere all'applicazione.
- Invalidare i token di aggiornamento per gli utenti che hanno accesso all'applicazione.
- Revocare tutte le autorizzazioni per l'applicazione.

Oppure è possibile disabilitare l'applicazione per bloccare l'accesso degli utenti e arrestare l'accesso dell'applicazione ai dati.


## <a name="disable-a-malicious-application"></a>Disabilitare un'applicazione dannosa 

È consigliabile disabilitare l'applicazione per bloccare l'accesso degli utenti e impedire all'applicazione di accedere ai dati. Se invece si elimina l'applicazione, gli utenti possono acconsentire nuovamente all'applicazione e concedere l'accesso ai dati.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali.**
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi copiare l'ID oggetto.

### <a name="powershell-commands"></a>Comandi di PowerShell


Recuperare l'ID oggetto entità servizio.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali.**
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi copiare l'ID oggetto.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Rimuovere tutti gli utenti assegnati all'applicazione.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Revocare le autorizzazioni concesse all'applicazione.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
Invalidare i token di aggiornamento.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Passaggi successivi
- [Gestire il consenso alle applicazioni e valutare la richiesta di consenso](manage-consent-requests.md)
- [Configurare il consenso utente](configure-user-consent.md)
- [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
