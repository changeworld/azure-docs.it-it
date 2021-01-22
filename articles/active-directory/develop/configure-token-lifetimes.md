---
title: Imposta la durata per i token
titleSuffix: Microsoft identity platform
description: Informazioni su come impostare la durata dei token emessi dalla piattaforma di identità Microsoft. Informazioni su come gestire i criteri predefiniti di un'organizzazione, creare criteri per l'accesso Web, creare criteri per un'app nativa che chiama un'API Web e gestire criteri avanzati.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 883a06bdffcd0afcbc1be6f2c761d6a1c2c2ea2a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681876"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurare i criteri di durata del token (anteprima)
È possibile specificare la durata di un token di accesso, SAML o ID emesso dalla piattaforma di identità Microsoft. La durata dei token può essere impostata per tutte le app di un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione. Per altre informazioni, vedere [durate dei token configurabili](active-directory-configurable-token-lifetimes.md).

In questa sezione viene descritto uno scenario di criteri comuni che consente di imporre nuove regole per la durata dei token. Nell'esempio viene illustrato come creare un criterio che richiede agli utenti di eseguire l'autenticazione con maggiore frequenza nell'app Web.

## <a name="get-started"></a>Introduzione
Per iniziare, seguire questa procedura:

1. Scaricare la [versione di anteprima pubblica del modulo Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview)più recente.
1. Eseguire il comando `Connect` per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Per visualizzare tutti i criteri creati nell'organizzazione, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  I risultati con valori di proprietà definiti che differiscono dalle impostazioni predefinite elencate in precedenza rientrano nell'ambito del ritiro.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Per visualizzare le app e le entità servizio collegate a criteri specifici identificati, eseguire il cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) seguente sostituendo **1a37dad8-5da7-4CC8-87C7-efbc0326cf20** con uno qualsiasi degli ID criterio. È quindi possibile decidere se configurare la frequenza di accesso con accesso condizionale o rimanere con i valori predefiniti Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Se il tenant dispone di criteri che definiscono valori personalizzati per le proprietà di configurazione del token di sessione e di aggiornamento, Microsoft consiglia di aggiornare tali criteri ai valori che riflettono le impostazioni predefinite descritte in precedenza. Se non viene apportata alcuna modifica, Azure AD rispetterà automaticamente i valori predefiniti.

## <a name="create-a-policy-for-web-sign-in"></a>Creare criteri per l'accesso Web

In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire più spesso l'autenticazione nell'app Web. Questi criteri consentono di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a più fattori per l'entità servizio dell'app Web.

1. Creare i criteri per la durata dei token.

    Questi criteri per l'accesso Web consentono di impostare su due ore la durata dei token di accesso/ID e la validità massima di un token di sessione a fattore singolo.

    1. Per creare il criterio, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri e per ottenere l' **ObjectID** dei criteri, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri all'entità servizio. È anche necessario ottenere l' **ObjectID** dell'entità servizio.

    1. Usare il cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) per visualizzare tutte le entità servizio dell'organizzazione o una singola entità servizio.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando si dispone dell'entità servizio, eseguire il cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Creare criteri di durata dei token per i token di aggiornamento e di sessione
> [!IMPORTANT]
> A partire da maggio 2020, i nuovi tenant non possono configurare la durata dei token di aggiornamento e di sessione.  I tenant con configurazione esistente possono modificare i criteri di aggiornamento e token di sessione fino al 30 gennaio 2021.  Azure Active Directory smetterà di rispettare la configurazione del token di sessione e di aggiornamento esistente nei criteri dopo il 30 gennaio 2021. È comunque possibile configurare la durata dei token di accesso, SAML e ID dopo il ritiro.
>
> Se è necessario continuare a definire il periodo di tempo prima che all'utente venga richiesto di eseguire di nuovo l'accesso, configurare la frequenza di accesso nell'accesso condizionale. Per altre informazioni sull'accesso condizionale, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).
>
> Se non si vuole usare l'accesso condizionale dopo la data di ritiro, i token di aggiornamento e di sessione verranno impostati sulla [configurazione predefinita](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) in tale data e non sarà più possibile modificarne la durata.

### <a name="manage-an-organizations-default-policy"></a>Gestire i criteri predefiniti di un'organizzazione
In questo esempio si crea un criterio che consente agli utenti di accedere con minore frequenza nell'intera organizzazione. A tale scopo, creare un criterio per la durata dei token per i token di aggiornamento a fattore singolo, applicato all'interno dell'organizzazione. Tali criteri vengono applicati a ogni applicazione nell'organizzazione e a ogni entità servizio per cui ancora non sono impostati criteri.

1. Creare i criteri per la durata dei token.

    1. Impostare il token di aggiornamento a fattore singolo su "until-revoked". In questo modo il token non scade fino a quando non viene revocato l'accesso. Creare la definizione dei criteri seguente:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Per creare il criterio, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Per rimuovere gli spazi vuoti, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aggiornare i criteri.

    Il primo criterio impostato in questo esempio potrebbe non essere rigoroso quanto richiesto dal servizio. Per impostare il token di aggiornamento a fattore singolo in modo che scada entro due giorni, eseguire il comando seguente:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Creare criteri per un'app nativa che chiama un'API Web
In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire l'autenticazione con minore frequenza. Questi criteri prolungano anche l'intervallo di tempo di inattività dell'utente prima che sia necessario rieseguire l'autenticazione. I criteri vengono applicati all'API web. Quando l'app nativa richiede l'API Web come risorsa, vengono applicati questi criteri.

1. Creare i criteri per la durata dei token.

    1. Per creare un criterio rigoroso per un'API Web, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri, eseguire il comando seguente:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri all'API Web. È necessario ottenere anche l'**ObjectId** dell'app. Usare il cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) per trovare l' **ObjectID** dell'app o usare il [portale di Azure](https://portal.azure.com/).

    Ottenere l' **ObjectID** dell'app e assegnare i criteri:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Gestire criteri avanzati
In questo esempio vengono creati alcuni criteri per apprendere il funzionamento del sistema di priorità. Viene inoltre illustrato come gestire più criteri applicati a diversi oggetti.

1. Creare i criteri per la durata dei token.

    1. Per creare un criterio predefinito dell'organizzazione che imposta la durata del token di aggiornamento a fattore singolo su 30 giorni, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri a un'entità servizio.

    A questo punto sono disponibili criteri che si applicano all'intera organizzazione. Si supponga di voler conservare tali criteri della durata di 30 giorni per un'entità servizio specifica, impostando però i criteri predefiniti dell'organizzazione sul valore limite superiore, ovvero fino alla revoca.

    1. Per visualizzare tutte le entità servizio dell'organizzazione, usare il cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Quando si dispone dell'entità servizio, eseguire il cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Impostare il flag `IsOrganizationDefault` su false.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Creare nuovi criteri predefiniti dell'organizzazione.

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    I criteri originali sono ora collegati all'entità servizio e i nuovi criteri sono impostati come criteri predefiniti dell'organizzazione. È importante ricordare che i criteri applicati alle entità servizio hanno priorità rispetto a quelli predefiniti dell'organizzazione.

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md) in Azure ad l'accesso condizionale.