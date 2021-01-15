---
title: Configurare l'iscrizione e l'accesso con un account Twitter
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Twitter alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f209e0ac17c62378d279a32f4a27f48a9f74bd
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232693"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Creare un'applicazione

Per abilitare l'accesso per gli utenti con un account Twitter in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione Twitter. Se non si ha già un account Twitter, è possibile iscriversi all'indirizzo [https://twitter.com/signup](https://twitter.com/signup) . È anche necessario [richiedere un account per sviluppatore](https://developer.twitter.com/en/apply/user.html). Per ulteriori informazioni, vedere [apply for Access](https://developer.twitter.com/en/apply-for-access).

1. Accedere al portale per [sviluppatori Twitter](https://developer.twitter.com/portal/projects-and-apps) con le credenziali dell'account Twitter.
1. In **app autonome** selezionare **+ Crea app**.
1. Immettere un **nome** per l'app e quindi fare clic su **completa**.
1. Copiare il valore della **chiave dell'app** e il **segreto della chiave API**.  Usare entrambi per configurare Twitter come provider di identità nel tenant. 
1. In **configurare l'app** selezionare **impostazioni app**.
1. In **Impostazioni autenticazione** selezionare **modifica** .
    1. Selezionare Abilita la casella di controllo **OAuth a tre zampe** .
    1. Selezionare la casella **di controllo Richiedi indirizzo di posta elettronica da utenti** .
    1. Per gli **URL di callback** immettere `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Sostituire `your-tenant` con il nome del nome tenant e `your-user-flow-Id` con l'identificatore del flusso utente. Ad esempio, `b2c_1A_signup_signin_twitter`. È necessario usare tutte le lettere minuscole quando si immette il nome del tenant e l'ID del flusso utente anche se sono definiti con lettere maiuscole in Azure AD B2C.
    1. Per l' **URL del sito Web**, immettere `https://your-tenant.b2clogin.com` . Sostituire `your-tenant` con il nome del tenant. Ad esempio, `https://contosob2c.b2clogin.com`.
    1. Immettere un URL per le **condizioni del servizio**, ad esempio `http://www.contoso.com/tos` . L'URL dei criteri è una pagina che viene mantenuta per fornire i termini e le condizioni per l'applicazione.
    1. Immettere un URL per l' **informativa sulla privacy**, ad esempio `http://www.contoso.com/privacy` . L'URL del criterio è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
    1. Selezionare **Salva**.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità** e quindi selezionare **Twitter**.
1. Immettere un **Nome**. Ad esempio *Twitter*.
1. Per **ID client**, immettere la *chiave API* dell'applicazione Twitter creata in precedenza.
1. Per il **segreto client**, immettere il *segreto chiave API* registrato.
1. Selezionare **Salva**.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità Twitter a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Selezionare il flusso utente per cui si vuole aggiungere il provider di identità Twitter.
1. In provider di identità basati su **Social Network** selezionare **Twitter**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave privata registrata in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Manual`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `TwitterSecret`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. In **Segreto** immettere il segreto client registrato in precedenza.
9. In **Uso chiave** selezionare `Encryption`.
10. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account Twitter, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account Twitter come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. Sostituire il valore di **client_id** con il *segreto della chiave API* registrato in precedenza.
5. Salvare il file.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappiano come comunicare con l'account Twitter. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è disponibile nelle schermate di iscrizione o accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità Twitter.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account Twitter, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `TwitterExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con un account Twitter per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza. Ad esempio: `Twitter-OAUTH1`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInTwitter.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInTwitter`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_twitter`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente che è stato creato (SignUpSignTwitter).
1. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
1. Verificare che nel campo **Selezionare l'applicazione** sia selezionata l'applicazione Azure AD B2C creata e quindi testarla facendo clic su **Esegui adesso**.

::: zone-end
