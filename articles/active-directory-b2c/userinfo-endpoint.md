---
title: Endpoint UserInfo | Microsoft Docs
description: Definire un endpoint UserInfo in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500581"
---
# <a name="userinfo-endpoint"></a>Endpoint UserInfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

L'endpoint UserInfo è parte della specifica di [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) ed è progettato per restituire attestazioni relative all'utente autenticato. L'endpoint UserInfo viene definito nei criteri di relying party usando l'elemento [endpoint](relyingparty.md#endpoints) .  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Panoramica dell'endpoint UserInfo

UserJourney Info utente specifica:

- **Autorizzazione**: l'endpoint UserInfo è protetto con un Bearer token. Un token di accesso emesso viene presentato nell'intestazione dell'autorizzazione per l'endpoint UserInfo. I criteri specificano il profilo tecnico che convalida il token in ingresso ed estrae le attestazioni, ad esempio il valore objectId dell'utente. Il valore objectId dell'utente viene usato per recuperare le attestazioni da restituire nella risposta del percorso dell'endpoint UserInfo. 
- **Passaggio di orchestrazione**: 
  - Un passaggio di orchestrazione viene utilizzato per raccogliere informazioni sull'utente. In base alle attestazioni contenute nel token di accesso in ingresso, il percorso utente richiama un [profilo tecnico Azure Active Directory](active-directory-technical-profile.md) per recuperare i dati relativi all'utente, ad esempio la lettura dell'utente da ObjectId. 
  - **Passaggi di orchestrazione facoltativi** : è possibile aggiungere altri passaggi di orchestrazione, ad esempio un profilo tecnico dell'API REST per recuperare altre informazioni sull'utente. 
  - **Issuer UserInfo** -specifica l'elenco di attestazioni restituite dall'endpoint UserInfo.

## <a name="create-a-userinfo-endpoint"></a>Creare un endpoint UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. aggiungere il profilo tecnico dell'emittente del token

1. Aprire il file *TrustFrameworkExtensions.xml*.
1. Se non esiste già, aggiungere un elemento ClaimsProvider e i relativi elementi figlio come primo elemento sotto l'elemento BuildingBlocks.
1. Aggiungere il provider di attestazioni seguente:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. La sezione InputClaims all'interno del profilo tecnico **UserInfoIssuer** specifica gli attributi che si desidera restituire. Il profilo tecnico UserInfoIssuer viene chiamato alla fine del percorso utente. 
1. Il profilo tecnico **UserInfoAuthorization** convalida la firma, il nome dell'autorità emittente e il destinatario del token ed estrae l'attestazione dal token in ingresso. Modificare i metadati seguenti per riflettere l'ambiente:
    1. **Issuer** : questo valore deve essere identico all' `iss` attestazione nell'attestazione del token di accesso. I token emessi da Azure AD B2C usano un'autorità emittente nel formato `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Altre informazioni sulla [personalizzazione dei token](configure-tokens.md).
    1. **IdTokenAudience** -deve essere identica all' `aud` attestazione nell'attestazione del token di accesso. In Azure AD B2C l' `aud` attestazione è l'ID dell'applicazione relying party. Questo valore è una raccolta e supporta più valori usando un delimitatore di virgola.

        Nel token di accesso seguente il `iss` valore dell'attestazione è `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . Il `aud` valore dell'attestazione è `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  L'elemento OutputClaims del profilo tecnico **UserInfoAuthorization** specifica gli attributi che si desidera leggere dal token di accesso. **ClaimTypeReferenceId** è il riferimento a un tipo di attestazione. Il **PartnerClaimType** facoltativo è il nome dell'attestazione definita nel token di accesso.



### <a name="2-add-the-userjourney-element"></a>2. aggiungere l'elemento UserJourney 

L'elemento [UserJourney](userjourneys.md) definisce il percorso dell'utente durante l'interazione con l'applicazione. Aggiungere l'elemento **UserJourneys**, se non esiste, con **UserJourney** identificato come `UserInfoJourney`:

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. includere l'endpoint per i criteri di relying party

Per includere l'endpoint UserInfo nell'applicazione relying party, aggiungere un elemento [endpoint](relyingparty.md#endpoints) al file *SocialAndLocalAccounts/SignUpOrSignIn.xml* . 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

L'elemento relying party completato sarà il seguente:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Nella pagina **Criteri personalizzati** selezionare **Carica i criteri personalizzati**.
1. Selezionare **Sovrascrivi i criteri personalizzati, se esistenti**, quindi cercare e selezionare il file *TrustframeworkExtensions.xml* .
1. Fare clic su **Carica**.
1. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Chiamata all'endpoint UserInfo

L'endpoint UserInfo usa l'API del token di connessione OAuth2 standard, chiamata usando il token di accesso ricevuto durante il recupero di un token per l'applicazione. Restituisce una risposta JSON che contiene le attestazioni relative all'utente. L'endpoint UserInfo è ospitato in Azure AD B2C in:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

L'endpoint/.well-known Configure (documento di individuazione di OpenID Connect) elenca il `userinfo_endpoint` campo. È possibile individuare l'endpoint UserInfo a livello di codice usando l'endpoint di configurazione di/.well-known all'indirizzo: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testare il criterio

1. In **criteri personalizzati** selezionare i criteri con cui è stato integrato l'endpoint UserInfo. Ad esempio, *B2C_1A_SignUpOrSignIn*.
1. Selezionare **Esegui adesso**. 
1. In **Seleziona applicazione** selezionare l'applicazione registrata in precedenza. Per **Seleziona URL di risposta** scegliere `https://jwt.ms` . Per ulteriori informazioni, vedere la pagina relativa alla [registrazione di un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).
1. Iscriversi o accedere con un indirizzo di posta elettronica o un account di social networking.
1. Copiare il id_token nel formato codificato dal [https://jwt.ms](https://jwt.ms) sito Web. È possibile usare questo valore per inviare una richiesta GET all'endpoint UserInfo e recuperare le informazioni sull'utente.
1. Inviare una richiesta GET all'endpoint UserInfo e recuperare le informazioni sull'utente.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Una risposta corretta avrà un aspetto simile al seguente:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile trovare un esempio di criteri dell'endpoint UserInfo in [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
