---
title: Configurare il comportamento della sessione-Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare il comportamento della sessione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2d64e85576b35caa2262ad1d635fc72fc7e2d2b8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120624"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurare il comportamento della sessione in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Single Sign-on (SSO) consente di aggiungere sicurezza e praticità quando gli utenti possono accedere tra le applicazioni in Azure Active Directory B2C (Azure AD B2C). Questo articolo descrive i Single Sign-On metodi usati in Azure AD B2C e consente di scegliere il metodo SSO più appropriato durante la configurazione dei criteri.

Con Single Sign-On, gli utenti accedono una volta con un singolo account e ottengono l'accesso a più applicazioni. L'applicazione può essere un'applicazione Web, per dispositivi mobili o a pagina singola, indipendentemente dalla piattaforma o dal nome di dominio.

Quando l'utente accede inizialmente a un'applicazione, Azure AD B2C rende permanente una sessione basata su cookie. Dopo le successive richieste di autenticazione, Azure AD B2C legge e convalida la sessione basata su cookie e rilascia un token di accesso senza richiedere all'utente di eseguire di nuovo l'accesso. Se la sessione basata su cookie scade o diventa non valida, all'utente viene richiesto di eseguire di nuovo l'accesso.  

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Panoramica della sessione di Azure AD B2C

L'integrazione con Azure AD B2C prevede tre tipi di sessioni SSO:

- **Azure ad B2C** sessione gestita da Azure ad B2C
- **Provider di identità federato** : sessione gestita dal provider di identità, ad esempio Facebook, Salesforce o account Microsoft
- **Applicazione** -sessione gestita dall'applicazione Web, per dispositivi mobili o a pagina singola

![Sessione SSO](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C sessione 

Quando un utente esegue l'autenticazione con un account locale o di social networking, Azure AD B2C archivia una sessione basata su cookie nel browser dell'utente. Il cookie viene archiviato con il nome di dominio del tenant Azure AD B2C, ad esempio `https://contoso.b2clogin.com` .

Se un utente accede inizialmente con un account federato e quindi durante l'intervallo di tempo della sessione (time-to-Live o TTL) accede alla stessa app o a un'altra app, Azure AD B2C tenta di acquisire un nuovo token di accesso dal provider di identità federato. Se la sessione del provider di identità federata è scaduta o non è valida, il provider di identità federato richiede le credenziali dell'utente. Se la sessione è ancora attiva (o se l'utente ha eseguito l'accesso con un account locale anziché un account federato), Azure AD B2C autorizza l'utente ed Elimina ulteriori richieste.

È possibile configurare il comportamento della sessione, inclusa la sessione TTL e il modo in cui Azure AD B2C condivide la sessione tra i criteri e le applicazioni.

### <a name="federated-identity-provider-session"></a>Sessione del provider di identità federato

Un provider di identità di social networking o aziendale gestisce la propria sessione. Il cookie viene archiviato nel nome di dominio del provider di identità, ad esempio `https://login.salesforce.com` . Azure AD B2C non controlla la sessione del provider di identità federato. Al contrario, il comportamento della sessione è determinato dal provider di identità federato. 

Considerare lo scenario seguente:

1. Un utente accede a Facebook per verificare il feed.
2. Successivamente, l'utente apre l'applicazione e avvia il processo di accesso. L'applicazione reindirizza l'utente a Azure AD B2C per completare il processo di accesso.
3. Nella pagina Azure AD B2C iscrizione o accesso l'utente sceglie di eseguire l'accesso con il proprio account Facebook. L'utente viene reindirizzato a Facebook. Se è presente una sessione attiva in Facebook, all'utente non viene richiesto di fornire le proprie credenziali e viene immediatamente reindirizzato a Azure AD B2C con un token di Facebook.

### <a name="application-session"></a>Sessione dell'applicazione

Un'applicazione Web, per dispositivi mobili o a pagina singola può essere protetta tramite l'accesso OAuth, i token ID o i token SAML. Quando un utente tenta di accedere a una risorsa protetta nell'app, l'app controlla se è presente una sessione attiva sul lato applicazione. Se non è presente alcuna sessione dell'app o la sessione è scaduta, l'app consente all'utente di Azure AD B2C alla pagina di accesso.

La sessione dell'applicazione può essere una sessione basata su cookie archiviata con il nome di dominio dell'applicazione, ad esempio `https://contoso.com` . Le applicazioni per dispositivi mobili potrebbero archiviare la sessione in modo diverso, ma con un approccio simile.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Configurare il comportamento della sessione Azure AD B2C

È possibile configurare il comportamento della sessione di Azure AD B2C, tra cui:

- **Durata della sessione dell'app Web (minuti)** : periodo di tempo durante il quale il cookie della sessione di Azure ad B2C viene archiviato nel browser dell'utente dopo l'autenticazione. È possibile impostare la durata della sessione per un massimo di 24 ore.

- **Timeout della sessione dell'app Web** : indica il modo in cui una sessione viene estesa dall'impostazione della durata della sessione o l'impostazione Mantieni l'accesso (KMSI).
  - **Rolling** : indica che la sessione viene estesa ogni volta che l'utente esegue un'autenticazione basata su cookie (impostazione predefinita).
  - **Absolute** : indica che l'utente deve eseguire di nuovo l'autenticazione dopo il periodo di tempo specificato.

- **Configurazione di Single Sign-on** : la sessione di Azure ad B2C può essere configurata con gli ambiti seguenti:
  - **Tenant**: questa è l'impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e flussi utente del tenant di B2C di condividere la stessa sessione utente. Ad esempio, una volta che un utente accede a un'applicazione, l'utente può anche accedere a un altro utente in seguito all'accesso.
  - **Applicazione**: questa impostazione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, è possibile usare questa impostazione se si vuole che l'utente possa accedere a Contoso Pharmacy indipendentemente dal fatto che l'utente abbia già eseguito l'accesso a Contoso Grocers.
  - **Criterio**: questa impostazione consente di mantenere una sessione utente esclusivamente per un flusso utente, indipendentemente dalle applicazioni che lo usano. Se, ad esempio, l'utente ha già effettuato l'accesso e ha completato un passaggio di multi-factor authentication, l'utente può accedere a parti con sicurezza più elevata di più applicazioni, purché la sessione associata al flusso utente non scada.
  - **Disabilitato** : questa impostazione impone all'utente di eseguire il flusso dell'intero utente a ogni esecuzione del criterio.
- **Mantieni l'accesso (KMSI)** : estende la durata della sessione tramite l'uso di un cookie permanente. Se questa funzionalità è abilitata e l'utente la seleziona, la sessione rimane attiva anche dopo che l'utente ha chiuso e riaperto il browser. La sessione viene revocata solo quando l'utente si disconnette. La funzionalità KMSI si applica solo all'accesso con account locali. La funzionalità KMSI ha la precedenza sulla durata della sessione.

::: zone pivot="b2c-user-flow"

Per configurare il comportamento della sessione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Proprietà**.
1. Configurare la **durata della sessione dell'app Web (minuti)**, il **timeout della sessione dell'app Web**, la **configurazione dell'accesso Single Sign-on** e **richiedere il token ID nelle richieste di disconnessione** , se necessario.
1. Fare clic su **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Per modificare le configurazioni del comportamento della sessione e dell'accesso SSO, è necessario aggiungere un elemento **UserJourneyBehaviors** all'interno dell'elemento [RelyingParty](relyingparty.md).  L'elemento **UserJourneyBehaviors** deve essere immediatamente successivo a **DefaultUserJourney**. L'elemento **UserJourneyBehavors** dovrebbe essere simile all'esempio seguente:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Abilita Mantieni l'accesso (KMSI)

È possibile abilitare la funzionalità KMSI per gli utenti delle applicazioni Web e native che dispongono di account locali nella directory Azure AD B2C. Quando si Abilita la funzionalità, gli utenti possono scegliere di rimanere connessi in modo che la sessione rimanga attiva dopo la chiusura del browser. Possono quindi riaprire il browser senza che venga richiesto di immettere nuovamente il nome utente e la password. Quando l'utente si scollega, l'accesso viene revocato.

![Pagina di accesso di esempio che mostra una casella di controllo Mantieni l'accesso](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI è configurabile a livello di flusso utente singolo. Prima di abilitare KMSI per i flussi utente, tenere presente quanto segue:

- KMSI è supportato solo per le versioni **consigliate** dei flussi utente di iscrizione e accesso (Susi), di accesso e di modifica del profilo. Se si dispone attualmente di versioni di anteprima **standard** o **legacy-V2** di questi flussi utente e si vuole abilitare KMSI, è necessario creare nuove versioni **consigliate** di questi flussi utente.
- KMSI non è supportato con la reimpostazione della password o i flussi utente di iscrizione.
- Se si vuole abilitare KMSI per tutte le applicazioni nel tenant, è consigliabile abilitare KMSI per tutti i flussi utente nel tenant. Poiché un utente può presentare più criteri durante una sessione, è possibile che si verifichi un errore che non dispone di KMSI abilitato, il che rimuove il cookie KMSI dalla sessione.
- KMSI non deve essere abilitato nei computer pubblici.

### <a name="configure-kmsi-for-a-user-flow"></a>Configurare KMSI per un flusso utente

Per abilitare KMSI per il flusso utente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **directory + sottoscrizione**   nel menu in alto e scegliere la directory che contiene il tenant Azure ad B2C.
3. Scegliere **tutti i servizi**   nell'angolo superiore sinistro del portale di Azure, quindi cercare e selezionare **Azure ad B2C**.
4. Selezionare **flussi utente (criteri)**.
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.

7. In  **comportamento sessione** selezionare **Abilita sessione di accesso continuo**. Accanto a **Mantieni la sessione di accesso (giorni)** immettere un valore compreso tra 1 e 90 per specificare il numero di giorni per cui una sessione può rimanere aperta.


   ![Abilita la sessione Mantieni l'accesso](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Gli utenti dovrebbero evitare di abilitare questa funzione su un computer pubblico.

### <a name="configure-the-page-identifier"></a>Configurare l'identificatore di pagina

Per abilitare KMSI, impostare l'elemento di definizione del contenuto sull' `DataUri` [identificatore di pagina](contentdefinitions.md#datauri) `unifiedssp` e la [pagina](page-layout.md) *1.1.0* o versione successiva.

1. Aprire il file di estensione dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>. Questo file di estensione è uno dei file di criteri inclusi nello Starter Pack del criterio personalizzato, che è necessario ottenere nel prerequisito, [Introduzione ai criteri personalizzati](custom-policy-get-started.md).
1. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'elemento **ContentDefinitions** all'elemento **BuildingBlocks** del criterio.

    Il criterio personalizzato dovrebbe essere simile al frammento di codice seguente:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Aggiungere i metadati al profilo tecnico autocertificato

Per aggiungere la casella di controllo KMSI alla pagina di iscrizione e accesso, impostare i `setting.enableRememberMe` metadati su true. Eseguire l'override dei profili tecnici SelfAsserted-LocalAccountSignin-email nel file di estensione.

1. Trovare l'elemento ClaimsProviders. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di attestazioni seguente all'elemento ClaimsProviders:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Salvare il file delle estensioni.

### <a name="configure-a-relying-party-file"></a>Configurare un file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Aprire il file dei criteri personalizzati. Ad esempio, *SignUpOrSignin.xml*.
1. Se non esiste già, aggiungere un `<UserJourneyBehaviors>` nodo figlio al `<RelyingParty>` nodo. Deve trovarsi immediatamente dopo `<DefaultUserJourney ReferenceId="User journey Id" />` , ad esempio: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

È consigliabile impostare il valore di SessionExpiryInSeconds su un periodo breve (1200 secondi), mentre il valore di KeepAliveInDays può essere impostato su un periodo di tempo relativamente lungo (30 giorni), come illustrato nell'esempio seguente:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Disconnessione

Quando si desidera disconnettersi l'utente dall'applicazione, non è sufficiente cancellare i cookie dell'applicazione o terminare la sessione con l'utente. È necessario reindirizzare l'utente a Azure AD B2C per disconnettersi. In caso contrario, l'utente potrebbe essere in grado di eseguire di nuovo l'autenticazione alle applicazioni senza immettere di nuovo le credenziali.

In seguito a una richiesta di disconnessione, Azure AD B2C:

1. Invalida la sessione basata su cookie Azure AD B2C.
::: zone pivot="b2c-user-flow"
2. Tenta di disconnettersi da provider di identità federati
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Tenta di disconnettersi dai provider di identità federati:
   - OpenId Connect: se l'endpoint di configurazione noto del provider di identità specifica un `end_session_endpoint` percorso.
   - OAuth2-se i [metadati del provider di identità](oauth2-technical-profile.md#metadata) contengono il `end_session_endpoint` percorso.
   - SAML: se i [metadati del provider di identità](identity-provider-generic-saml.md) contengono il `SingleLogoutService` percorso.
4. Facoltativamente, si disconnette da altre applicazioni. Per altre informazioni, vedere la sezione [Single Sign-out](#single-sign-out) .

> [!NOTE]
> È possibile disabilitare la disconnessione dai provider di identità federati impostando i metadati del profilo tecnico del provider `SingleLogoutEnabled` di identità su `false` .
::: zone-end

La disconnessione Cancella lo stato di Single Sign-On dell'utente con Azure AD B2C, ma potrebbe non disconnettersi l'utente dalla propria sessione del provider di identità di social networking. Se l'utente seleziona lo stesso provider di identità durante un accesso successivo, potrebbe eseguire nuovamente l'autenticazione senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione, non significa necessariamente che voglia disconnettersi dal proprio account Facebook. Tuttavia, se vengono utilizzati account locali, la sessione dell'utente termina correttamente.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Single Sign-Out 

Quando si reindirizza l'utente all'endpoint di disconnessione Azure AD B2C (per i protocolli OAuth2 e SAML), Azure AD B2C Cancella la sessione dell'utente dal browser. Tuttavia, l'utente potrebbe ancora essere connesso ad altre applicazioni che usano Azure AD B2C per l'autenticazione. Per consentire a tali applicazioni di disconnettersi l'utente contemporaneamente, Azure AD B2C invia una richiesta HTTP GET all'oggetto registrato `LogoutUrl` di tutte le applicazioni a cui l'utente è attualmente connesso.

Le applicazioni devono rispondere a questa richiesta cancellando qualsiasi sessione che identifica l'utente e restituendo una risposta `200`. Se si vuole supportare l'accesso Single Sign-out nell'applicazione, è necessario implementare `LogoutUrl` nel codice dell'applicazione. 

Per supportare single sign-out, i profili tecnici dell'emittente di token per JWT e SAML devono specificare:

- Nome del protocollo, ad esempio `<Protocol Name="OpenIdConnect" />`
- Riferimento al profilo tecnico della sessione, ad esempio `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Nell'esempio seguente vengono illustrate le autorità emittenti dei token JWT e SAML con Single Sign-out:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Proteggere il reindirizzamento di disconnessione

Dopo la disconnessione, l'utente viene reindirizzato all'URI specificato nel `post_logout_redirect_uri` parametro, indipendentemente dagli URL di risposta specificati per l'applicazione. Tuttavia, se viene passato un oggetto valido `id_token_hint` e il **token ID richiesto nelle richieste di disconnessione** è attivato, Azure ad B2C verifica che il valore `post_logout_redirect_uri` corrisponda a uno degli URI di reindirizzamento configurati dell'applicazione prima di eseguire il reindirizzamento. Se per l'applicazione non è stato configurato alcun URL di risposta corrispondente, viene visualizzato un messaggio di errore e l'utente non viene reindirizzato. 

::: zone pivot="b2c-user-flow"

Per richiedere un token ID nelle richieste di disconnessione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Proprietà**.
1. Abilitare il **Richiedi token ID nelle richieste di disconnessione**.
1. Tornare alla  **Azure ad B2C**.
1. Selezionare **registrazioni app**, quindi selezionare l'applicazione.
1. Selezionare **Autenticazione**.
1. Nella casella di testo **URL di disconnessione** Digitare l'URI di reindirizzamento post-disconnessione e quindi selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Per richiedere un token ID nelle richieste di disconnessione, aggiungere un elemento **UserJourneyBehaviors** all'interno dell'elemento [RelyingParty](relyingparty.md) . Impostare quindi il **EnforceIdTokenHintOnLogout** dell'elemento **SingleSignOn** su `true` . L'elemento **UserJourneyBehaviors** dovrebbe essere simile all'esempio seguente:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Per configurare l'URL di disconnessione dell'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **registrazioni app**, quindi selezionare l'applicazione.
1. Selezionare **Autenticazione**.
1. Nella casella di testo **URL di disconnessione** Digitare l'URI di reindirizzamento post-disconnessione e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare i token in Azure ad B2C](configure-tokens.md).
