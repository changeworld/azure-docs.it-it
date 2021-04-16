---
title: Gestire i criteri di consenso delle app in Azure AD
description: Informazioni su come gestire i criteri di consenso delle app predefiniti e personalizzati per controllare quando è possibile concedere il consenso.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375696"
---
# <a name="manage-app-consent-policies"></a>Gestire i criteri di consenso delle app

Con Azure AD PowerShell, è possibile visualizzare e gestire i criteri di consenso delle app.

I criteri di consenso dell'app sono costituiti da uno o più set di condizioni "include" e zero o più set di condizioni "escluse". Perché un evento sia considerato nei criteri di  consenso dell'app, deve corrispondere ad almeno un set di condizioni "include" e non deve corrispondere ad alcun set di condizioni  "escluse".

Ogni set di condizioni è costituito da diverse condizioni. Perché un evento corrisponda a un set di condizioni, *tutte* le condizioni nel set di condizioni devono essere soddisfatte.

I criteri di consenso delle app in cui l'ID inizia con "microsoft-" sono criteri predefiniti. Alcuni di questi criteri predefiniti vengono usati nei ruoli di directory predefiniti esistenti. Ad esempio, i criteri di consenso dell'app descrivono le condizioni in cui i ruoli Amministratore applicazione e Amministratore applicazioni cloud sono autorizzati a concedere il consenso dell'amministratore `microsoft-application-admin` a livello di tenant. I criteri predefiniti possono essere usati nei ruoli di directory personalizzati e per configurare le impostazioni di consenso dell'utente, ma non possono essere modificati o eliminati.

## <a name="pre-requisites"></a>Prerequisiti

1. Accertarsi di usare il modulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview). Questo passaggio è importante se sono stati installati sia il modulo [AzureAD](/powershell/module/azuread/) sia il modulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Connettersi ad Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Elencare i criteri di consenso delle app esistenti

È buona idea iniziare conoscendo i criteri di consenso delle app esistenti nell'organizzazione:

1. Elencare tutti i criteri di consenso delle app:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Visualizzare i set di condizioni "include" di un criterio:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Visualizzare i set di condizioni "esclude":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Creare criteri di consenso dell'app personalizzati

Seguire questa procedura per creare criteri di consenso dell'app personalizzati:

1. Creare un nuovo criterio di consenso dell'app vuoto.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Aggiungere set di condizioni "include".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Ripetere questo passaggio per aggiungere altri set di condizioni di "inclusione".

1. Facoltativamente, aggiungere set di condizioni "excludes".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Ripetere questo passaggio per aggiungere altri set di condizioni di esclusione.

Dopo aver creato i criteri di consenso dell'app, è possibile consentire [il consenso dell'utente](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) in base a questi criteri.

## <a name="delete-a-custom-app-consent-policy"></a>Eliminare i criteri di consenso dell'app personalizzati

1. Di seguito viene illustrato come eliminare un criterio di consenso dell'app personalizzato. **Questa azione non può essere annullata.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> I criteri di consenso delle app eliminati non possono essere ripristinati. Se si elimina accidentalmente un criterio di consenso dell'app personalizzato, sarà necessario creare nuovamente il criterio.

---

### <a name="supported-conditions"></a>Condizioni supportate

La tabella seguente fornisce l'elenco delle condizioni supportate per i criteri di consenso delle app.

| Condizione | Descrizione|
|:---------------|:----------|
| PermissionClassification | Classificazione [delle autorizzazioni](configure-permission-classifications.md) per l'autorizzazione concessa o "all" in modo che corrisponda a qualsiasi classificazione di autorizzazione (incluse le autorizzazioni non classificate). Il valore predefinito è "all". |
| Tipo di autorizzazione | Tipo di autorizzazione dell'autorizzazione concessa. Usare "applicazione" per le autorizzazioni dell'applicazione (ad esempio, i ruoli dell'app) o "delegate" per le autorizzazioni delegate. <br><br>**Nota:** il valore "delegatedUserConsentable" indica le autorizzazioni delegate che non sono state configurate dall'autore dell'API per richiedere il consenso dell'amministratore. Questo valore può essere usato nei criteri di concessione delle autorizzazioni predefiniti, ma non nei criteri di concessione delle autorizzazioni personalizzati. Obbligatorio. |
| ResourceApplication | **AppId dell'applicazione** della risorsa(ad esempio, l'API) per cui viene concessa un'autorizzazione o "qualsiasi" da associare a qualsiasi applicazione o API della risorsa. Il valore predefinito è "any". |
| Autorizzazioni | Elenco di ID di autorizzazione per le autorizzazioni specifiche con cui trovare la corrispondenza oppure elenco con il singolo valore "all" in modo che corrisponda a qualsiasi autorizzazione. Il valore predefinito è il singolo valore "all". <ul><li>Gli ID autorizzazione delegati sono disponibili nella **proprietà OAuth2Permissions** dell'oggetto ServicePrincipal dell'API.</li><li>Gli ID di autorizzazione dell'applicazione sono disponibili nella **proprietà AppRoles** dell'oggetto ServicePrincipal dell'API.</li></ol> |
| ClientApplicationIds | Elenco di valori **AppId** per le applicazioni client con cui trovare la corrispondenza oppure un elenco con il singolo valore "all" in modo che corrisponda a qualsiasi applicazione client. Il valore predefinito è il singolo valore "all". |
| ClientApplicationTenantIds | Elenco di ID Azure Active Directory tenant in cui è registrata l'applicazione client o un elenco con il singolo valore "all" per la corrispondenza con le app client registrate in qualsiasi tenant. Il valore predefinito è il singolo valore "all". |
| ClientApplicationPublisherIds | Un elenco di ID Microsoft Partner Network (MPN) per gli editori verificati dell'applicazione client o un elenco con il singolo valore "all" per la corrispondenza con le app client di qualsiasi editore. [](../develop/publisher-verification-overview.md) Il valore predefinito è il singolo valore "all". |
| ClientApplicationsFromVerifiedPublisherOnly | Impostare su `$true` in modo che corrisponda solo alle applicazioni client con un server di pubblicazione [verificato.](../develop/publisher-verification-overview.md) Impostare su `$false` in modo che corrisponda a qualsiasi app client, anche se non dispone di un editore verificato. Il valore predefinito è `$false`. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Configurare le impostazioni di consenso utente](configure-user-consent.md)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)
* [Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Per ottenere assistenza o trovare risposte alle domande:
* [Azure AD su Microsoft Q&A](/answers/products/)