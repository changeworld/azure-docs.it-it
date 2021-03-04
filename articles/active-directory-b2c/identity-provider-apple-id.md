---
title: Configurare l'iscrizione e l'accesso con un ID Apple
titleSuffix: Azure AD B2C
description: Fornire l'iscrizione e l'accesso ai clienti con ID Apple nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc6801de858c72a703317805d00f8e50cae69bbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054718"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Configurare l'iscrizione e l'accesso con un ID Apple usando Azure Active Directory B2C (anteprima)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Creare un'applicazione Apple ID

Per abilitare l'accesso per gli utenti con un ID Apple in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in [https://developer.apple.com](https://developer.apple.com) . Per altre informazioni, vedere [accedere con Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Se non si ha già un account Apple Developer, è possibile iscriversi a [Apple Developer Program](https://developer.apple.com/programs/enroll/).

1. Accedere al [portale Apple Developer](https://developer.apple.com/account) con le credenziali dell'account.
1. Dal menu selezionare **certificati, ID, & profili** e quindi selezionare **(+)**.
1. Per **registra un nuovo identificatore**, selezionare **ID app**, quindi selezionare **continua**.
1. Per **selezionare un tipo** selezionare **app**, quindi fare clic su **continua**.
1. Per **registrare un ID app**:
    1. Immettere una **Descrizione** 
    1. Immettere l' **ID bundle**, ad esempio `com.contoso.azure-ad-b2c` . 
    1. Per **funzionalità** selezionare **Accedi con Apple** dall'elenco delle funzionalità. 
    1. Prendere nota del prefisso dell'ID app (ID Team) da questo passaggio. Sarà necessario più avanti.
    1. Selezionare **Continue** (Continua) e quindi **Register** (Registra).
1. Dal menu selezionare **certificati, ID, & profili** e quindi selezionare **(+)**.
1. Per **registra un nuovo identificatore**, selezionare **ID Servizi**, quindi selezionare **continua**.
1. Per **registrare un ID Servizi**:
    1. Immettere una **Descrizione**. La descrizione viene visualizzata all'utente nella schermata del consenso.
    1. Immettere l' **identificatore**, ad esempio `com.consoto.azure-ad-b2c-service` . L'identificatore è l'ID client per il flusso OpenID Connect.
    1. Selezionare **continue (continua**) e quindi selezionare **Register (registra**).
1. In **identificatori** selezionare l'identificatore creato.
1. Selezionare **Accedi con Apple**, quindi selezionare **Configura**.
    1. Selezionare l' **ID app principale** con cui si vuole configurare l'accesso con Apple.
    1. In **domini e sottodomini**, immettere `your-tenant-name.b2clogin.com` . Sostituire your-tenant-name con il nome del tenant.
    1. In **URL restituiti** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire your-tenant-name con il nome del tenant.
    1. Selezionare **Avanti**, quindi fare clic su **fine**.
    1. Quando la finestra popup è chiusa, selezionare **continua** e quindi fare clic su **Salva**.

## <a name="creating-an-apple-client-secret"></a>Creazione di un segreto client Apple

1. Dal menu Apple Developer Portal selezionare **chiavi**, quindi selezionare **(+)**.
1. Per **registra una nuova chiave**:
    1. Digitare un **nome di chiave**.
    1. Selezionare **Accedi con Apple**, quindi selezionare **Configura**.
    1. Per l' **ID app principale**, selezionare l'app creata in precedenza e selezionare **Salva**.
    1. Selezionare **Configure (Configura**) e quindi selezionare **Register (registra** ) per completare il processo di registrazione della chiave.
1. Per **scaricare la chiave**, selezionare **download** per scaricare un file con estensione P8 contenente la chiave.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Configurare Apple come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure ad B2C.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
1. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.
1. Selezionare **provider di identità** e quindi selezionare **Apple (anteprima)**.
1. Immettere un **Nome**. Ad esempio, *Apple*.
1. Immettere l' **ID sviluppatore Apple (ID Team)**.
1. Immettere l' **ID del servizio Apple (ID client)**.
1. Immettere l' **ID della chiave Apple**.
1. Selezionare e caricare i **dati del certificato Apple**.
1. Selezionare **Salva**.


> [!IMPORTANT] 
> - Per accedere con Apple è necessario che l'amministratore rinnovi il segreto client ogni 6 mesi. 
> - Durante l'anteprima pubblica di questa funzionalità, sarà necessario rinnovare manualmente il segreto client Apple se scade. Verrà visualizzato un avviso in anticipo nei provider di identità Apple configurare la pagina social IDP, ma è consigliabile impostare un promemoria personalizzato. 
> - Se è necessario rinnovare il segreto, aprire Azure ad B2C nella portale di Azure, passare a **Identity Providers**  >  **Apple** e selezionare **renew Secret**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità Apple a un flusso utente

Per consentire agli utenti di accedere con un ID Apple, è necessario aggiungere il provider di identità Apple a un flusso utente. L'accesso con Apple può essere configurato solo per la versione **consigliata** dei flussi utente. Per aggiungere il provider di identità Apple a un flusso utente:

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Selezionare un flusso utente per il quale si vuole aggiungere il provider di identità Apple. 
1. In **provider di identità basati su social network** selezionare **Apple (anteprima)**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Firma del segreto client

Usare il file. P8 scaricato in precedenza per firmare il segreto client in un token JWT. Sono disponibili molte librerie che consentono di creare e firmare i JWT. Usare la [funzione di Azure che crea un token](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) per l'utente. 

1. Creare una [funzione di Azure](../azure-functions/functions-create-function-app-portal.md).
1. In **Developer** selezionare **codice + test**. 
1. Copiare il contenuto del file [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) e incollarlo nell'editor.
1. Selezionare **Salva**.
1. Eseguire una `POST` richiesta HTTP e specificare le informazioni seguenti:

    - **appleTeamId**: ID del team per sviluppatori Apple
    - **appleServiceId**: ID del servizio Apple (anche ID client)
    - **p8key**: chiave di formato PEM. Per ottenere questo problema, aprire il file. P8 in un editor di testo e copiare tutti gli elementi compresi tra `-----BEGIN PRIVATE KEY-----` e `-----END PRIVATE KEY-----` senza interruzioni di riga.
 
Il codice JSON seguente è un esempio di una chiamata alla funzione di Azure:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

La funzione di Azure risponde con un segreto client JWT correttamente formattato e firmato in una risposta, ad esempio:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il segreto client registrato in precedenza nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
2. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.
1. Nella pagina **Panoramica** selezionare **Framework esperienza di identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere **manuale**.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio, "AppleSecret". Il prefisso "B2C_1A_" viene aggiunto automaticamente al nome della chiave.
1. In **Secret (segreto**) immettere il valore di un token restituito dalla funzione di Azure (un token JWT).
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

> [!IMPORTANT] 
> - Per accedere con Apple è necessario che l'amministratore rinnovi il segreto client ogni 6 mesi.
> - È necessario rinnovare manualmente il segreto client Apple se scade e archiviare il nuovo valore nella chiave dei criteri.
> - Si consiglia di impostare un promemoria personalizzato entro 6 mesi per generare un nuovo segreto client. 

## <a name="configure-apple-as-an-identity-provider"></a>Configurare Apple come provider di identità

Per consentire agli utenti di accedere con un ID Apple, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint fornisce un set di attestazioni utilizzate da Azure AD B2C per verificare che un utente specifico sia autenticato.

È possibile definire un ID Apple come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Impostare **client_id** sull'identificatore del servizio. Ad esempio: `com.consoto.azure-ad-b2c-service`.
5. Salvare il file.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
