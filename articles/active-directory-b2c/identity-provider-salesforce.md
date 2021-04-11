---
title: Configurare l'iscrizione e l'accesso con un account Salesforce
titleSuffix: Azure AD B2C
description: Fornire l'iscrizione e l'accesso ai clienti con account Salesforce nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 008d47fe7086322ea0bb7ef26a6c4b449f4269d2
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028826"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Salesforce usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Creare un'applicazione Salesforce

Per abilitare l'accesso per gli utenti con un account Salesforce in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in Salesforce [App Manager](https://login.salesforce.com/). Per altre informazioni, vedere [configurare le impostazioni dell'app connessa di base](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)e [abilitare le impostazioni OAuth per l'integrazione delle API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/).
1. Dal menu selezionare **installazione**.
1.  Espandere **app**, quindi selezionare **App Manager**.
1. Selezionare **nuova app connessa**.
1. In **informazioni di base** immettere:
    1. **Nome dell'app connessa** : il nome dell'app connessa viene visualizzato in App Manager e nel riquadro di avvio dell'app. Il nome deve essere univoco all'interno dell'organizzazione. 
    1. **Nome API** 
    1. **Indirizzo di posta elettronica di contatto** : il contatto per Salesforce
1. In **API (Abilita impostazioni OAuth)** selezionare **Abilita impostazioni OAuth**
    1. Per **URL callback** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Se si usa un [dominio personalizzato](custom-domain.md), immettere `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire `your-tenant-name` con il nome del tenant e con il `your-domain-name` dominio personalizzato. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
    1. Negli **ambiti OAuth selezionati** selezionare Accedi alle **informazioni di base (ID, profilo, indirizzo di posta elettronica, indirizzo, telefono)** e **Consenti l'accesso all'identificatore univoco (OpenID)**.
    1. Selezionare **Richiedi segreto per il flusso del server Web**.
1. Selezionare **Configura token ID** 
    1. Impostare il **token valido per** 5 minuti.
    1. Selezionare **Includi attestazioni standard**.
1. Fare clic su **Salva**.
1. Copiare i valori di **chiave utente** e **segreto utente**. Sono necessari entrambi per configurare Salesforce come provider di identità nel tenant. Il **segreto client** è una credenziale di sicurezza importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurare Salesforce come provider di identità

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Salesforce*.
1. Per **URL metadati** immettere l'URL del documento di [configurazione di Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Per una sandbox, login.salesforce.com viene sostituito da test.salesforce.com. Per una community, login.salesforce.com viene sostituito con l'URL della community, ad esempio username.force.com/.well-known/openid-configuration. L'URL deve essere HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Per **ID client**, immettere l'ID applicazione registrato in precedenza.
1. In **Segreto client** immettere il segreto client registrato in precedenza.
1. In **Ambito**, immettere il `openid id profile email`.
1. Lasciare i valori predefiniti per **Tipo di risposta** e **Modalità di risposta**.
1. (Opzionale) Per l'**hint di dominio**, immettere `contoso.com`. Per altre informazioni, vedere [Configurare l'accesso diretto tramite Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. In **Mapping delle attestazioni del provider di identità** selezionare le attestazioni seguenti:

    - **ID utente**: *Sub*
    - **Nome visualizzato**: *name*
    - **Nome**: *given_name*
    - **Cognome**: *family_name*
    - **Posta elettronica** *:*

1. Selezionare **Salva**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità Salesforce a un flusso utente 

A questo punto, il provider di identità Salesforce è stato configurato, ma non è ancora disponibile in nessuna delle pagine di accesso. Per aggiungere il provider di identità Salesforce a un flusso utente:

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente per cui si vuole aggiungere il provider di identità Salesforce.
1. In provider di identità basati su **Social Network** selezionare **Salesforce**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui flusso utente** .
1. Dalla pagina di iscrizione o accesso selezionare **Salesforce** per accedere con l'account Salesforce.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il segreto client registrato in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Manual`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SalesforceSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. In **Segreto** immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Configurare Salesforce come provider di identità

Per consentire agli utenti di accedere con un account Salesforce, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account Salesforce come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. I **metadati** sono impostati sull'URL del documento di [configurazione di Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Per una sandbox, login.salesforce.com viene sostituito da test.salesforce.com. Per una community, login.salesforce.com viene sostituito con l'URL della community, ad esempio username.force.com/.well-known/openid-configuration. L'URL deve essere HTTPS.
5. Impostare **client_id** sull'ID applicazione ottenuto con la registrazione dell'applicazione.
6. Salvare il file.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](tutorial-register-applications.md). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o accesso selezionare **Salesforce** per accedere con l'account Salesforce.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.


::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [passare il token Salesforce all'applicazione](idp-pass-through-user-flow.md).
