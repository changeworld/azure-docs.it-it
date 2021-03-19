---
title: Configurare l'iscrizione e l'accesso con il provider di identità SAML
titleSuffix: Azure Active Directory B2C
description: Configurare l'iscrizione e l'accesso con qualsiasi provider di identità SAML (IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a66486d791968f5752b96ed00374f8662b9c30fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580046"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con il provider di identità SAML usando Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) supporta la Federazione con i provider di identità SAML 2,0. Questo articolo illustra come abilitare l'accesso con un account utente del provider di identità SAML, consentendo agli utenti di accedere con le identità aziendali o di social networking esistenti, ad esempio [ADFS](./identity-provider-adfs.md) e [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Panoramica dello scenario

È possibile configurare Azure AD B2C per consentire agli utenti di accedere all'applicazione con credenziali provenienti da provider di identità SAML (Social Network) esterni o Enterprise. Quando Azure AD B2C viene eseguita la Federazione con un provider di identità SAML, funge da **provider di servizi** che avvia una richiesta SAML al **provider di identità** SAML e in attesa di una risposta SAML. Nel diagramma seguente:

1. L'applicazione avvia una richiesta di autorizzazione per Azure AD B2C. L'applicazione può essere un'applicazione [OAuth 2,0](protocols-overview.md) o [OpenID Connect](openid-connect.md) o un [provider di servizi SAML](saml-service-provider.md). 
1. Nella pagina di accesso Azure AD B2C l'utente sceglie di eseguire l'accesso con un account del provider di identità SAML, ad esempio *Contoso*. Azure AD B2C avvia una richiesta di autorizzazione SAML e porta l'utente al provider di identità SAML per completare l'accesso.
1. Il provider di identità SAML restituisce una risposta SAML.
1. Azure AD B2C convalida il token SAML, estrae le attestazioni, emette il proprio token e riporta l'utente all'applicazione.  

![Accedi con il flusso del provider di identità SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Componenti della soluzione

Per questo scenario sono necessari i componenti seguenti:

* Un **provider di identità** SAML con la possibilità di ricevere, decodificare e rispondere alle richieste saml da Azure ad B2C.
* **Endpoint di metadati** SAML disponibile pubblicamente per il provider di identità.
* [Tenant Azure ad B2C](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

Per stabilire una relazione di trust tra Azure AD B2C e il provider di identità SAML, è necessario fornire un certificato X509 valido con la chiave privata. Azure AD B2C firma le richieste SAML, usando la chiave privata del certificato. Il provider di identità convalida la richiesta utilizzando la chiave pubblica del certificato. La chiave pubblica è accessibile tramite i metadati del profilo tecnico. In alternativa, è possibile caricare manualmente il file con estensione cer nel provider di identità SAML.

Un certificato autofirmato è accettabile per la maggior parte degli scenari. Per gli ambienti di produzione, è consigliabile usare un certificato X509 emesso da un'autorità di certificazione. Come descritto più avanti in questo documento, per un ambiente non di produzione è anche possibile disabilitare la firma SAML per entrambe le entità.

### <a name="obtain-a-certificate"></a>Ottenere un certificato

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Caricare il certificato

È necessario archiviare il certificato nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Upload`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SAMLSigningCert`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. Individuare e selezionare il file di certificato con estensione pfx con la chiave privata.
1. Fare clic su **Crea**.

## <a name="configure-the-saml-technical-profile"></a>Configurare il profilo tecnico SAML

Definire il provider di identità SAML aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri. I provider di attestazioni contengono un profilo tecnico SAML che determina gli endpoint e i protocolli necessari per comunicare con il provider di identità SAML. Per aggiungere un provider di attestazioni con un profilo tecnico SAML:

1. Aprire *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Aggiornare gli elementi XML seguenti con il valore pertinente:

|Elemento XML  |Valore  |
|---------|---------|
|ClaimsProvider\Domain  | Nome di dominio utilizzato per l' [accesso diretto](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Immettere il nome di dominio che si vuole usare nell'accesso diretto. Ad esempio, *contoso.com*. |
|TechnicalProfile\DisplayName|Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso. Ad esempio, *Contoso*. |
|Metadata\PartnerEntity|URL dei metadati del provider di identità SAML. In alternativa, è possibile copiare i metadati del provider di identità e aggiungerli all'interno dell'elemento CDATA `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Eseguire il mapping delle attestazioni

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità SAML. Mappare il nome dell'attestazione definita nei criteri al nome dell'asserzione definito nel provider di identità. Controllare il provider di identità per l'elenco di attestazioni (asserzioni). Per ulteriori informazioni, vedere [mapping delle attestazioni](identity-provider-generic-saml-options.md#claims-mapping).

Nell'esempio precedente *Contoso-Saml2* include le attestazioni restituite da un provider di identità SAML:

* Viene eseguito il mapping dell'attestazione **issuerUserId** all'attestazione **assertionSubjectName** .
* Il mapping dell'attestazione **first_name** viene eseguito per l'attestazione **givenName**.
* Il mapping dell'attestazione **last_name** viene eseguito per l'attestazione **surname**.
* Viene eseguito il mapping dell'attestazione **DisplayName** all' `http://schemas.microsoft.com/identity/claims/displayname` attestazione.
* L'attestazione **email** senza eseguire il mapping del nome.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità:

* L'attestazione **identityProvider** che contiene il nome del provider di identità.
* L'attestazione **authenticationSource** con un valore predefinito di **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Aggiungere il profilo tecnico della sessione SAML

Se non si dispone già del `SM-Saml-idp` profilo tecnico della sessione SAML, aggiungerne uno ai criteri di estensione. Individuare la sezione `<ClaimsProviders>` e aggiungere il frammento XML seguente. Se il criterio contiene già il profilo tecnico `SM-Saml-idp`, andare al passaggio successivo. Per altre informazioni, vedere [Gestione delle sessioni Single Sign-On](custom-policy-reference-sso.md).

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Configurare il provider di identità SAML

Dopo aver configurato i criteri, è necessario configurare il provider di identità SAML con i metadati SAML Azure AD B2C. I metadati SAML sono informazioni utilizzate nel protocollo SAML per esporre la configurazione dei criteri, il provider di **Servizi**. Definisce la posizione dei servizi, ad esempio l'accesso e la disconnessione, i certificati, il metodo di accesso e altro ancora.

Ogni provider di identità SAML prevede diversi passaggi per l'impostazione di un provider di servizi. Alcuni provider di identità SAML chiedono i metadati del Azure AD B2C, mentre altri richiedono di eseguire manualmente il file di metadati e fornire le informazioni. Per informazioni aggiuntive, vedere la documentazione del provider di identità.

Nell'esempio seguente viene illustrato un indirizzo URL per i metadati SAML di un Azure AD B2C profilo tecnico:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Quando si usa un [dominio personalizzato](custom-domain.md), usare il formato seguente:

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Sostituire i valori seguenti:

- **nome-tenant** con il nome del tenant, ad esempio your-tenant.onmicrosoft.com.
- **nome di dominio** con il nome di dominio personalizzato, ad esempio login.contoso.com.
- **your-policy** con il nome dei criteri. Ad esempio, B2C_1A_signup_signin_adfs.
- **profilo tecnico** con il nome del profilo tecnico del provider di identità SAML. Ad esempio, Contoso-SAML2.

Aprire un browser e passare all'URL. Assicurarsi di digitare l'URL corretto e di avere accesso al file dei metadati XML.

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **criteri** selezionare **Framework esperienza di identità**
1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](troubleshoot-custom-policies.md#troubleshoot-the-runtime). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o accesso selezionare **Contoso** per accedere con l'account contoso.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le opzioni del provider di identità SAML con Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end