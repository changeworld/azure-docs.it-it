---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9d24bd65f1bd473560585ec9d594ddc77d858177
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111274"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Per impostazione predefinita, Azure Active Directory B2C (Azure AD B2C) verifica l'indirizzo di posta elettronica del cliente per gli account locali (account per gli utenti che effettuano l'iscrizione con l'indirizzo di posta elettronica o il nome utente). Azure AD B2C garantisce indirizzi di posta elettronica validi richiedendo ai clienti di verificarli durante il processo di iscrizione. Impedisce inoltre agli attori malintenzionati di usare processi automatizzati per generare account illeciti nelle applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione e i clienti possono invece verificare il proprio indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. Questa operazione consente di creare un processo di iscrizione più semplice e offre agli sviluppatori la flessibilità necessaria per distinguere i clienti che hanno verificato il proprio indirizzo di posta elettronica dai clienti che non lo hanno fatto.

> [!WARNING]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disattiva la verifica predefinita della posta elettronica fornita dal Azure AD B2C, si consiglia di implementare un sistema di verifica sostitutivo.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Disabilitare la verifica tramite posta elettronica

::: zone pivot="b2c-user-flow"

Per disabilitare la verifica tramite posta elettronica, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera disabilitare la verifica tramite posta elettronica. Ad esempio, *B2C_1_signinsignup*.
1. Selezionare **layout di pagina**.
1. Selezionare **la pagina di iscrizione dell'account locale**.
1. In **attributi utente** selezionare **indirizzo di posta elettronica**.
1. Nell'elenco a discesa **richiesta di verifica** selezionare **No**.
1. Selezionare **Salva**. La verifica tramite posta elettronica è ora disabilitata per questo flusso utente.

::: zone-end

::: zone pivot="b2c-custom-policy"
Il profilo tecnico **LocalAccountSignUpWithLogonEmail** è un [autocertificato](self-asserted-technical-profile.md), che viene richiamato durante il flusso di iscrizione. Per disabilitare la verifica tramite posta elettronica, impostare i `EnforceEmailVerification` metadati su false. Eseguire l'override dei profili tecnici LocalAccountSignUpWithLogonEmail nel file di estensione. 

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Trovare l'elemento `ClaimsProviders`. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di attestazioni seguente all' `ClaimsProviders` elemento:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Verificare i criteri 

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera disabilitare la verifica tramite posta elettronica. Ad esempio, *B2C_1_signinsignup*.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica senza la convalida.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Carica criteri personalizzati** e caricare i file dei due criteri modificati.
1. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica senza la convalida.

::: zone-end


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare l'interfaccia utente in Azure Active Directory B2C](customize-ui-with-html.md)

