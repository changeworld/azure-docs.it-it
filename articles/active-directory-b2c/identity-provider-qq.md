---
title: Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account QQ alle applicazioni da Azure Active Directory B2C.
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
ms.openlocfilehash: d4d4ae72594532056c0e09a78898abb68ebc1dcf
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584766"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare un account QQ come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si ha già un account QQ, è possibile iscriversi all'indirizzo [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

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
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se `tenant_name` è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
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

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Se si vuole che gli utenti possano accedere usando un account QQ, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account QQ come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri.

1. Aprire *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAUTH">
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

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappiano come comunicare con l'account QQ. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

Il provider di identità è a questo punto configurato, ma non è disponibile in alcuna delle schermate di iscrizione/accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che includa anche il provider di identità QQ.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInQQ`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account QQ, viene visualizzato un nuovo pulsante quando un utente atterra nella pagina.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `QQExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nell'Azure AD B2C comunicare con un account QQ per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore che è stato usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza. Ad esempio: `QQ-OAuth`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-qq-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità QQ a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente che si vuole usare per il provider di identità QQ.
1. In provider di identità basati su **Social Network** selezionare **QQ**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, rinominarlo in *SignUpSignInQQ.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInQQ`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_QQ`
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente creato (SignUpSignQQ).
1. Salvare le modifiche, caricare il file.
1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui adesso** e selezionare QQ per accedere con QQ e testare i criteri personalizzati.

::: zone-end
