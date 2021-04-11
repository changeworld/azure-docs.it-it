---
title: Configurare l'iscrizione e l'accesso con un account Google
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Google alle applicazioni da Azure Active Directory B2C.
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
ms.openlocfilehash: e4dee196d3ff0796802d2552f073446ad6912663
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028265"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Google tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Creazione di un'applicazione Google

Per abilitare l'accesso per gli utenti con un account Google in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in [Google Developers Console](https://console.developers.google.com/). Per ulteriori informazioni, vedere [la pagina relativa alla configurazione di OAuth 2,0](https://support.google.com/googleapi/answer/6158849). Se non si ha già un account Google, è possibile iscriversi all'indirizzo [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Accedere alla [Google Developers Console](https://console.developers.google.com/) con le credenziali dell'account Google.
1. Nell'angolo superiore sinistro della pagina selezionare l'elenco progetto, quindi selezionare **nuovo progetto**.
1. Immettere un **nome di progetto** e selezionare **Crea**.
1. Assicurarsi di usare il nuovo progetto selezionando l'elenco a discesa progetto nella parte superiore sinistra della schermata. Selezionare il progetto in base al nome e quindi selezionare **Apri**.
1. Selezionare **schermata di consenso OAuth** nel menu a sinistra, selezionare **esterno** e quindi fare clic su **Crea**.
Immettere un **nome** per l'applicazione. Immettere *b2clogin.com* nella sezione **domini autorizzati** e selezionare **Salva**.
1. Selezionare **Credenziali** nel menu a sinistra e quindi selezionare **Crea credenziali di accesso** > **ID client Oauth**.
1. In **Tipo di applicazione** selezionare **Applicazione Web**.
    1. Immettere un **nome** per l'applicazione.
    1. Per le **origini JavaScript autorizzate**, immettere `https://your-tenant-name.b2clogin.com` . Se si usa un [dominio personalizzato](custom-domain.md), immettere `https://your-domain-name` .
    1. Per gli **URI di reindirizzamento autorizzati**, immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Se si usa un [dominio personalizzato](custom-domain.md), immettere `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire `your-domain-name` con il dominio personalizzato e `your-tenant-name` con il nome del tenant. Usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
1. Fare clic su **Crea**.
1. Copiare i valori di **ID client** e **segreto client**. Sono necessari entrambi per configurare Google come provider di identità nel tenant. Il **segreto client** è una credenziale di sicurezza importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>Configurare Google come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi selezionare **Google**.
1. Immettere un **Nome**. Ad esempio, *Google*.
1. Per **ID client**, immettere l'ID client dell'applicazione Google creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

## <a name="add-google-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità Google a un flusso utente 

A questo punto, il provider di identità Google è stato configurato, ma non è ancora disponibile in nessuna delle pagine di accesso. Per aggiungere il provider di identità Google a un flusso utente:


1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente per cui si vuole aggiungere il provider di identità Google.
1. In provider di identità basati su **Social Network** selezionare **Google**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui flusso utente** .
1. Dalla pagina di iscrizione o accesso selezionare **Google** per accedere con l'account Google.

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
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `GoogleSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. In **Segreto** immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="configure-google-as-an-identity-provider"></a>Configurare Google come provider di identità

Per consentire agli utenti di accedere con un account Google, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account Google come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Impostare **client_id** sull'ID applicazione ottenuto con la registrazione dell'applicazione.
5. Salvare il file.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](tutorial-register-applications.md). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o accesso selezionare **Google** per accedere con l'account Google.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [passare il token Google all'applicazione](idp-pass-through-user-flow.md).