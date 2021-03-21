---
title: Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account QQ alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f09b4557f9bbf2f074948bd7c8dbd349cd397bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488670"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per abilitare l'accesso per gli utenti con un account QQ in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel [portale per sviluppatori QQ](http://open.qq.com). Se non si ha già un account QQ, è possibile iscriversi all'indirizzo [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Accedere al [portale per sviluppatori QQ](http://open.qq.com) con le credenziali dell'account QQ.
1. Dopo l'accesso, passare a [https://open.qq.com/reg](https://open.qq.com/reg) per registrarsi come sviluppatore.
1. Selezionare **个人** (sviluppatore singolo).
1. Immettere le informazioni necessarie e quindi fare clic su **下一步** (passaggio successivo).
1. Questa operazione completerà il processo di verifica email. È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore.

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Passare a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selezionare **应用管理** (gestione app).
1. Selezionare **创建应用**(crea app) e immettere le informazioni necessarie.
1. Per **授权回调域** (URL callback) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Se si usa un [dominio personalizzato](custom-domain.md), immettere `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire `your-tenant-name` con il nome del tenant e con il `your-domain-name` dominio personalizzato.
1. Selezionare **创建应用** (crea app).
1. Nella pagina di conferma selezionare **应用管理** (gestione app) per tornare alla pagina di gestione delle app.
1. Selezionare **查看** (visualizza) accanto all'app appena creata.
1. Selezionare **修改**(modifica).
1. Copiare l'**ID APP** e la **CHIAVE APP**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>Configurare QQ come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **provider di identità**, quindi selezionare **QQ (anteprima)**.
1. Immettere un **Nome**. Ad esempio, *QQ*.
1. Per **ID client** immettere l'ID app dell'applicazione QQ creata in precedenza.
1. Per il **segreto client**, immettere la chiave dell'app registrata.
1. Selezionare **Salva**.

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità QQ a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente per cui si vuole aggiungere il provider di identità QQ.
1. In provider di identità basati su **Social Network** selezionare **QQ**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui flusso utente** .
1. Dalla pagina di iscrizione o accesso selezionare **QQ** per accedere con l'account QQ.

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
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `QQSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. In **Segreto** immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="configure-qq-as-an-identity-provider"></a>Configurare QQ come provider di identità

Per consentire agli utenti di accedere con un account QQ, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account QQ come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o accesso selezionare **QQ** per accedere con l'account QQ.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

::: zone-end
