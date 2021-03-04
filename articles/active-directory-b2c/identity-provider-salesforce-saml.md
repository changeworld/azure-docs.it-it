---
title: Configurare l'accesso con un provider SAML Salesforce usando il protocollo SAML
titleSuffix: Azure AD B2C
description: Configurare l'accesso con un provider SAML di Salesforce usando il protocollo SAML in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cfdd96ffa9e7758fc594f7e7ae53c84495b8b2e8
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095409"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Configurare l'accesso con un provider SAML di Salesforce usando il protocollo SAML in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come consentire l'accesso agli utenti di un'organizzazione Salesforce usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). Per abilitare l'accesso, è necessario aggiungere un [provider di identità SAML](identity-provider-generic-saml.md) a un criterio personalizzato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Se non è già stato fatto, iscriversi per ottenere un [account Developer Edition gratuito](https://developer.salesforce.com/signup). Questo articolo usa [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configurazione di un dominio personale](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) per la propria organizzazione Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Configurare Salesforce come provider di identità

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/).
2. Nel menu a sinistra espandere **Identity** (Identità) in **Settings** (Impostazioni) e quindi selezionare **Identity Provider** (Provider di identità).
3. Selezionare **Enable Identity Provider** (Abilita provider di identità).
4. In **Select the certificate** (Selezionare il certificato) selezionare il certificato che si desidera venga usato da Salesforce per comunicare con Azure AD B2C. È possibile usare il certificato predefinito.
5. Fare clic su **Salva**.

### <a name="create-a-connected-app-in-salesforce"></a>Creare un'app connessa in Salesforce

1. Nella pagina **Identity Provider** (Provider di identità) selezionare **Service Providers are now created via Connected Apps. Click here** (I provider di servizi vengono ora creati tramite app connesse. Fare clic qui).
2. In **Basic Information** (Informazioni di base) immettere i valori richiesti per l'app connessa.
3. In **Web App Settings** (Impostazioni app Web) selezionare la casella **Enable SAML** (Abilita SAML).
4. Nel campo **Entity ID** (ID entità) immettere l'URL seguente. Assicurarsi di sostituire il valore `your-tenant` con il nome del tenant di Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Nel campo **ACS URL** (URL ACS) immettere l'URL seguente. Assicurarsi di sostituire il valore `your-tenant` con il nome del tenant di Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Scorrere l'elenco fino alla fine e quindi fare clic su **Save** (Salva).

### <a name="get-the-metadata-url"></a>Ottenere l'URL dei metadati

1. Nella pagina di panoramica dell'app connessa fare clic su **Manage** (Gestisci).
2. Copiare il valore per **Metadata Discovery Endpoint** (Endpoint di individuazione dei metadati) e salvarlo. Questo valore sarà usato più avanti in questo articolo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurare gli utenti di Salesforce per la federazione

1. Nella pagina **Manage** (Gestisci) dell'app connessa fare clic su **Manage Profiles** (Gestisci profili).
2. Selezionare i profili (o gruppi di utenti) di cui si intende eseguire la federazione con Azure AD B2C. Come amministratore di sistema, è necessario selezionare la casella per **System Administrator** (Amministratore di sistema) in modo che sia possibile eseguire la federazione con l'account di Salesforce.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il certificato creato nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Upload`.
7. Immettere un **nome** per il criterio. Ad esempio, SAMLSigningCert. Al nome della chiave viene aggiunto automaticamente il prefisso `B2C_1A_`.
8. Cercare e selezionare il certificato B2CSigningCert.pfx creato.
9. Immettere la **password** per il certificato.
3. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account Salesforce, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account Salesforce come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri. Per altre informazioni, vedere [definire un provider di identità SAML](identity-provider-generic-saml.md).

1. Aprire *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
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

1. Aggiornare il valore di **PartnerEntity** con l'URL dei metadati di Salesforce copiato in precedenza.
1. Aggiornare il valore di entrambe le istanze di **StorageReferenceId** con il nome della chiave del certificato di firma. Ad esempio, B2C_1A_SAMLSigningCert.
1. Individuare la sezione `<ClaimsProviders>` e aggiungere il frammento XML seguente. Se il criterio contiene già il profilo tecnico `SM-Saml-idp`, andare al passaggio successivo. Per altre informazioni, vedere [Gestione delle sessioni Single Sign-On](custom-policy-reference-sso.md).

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
1. Salvare il file.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end