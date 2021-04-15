---
title: Configurare la modalità con cui gli utenti finali forniscono il consenso alle applicazioni usando Azure AD
description: Di seguito viene descritto come gestire la modalità e le tempistiche con cui gli utenti possono fornire il consenso alle applicazioni che avranno accesso ai dati dell'organizzazione.
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
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374438"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurare la modalità con cui gli utenti finali forniscono il consenso alle applicazioni

È possibile integrare le applicazioni con Microsoft Identity Platform per consentire agli utenti di accedere con il proprio account aziendale o dell'istituto di istruzione e consultare i dati dell'organizzazione per offrire esperienze avanzate basate sui dati.

Prima che un'applicazione possa accedere ai dati dell'organizzazione, un utente deve concedere le autorizzazioni dell'applicazione a tale scopo. Autorizzazioni diverse consentono livelli di accesso diversi. Per impostazione predefinita, tutti gli utenti possono fornire il consenso alle applicazioni per le autorizzazioni che non richiedono il consenso dell'amministratore. Per impostazione predefinita, ad esempio, un utente può fornire il consenso in modo che un'app acceda alla cassetta postale ma non può consentire a un'app di accedere senza limitazioni per la lettura e la scrittura di tutti i file dell'organizzazione.

Con la possibilità di concedere alle app l'accesso ai dati, gli utenti possono acquisire facilmente applicazioni utili ed essere produttivi. Tuttavia, in alcune situazioni questa configurazione può rappresentare un rischio se non viene monitorata e controllata con attenzione.

> [!IMPORTANT]
> Per ridurre il rischio di applicazioni dannose che tentano di ingannare gli utenti per ottenere l'accesso ai dati dell'organizzazione, è consigliabile fornire il consenso utente solo alle applicazioni pubblicate da un [editore verificato](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Impostazioni per il consenso utente

I criteri di consenso dell'app descrivono le condizioni che devono essere soddisfatte prima che un'app possa essere autorizzata. Questi criteri possono includere condizioni per l'app che richiede l'accesso, nonché le autorizzazioni richieste dall'app.

Scegliendo quali criteri di consenso per le app si applicano a tutti gli utenti, è possibile impostare limiti per il momento in cui gli utenti finali sono autorizzati a concedere il consenso alle app e quando verrà richiesto loro di richiedere la revisione e l'approvazione dell'amministratore:

* **Disable user consent** (Disabilita consenso utente): gli utenti non possono concedere le autorizzazioni alle applicazioni. Gli utenti possono continuare ad accedere alle app a cui hanno precedentemente fornito il consenso o che sono autorizzate dagli amministratori per loro conto, ma non potranno fornire autonomamente il consenso a nuove autorizzazioni o a nuove app. Solo gli utenti a cui è stato concesso un ruolo della directory che include l'autorizzazione per concedere il consenso potranno dare il consenso alle nuove app.

* Gli utenti possono acconsentire alle app di editori verificati o dell'organizzazione, ma solo [](../develop/publisher-verification-overview.md) per le autorizzazioni **selezionate:** tutti gli utenti possono dare il consenso solo alle app pubblicate da un editore verificato e alle app registrate nel tenant. Gli utenti possono concedere il consenso solo alle autorizzazioni classificate come "a basso impatto". È necessario [classificare le autorizzazioni](configure-permission-classifications.md) per selezionare le autorizzazioni a cui gli utenti sono autorizzati a fornire il consenso.

* **Gli utenti possono acconsentire a** tutte le app: questa opzione consente a tutti gli utenti di acconsentire a qualsiasi autorizzazione che non richiede il consenso dell'amministratore per qualsiasi applicazione.

* **Criteri di consenso dell'app** personalizzati: per altre opzioni sulle condizioni che regolano il consenso dell'utente, è possibile creare criteri di consenso dell'app personalizzati e configurarne l'applicazione per il consenso dell'utente. [](manage-app-consent-policies.md#create-a-custom-app-consent-policy)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per configurare le impostazioni di consenso utente tramite il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../roles/permissions-reference.md#global-administrator).
1. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Consenso e autorizzazioni** > **Impostazioni per il consenso utente**.
1. In **User consent for applications** (Consenso utente per le applicazioni) selezionare l'impostazione di consenso che si intende configurare per tutti gli utenti.
1. Per salvare le impostazioni, fare clic su **Save** (Salva).

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Impostazioni per il consenso utente":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile usare la versione più recente Azure AD di anteprima di PowerShell, [AzureADPreview,](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)per scegliere i criteri di consenso dell'app che regolano il consenso dell'utente per le applicazioni.

#### <a name="disable-user-consent"></a>Disabilitare il consenso dell'utente

Per disabilitare il consenso dell'utente, impostare i criteri di consenso che regolano il consenso dell'utente su vuoti:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Consenti consenso utente soggetto ai criteri di consenso dell'app

Per consentire il consenso dell'utente, scegliere i criteri di consenso dell'app da autorizzare gli utenti a concedere il consenso alle app:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Sostituire `{consent-policy-id}` con l'ID del criterio che si desidera applicare. È possibile scegliere [un criterio di consenso](manage-app-consent-policies.md#create-a-custom-app-consent-policy) per l'app personalizzato creato oppure uno dei criteri predefiniti seguenti:

| ID | Descrizione |
|:---|:------------|
| microsoft-user-default-low | **Consenti il consenso dell'utente per le app da editori verificati, per le autorizzazioni selezionate**<br /> Consentire il consenso utente limitato solo per le app di editori e app verificate registrate nel tenant e solo per le autorizzazioni classificate come "a basso impatto". Non dimenticare di classificare le [autorizzazioni per](configure-permission-classifications.md) selezionare le autorizzazioni a cui gli utenti sono autorizzati a concedere il consenso. |
| microsoft-user-default-legacy | **Consentire il consenso dell'utente per le app**<br /> Questa opzione consente a tutti gli utenti di acconsentire a qualsiasi autorizzazione che non richiede il consenso dell'amministratore, per qualsiasi applicazione |
  
Ad esempio, per abilitare il consenso dell'utente soggetto ai criteri `microsoft-user-default-low` predefiniti:

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [](configure-admin-consent-workflow.md) Abilitare il flusso di lavoro di consenso dell'amministratore per consentire agli utenti di richiedere la revisione e l'approvazione di un'applicazione a cui l'utente non è autorizzato a concedere il consenso, ad esempio quando il consenso dell'utente è stato disabilitato o quando un'applicazione richiede autorizzazioni a cui l'utente non è autorizzato a concedere.

## <a name="risk-based-step-up-consent"></a>Consenso alle procedure dettagliate basate sui rischi

Il consenso incrementale basato sul rischio consente di ridurre l'esposizione degli utenti ad app dannose che effettuano [richieste di consenso illecite](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Se Microsoft rileva una richiesta di consenso dell'utente finale rischiosa, sarà necessario un "passaggio" al consenso dell'amministratore. Questa funzionalità è abilitata per impostazione predefinita, ma comporterà una modifica del comportamento solo quando è abilitato il consenso dell'utente finale.

Quando viene rilevata una situazione di rischio, nella richiesta di consenso viene visualizzato un messaggio che indica che è necessaria l'approvazione dell'amministratore. Se [il flusso di lavoro della richiesta di consenso dell'amministratore](configure-admin-consent-workflow.md) è abilitato, l'utente può inviare la richiesta a un amministratore per un'ulteriore verifica direttamente dalla richiesta di consenso. In caso contrario, verrà visualizzato il messaggio seguente:

* **AADSTS90094:** &lt;NomeVisualizzatoAppClient&gt; richiede l'autorizzazione per accedere alle risorse dell'organizzazione che solo un amministratore può concedere. Prima di usarla, è necessario chiedere a un amministratore di concedere l'autorizzazione a quest'app.

In questo caso, verrà registrato anche un evento di controllo con una categoria "ApplicationManagement", un tipo di attività di "Consent to application" (Consenso per l'applicazione) e il motivo dello stato "Risky application detected" (Applicazione rischiosa rilevata).

> [!IMPORTANT]
> Gli amministratori devono [valutare attentamente tutte le richieste di consenso](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) prima di approvarne una, soprattutto quando Microsoft ha rilevato il rischio.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Disabilitare o riabilitare il consenso incrementale basato sul rischio usando PowerShell

È possibile usare il modulo Azure AD PowerShell Preview, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), per disabilitare il passaggio al consenso amministratore necessario nei casi in cui Microsoft rilevi un rischio oppure riabilitarlo se è stato disabilitato in precedenza.

1. Accertarsi di usare il modulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview). Questo passaggio è importante se sono stati installati sia il modulo [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) sia il modulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Connettersi ad Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recuperare il valore corrente per le impostazioni della directory **Consent Policy Settings** (Impostazioni criteri di consenso) nel tenant. È necessario controllare se sono state create le impostazioni della directory per questa funzionalità e, in caso contrario, usare i valori del modello di impostazioni di directory corrispondente.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Informazioni sul valore delle impostazioni:

    | Impostazione       | Tipo         | Descrizione  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Flag che indica se il consenso dell'utente verrà bloccato quando viene rilevata una richiesta rischiosa. |

1. Aggiornare il valore delle impostazioni per la configurazione desiderata:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

1. Salvare le impostazioni.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Configurare le impostazioni di consenso utente](configure-user-consent.md)
* [Gestire i criteri di consenso delle app](manage-app-consent-policies.md)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)
* [Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Per ottenere assistenza o trovare risposte alle domande:
* [Azure AD su Microsoft Q&A.](/answers/topics/azure-active-directory.html)