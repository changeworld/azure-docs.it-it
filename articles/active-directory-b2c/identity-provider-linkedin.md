---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1ade28af1094694d2cb6dffaecec4e4131630535
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584868"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="b2c-user-flow"

* [Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.
* Se non è già stato fatto, [aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

* Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
* Se non è già stato fatto, [aggiungere un'applicazione API Web al tenant di Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come [provider di identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si dispone già di un account LinkedIn, è possibile iscriversi all'indirizzo [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
1. Selezionare **app personali** e quindi fare clic su **Crea app**.
1. Immettere **nome dell'app**, **pagina LinkedIn**, **URL dell'informativa sulla privacy** e **logo dell'app**.
1. Accettare le condizioni per l' **utilizzo dell'API** LinkedIn e fare clic su **Crea app**.
1. Selezionare la scheda **autenticazione** . In **chiavi di autenticazione** copiare i valori per **ID client** e **segreto client**. Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Secret** è un'importante credenziale di sicurezza.
1. Selezionare la matita di modifica accanto a **URL di reindirizzamento autorizzati per l'app** e quindi selezionare **Aggiungi URL di reindirizzamento**. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` , sostituendo `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Selezionare **Aggiorna**.
2. Per impostazione predefinita, l'app LinkedIn non è approvata per gli ambiti correlati all'accesso. Per richiedere una verifica, selezionare la scheda **prodotti** e quindi selezionare **Accedi con LinkedIn**. Al termine della verifica, gli ambiti necessari verranno aggiunti all'applicazione.
   > [!NOTE]
   > È possibile visualizzare gli ambiti attualmente consentiti per l'app nella scheda **autenticazione** della sezione **ambiti di OAuth 2,0** .

::: zone pivot="b2c-user-flow"

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità** e quindi fare clic su **LinkedIn**.
1. Immettere un **Nome**. Ad esempio, *LinkedIn*.
1. Per **ID client**, immettere l'ID client dell'applicazione LinkedIn creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il segreto client registrato in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Manual`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `LinkedInSecret`. Il prefisso *B2C_1A_* viene aggiunto automaticamente al nome della chiave.
8. In **Secret (segreto**) immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account LinkedIn, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

Definire un account LinkedIn come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire il file * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ nell'editor. Questo file si trova nello [Starter Pack dei criteri personalizzati][starter-pack] scaricato come parte di uno dei prerequisiti.
1. Trovare l'elemento _ *ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. Sostituire il valore di **client_id** con l'ID client dell'applicazione LinkedIn registrata in precedenza.
1. Salvare il file.

### <a name="add-the-claims-transformations"></a>Aggiungere le trasformazioni delle attestazioni

Il profilo tecnico LinkedIn richiede l'aggiunta delle trasformazioni delle attestazioni **ExtractGivenNameFromLinkedInResponse** e **ExtractSurNameFromLinkedInResponse** all'elenco di ClaimsTransformations. Se non è stato definito un elemento **ClaimsTransformations** nel file, aggiungere gli elementi XML padre come illustrato di seguito. Per le trasformazioni delle attestazioni è necessario anche un nuovo tipo di attestazione definito denominato **nullStringClaim**.

Aggiungere l'elemento **BuildingBlocks** nella parte superiore del file di *TrustFrameworkExtensions.xml* . Per un esempio, vedere *TrustFrameworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto si dispone di un criterio configurato in modo che Azure AD B2C sappia come comunicare con l'account LinkedIn. Provare a caricare il file di estensione dei criteri per verificare che non ci siano problemi finora.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è disponibile in nessuna delle schermate di iscrizione o di accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità LinkedIn.

1. Aprire il file di *TrustFrameworkBase.xml* nello Starter Pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account LinkedIn, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelections** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `LinkedInExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con un account LinkedIn per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza. Ad esempio: `LinkedIn-OAUTH`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

::: zone-end

:: area pivot = "B2C-flusso utente"

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità LinkedIn a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente che si vuole usare come provider di identità LinkedIn.
1. In provider di identità basati su **Social Network** selezionare **LinkedIn**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInLinkedIn.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInLinkedIn`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_linkedin`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente che è stato creato (SignUpSignLinkedIn).
1. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
1. Verificare che nel campo **Selezionare l'applicazione** sia selezionata l'applicazione Azure AD B2C creata e quindi testarla facendo clic su **Esegui adesso**.


## <a name="migration-from-v10-to-v20"></a>Migrazione dalla versione 1.0 alla versione 2.0

LinkedIn ha [aggiornato di recente le API dalla versione 1.0 alla versione 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Per eseguire la migrazione della configurazione esistente alla nuova configurazione, usare le informazioni nelle sezioni riportate di seguito per aggiornare gli elementi nel profilo tecnico.

### <a name="replace-items-in-the-metadata"></a>Sostituisci elementi nei metadati

Nell'elemento dei **metadati** esistente di **TechnicalProfile** aggiornare gli elementi di **elemento** seguenti da:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Con:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Aggiungere elementi ai metadati

Nei **metadati** di **TechnicalProfile** aggiungere gli elementi **Item** seguenti:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aggiornare OutputClaims

Nella **OutputClaims** esistente di **TechnicalProfile** aggiornare gli elementi **OutputClaim** seguenti da:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Con:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Aggiungi nuovi elementi OutputClaimsTransformation

In **OutputClaimsTransformations** di **TechnicalProfile** aggiungere gli elementi **OutputClaimsTransformation** seguenti:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definire le nuove trasformazioni delle attestazioni e il tipo di attestazione

Nell'ultimo passaggio sono state aggiunte nuove trasformazioni delle attestazioni che devono essere definite. Per definire le trasformazioni delle attestazioni, aggiungerle all'elenco di **ClaimsTransformations**. Se non è stato definito un elemento **ClaimsTransformations** nel file, aggiungere gli elementi XML padre come illustrato di seguito. Per le trasformazioni delle attestazioni è necessario anche un nuovo tipo di attestazione definito denominato **nullStringClaim**.

L'elemento **BuildingBlocks** deve essere aggiunto alla parte superiore del file. Vedere l' *TrustframeworkBase.xml* come esempio.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Ottenere un indirizzo di posta elettronica

Come parte della migrazione di LinkedIn dalla versione 1.0 alla versione 2.0, è necessaria una chiamata aggiuntiva a un'altra API per ottenere l'indirizzo di posta elettronica. Se è necessario ottenere l'indirizzo di posta elettronica durante l'iscrizione, seguire questa procedura:

1. Completare i passaggi precedenti per consentire Azure AD B2C di eseguire la Federazione con LinkedIn per consentire all'utente di accedere. Come parte della Federazione, Azure AD B2C riceve il token di accesso per LinkedIn.
1. Salvare il token di accesso di LinkedIn in un'attestazione. [Vedere le istruzioni qui](idp-pass-through-user-flow.md).
1. Aggiungere il provider di attestazioni seguente che effettua la richiesta all' `/emailAddress` API di LinkedIn. Per autorizzare questa richiesta, è necessario il token di accesso di LinkedIn.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aggiungere il passaggio di orchestrazione seguente al percorso utente, in modo che il provider di attestazioni API venga attivato quando un utente accede tramite LinkedIn. Assicurarsi di aggiornare il `Order` numero in modo appropriato. Aggiungere questo passaggio subito dopo il passaggio di orchestrazione che attiva il profilo tecnico di LinkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Il recupero dell'indirizzo di posta elettronica da LinkedIn durante l'iscrizione è facoltativo. Se si sceglie di non ricevere il messaggio di posta elettronica da LinkedIn ma ne è necessario uno durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.

Per un esempio completo di un criterio che usa il provider di identità LinkedIn, vedere lo [Starter Pack per i criteri personalizzati](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migrazione dalla versione 1.0 alla versione 2.0

LinkedIn ha [aggiornato di recente le API dalla versione 1.0 alla versione 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Come parte della migrazione, Azure AD B2C è in grado di ottenere solo il nome completo dell'utente LinkedIn durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi raccolti durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.

::: zone-end