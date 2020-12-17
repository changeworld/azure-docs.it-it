---
title: Assegnare un'identità gestita a un ruolo applicazione usando PowerShell-Azure AD
description: Istruzioni dettagliate per l'assegnazione di un accesso a identità gestite al ruolo di un'altra applicazione tramite PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: 409ba7a954830bb2370ce83989b9e8b08b742fe7
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631177"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Assegnare un accesso a identità gestite a un ruolo applicazione tramite PowerShell

Le identità gestite per le risorse di Azure forniscono ai servizi di Azure un'identità in Azure Active Directory. Funzionano senza richiedere credenziali nel codice. I servizi di Azure usano questa identità per l'autenticazione ai servizi che supportano l'autenticazione Azure AD. I ruoli applicazione forniscono un tipo di controllo degli accessi in base al ruolo e consentono a un servizio di implementare regole di autorizzazione.

In questo articolo si apprenderà come assegnare un'identità gestita a un ruolo applicazione esposto da un'altra applicazione usando Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare il [Azure cloud Shell](../../cloud-shell/overview.md), che è possibile aprire usando il pulsante **prova** nell'angolo superiore destro dei blocchi di codice.
    - Eseguire gli script localmente installando la versione più recente di [Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Assegnare un accesso a identità gestite al ruolo app di un'altra applicazione

1. Abilitare l'identità gestita in una risorsa di Azure, [ad esempio una macchina virtuale di Azure](qs-configure-powershell-windows-vm.md).

1. Trovare l'ID oggetto dell'entità servizio dell'identità gestita.

   **Per un'identità gestita assegnata dal sistema**, è possibile trovare l'ID oggetto nella portale di Azure nella pagina **identità** della risorsa. È anche possibile usare lo script di PowerShell seguente per trovare l'ID oggetto. È necessario l'ID risorsa della risorsa creata nel passaggio 1, disponibile nella portale di Azure nella pagina delle **Proprietà** della risorsa.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Per un'identità gestita assegnata dall'utente**, è possibile trovare l'ID oggetto dell'identità gestita nella portale di Azure nella pagina **Panoramica** della risorsa. È anche possibile usare lo script di PowerShell seguente per trovare l'ID oggetto. È necessario l'ID risorsa dell'identità gestita assegnata dall'utente.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Creare una nuova registrazione dell'applicazione per rappresentare il servizio a cui l'identità gestita invierà una richiesta. Se l'API o il servizio che espone la concessione del ruolo app all'identità gestita ha già un'entità servizio nel tenant di Azure AD, ignorare questo passaggio. Se ad esempio si vuole concedere l'accesso all'identità gestita all'API di Microsoft Graph, è possibile ignorare questo passaggio.

1. Trovare l'ID oggetto dell'entità servizio dell'applicazione del servizio. È possibile trovarlo utilizzando il portale di Azure. Passare a Azure Active Directory e aprire la pagina **applicazioni aziendali** , quindi individuare l'applicazione e cercare l' **ID oggetto**. È anche possibile trovare l'ID oggetto dell'entità servizio in base al nome visualizzato usando il seguente script di PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > I nomi visualizzati per le applicazioni non sono univoci, quindi è necessario verificare di ottenere l'entità servizio dell'applicazione corretta.

1. Aggiungere un [ruolo app](../develop/howto-add-app-roles-in-azure-ad-apps.md) all'applicazione creata nel passaggio 3. È possibile creare il ruolo utilizzando il portale di Azure o utilizzando Microsoft Graph. Ad esempio, è possibile aggiungere un ruolo app come il seguente:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Assegnare il ruolo app all'identità gestita. Per assegnare il ruolo app sono necessarie le informazioni seguenti:
    * `managedIdentityObjectId`: ID oggetto dell'entità servizio dell'identità gestita, disponibile nel passaggio 2.
    * `serverServicePrincipalObjectId`: ID oggetto dell'entità servizio dell'applicazione server, disponibile nel passaggio 4.
    * `appRoleId`: ID del ruolo app esposto dall'app Server, generato nel passaggio 5. nell'esempio, l'ID del ruolo dell'app è `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Eseguire lo script di PowerShell seguente per aggiungere l'assegnazione di ruolo:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Script completo

Questo script di esempio illustra come assegnare un'identità gestita di un'app Web di Azure a un ruolo app.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare identità gestite per risorse di Azure in una macchina virtuale tramite PowerShell](qs-configure-powershell-windows-vm.md).
