---
title: Impostare la durata per i token
titleSuffix: Microsoft identity platform
description: Informazioni su come impostare la durata dei token rilasciati da Microsoft Identity Platform. Informazioni su come gestire i criteri predefiniti di un'organizzazione, creare criteri per l'accesso Web, creare criteri per un'app nativa che chiama un'API Web e gestire criteri avanzati.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363887"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurare i criteri di durata dei token (anteprima)
È possibile specificare la durata di un token di accesso, SAML o ID rilasciato da Microsoft Identity Platform. La durata dei token può essere impostata per tutte le app di un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione. Per altre informazioni, vedere [Durate dei token configurabili.](active-directory-configurable-token-lifetimes.md)

In questa sezione viene descritto uno scenario di criteri comune che consente di imporre nuove regole per la durata dei token. Nell'esempio si apprenderà come creare un criterio che richiede agli utenti di eseguire l'autenticazione più frequentemente nell'app Web.

## <a name="get-started"></a>Introduzione

Per iniziare, scaricare la versione di [anteprima pubblica Azure AD del modulo PowerShell più recente.](https://www.powershellgallery.com/packages/AzureADPreview)

Eseguire quindi il `Connect` comando per accedere all'account Azure AD amministratore. Eseguire questo comando ogni volta che si avvia una nuova sessione.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Creare criteri per l'accesso Web

In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire più spesso l'autenticazione nell'app Web. Questo criterio imposta la durata dei token di accesso/ID sull'entità servizio dell'app Web.

1. Creare i criteri per la durata dei token.

    Questo criterio, per l'accesso Web, imposta la durata del token di accesso/ID su due ore.

    Per creare i criteri, eseguire il cmdlet [New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Per visualizzare i nuovi criteri e ottenere il criterio **ObjectId**, eseguire il cmdlet [Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Assegnare i criteri all'entità servizio. È anche necessario ottenere **l'ObjectId dell'entità** servizio.

    Usare il cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) per visualizzare tutte le entità servizio dell'organizzazione o una singola entità servizio.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Quando si dispone dell'entità servizio, eseguire il cmdlet [Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Visualizzare i criteri esistenti in un tenant

Per visualizzare tutti i criteri creati nell'organizzazione, eseguire il cmdlet [Get-AzureADPolicy.](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)  Tutti i risultati con valori di proprietà definiti che differiscono dalle impostazioni predefinite elencate in precedenza sono nell'ambito del ritiro.

```powershell
Get-AzureADPolicy -All $true
```

Per visualizzare le app e le entità servizio collegate a un criterio specifico identificato, eseguire il cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) seguente sostituendo **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** con uno degli ID criteri. È quindi possibile decidere se configurare la frequenza di accesso all'accesso condizionale o mantenere le impostazioni Azure AD predefinite.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Se il tenant dispone di criteri che definiscono valori personalizzati per le proprietà di configurazione del token di aggiornamento e di sessione, Microsoft consiglia di aggiornare tali criteri ai valori che riflettono le impostazioni predefinite descritte in precedenza. Se non viene apportata alcuna modifica, Azure AD automaticamente i valori predefiniti.

### <a name="troubleshooting"></a>Risoluzione dei problemi
Alcuni utenti hanno segnalato un `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` errore dopo l'esecuzione del `Get-AzureADPolicy` cmdlet. Come soluzione alternativa, eseguire il comando seguente per disinstallare/reinstallare il modulo AzureAD e quindi installare il modulo AzureADPreview:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md) in Azure AD condizionale.
