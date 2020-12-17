---
title: Configurare l'iscrizione e l'accesso con un account ID.me
titleSuffix: Azure AD B2C
description: Fornire l'iscrizione e l'accesso ai clienti con account ID.me nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6877a82d81b46ac2ffda8d0f0bf3fb0d7b337ff3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663631"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account ID.me usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>Creare un'applicazione ID.me

Per usare un account ID.me come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in [ID.me Developer Resources per API & SDK](https://developers.id.me/). Se non si ha già un account sviluppatore ID.me, è possibile iscriversi all'indirizzo [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. Accedere alle [risorse per sviluppatori di ID.me per l'API & SDK](https://developers.id.me/) con le credenziali dell'account ID.me.
1. Selezionare **Visualizza applicazioni personali** e selezionare **continua**.
1. Selezionare **Crea nuovo**
    1. Immettere un **nome** e un **nome visualizzato**.
    1. In **URI di reindirizzamento** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire `your-tenant-name` con il nome del tenant. 
1. Fare clic su **Continue**.
1. Copiare i valori di **ID client** e **segreto client**. Entrambi sono necessari per aggiungere il provider di identità nel tenant.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il segreto client registrato in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Manual`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `IdMeSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. In **Segreto** immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Signature`.
10. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Se si vuole che gli utenti possano accedere usando un account ID.me, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account ID.me come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAUTH2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Impostare **client_id** sull'ID applicazione ottenuto con la registrazione dell'applicazione.
5. Salvare il file.

### <a name="add-the-claims-transformations"></a>Aggiungere le trasformazioni delle attestazioni

Successivamente, è necessaria una trasformazione delle attestazioni per creare l'attestazione displayName. Aggiungere la trasformazione delle attestazioni seguente all' `<ClaimsTransformations>` elemento all'interno di `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappiano come comunicare con l'account ID.me. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che disponga anche del provider di identità ID.me.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInIdMe`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account ID.me, viene visualizzato un nuovo pulsante quando un utente atterra nella pagina.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `IdMeExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nell'Azure AD B2C comunicare con un account ID.me per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore che è stato usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAUTH2" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza. Ad esempio: `IdMe-OAUTH2`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, rinominarlo in *SignUpSignInIdMe.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignIdMe`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_IdMe`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente creato (SignUpSignIdMe).
1. Salvare le modifiche, caricare il file.
1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui adesso** e selezionare ID.me per accedere con ID.me e testare i criteri personalizzati.

::: zone-end