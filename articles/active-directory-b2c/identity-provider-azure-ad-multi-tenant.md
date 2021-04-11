---
title: Configurare l'accesso per la Azure AD multi-tenant mediante criteri personalizzati
titleSuffix: Azure AD B2C
description: Aggiungere un provider di identità di Azure AD multi-tenant usando criteri personalizzati in Azure Active Directory B2C.
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
ms.openlocfilehash: 9d9ba7e3e898da8d5b7d1bb4fbc69554ee205147
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028333"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso per Azure Active Directory multi-tenant usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Questo articolo illustra come abilitare l'accesso per gli utenti che usano l'endpoint multi-tenant per Azure Active Directory (Azure AD). Consentire agli utenti di più tenant Azure AD di accedere usando Azure AD B2C, senza dover configurare un provider di identità per ogni tenant. Tuttavia, i membri guest di questi tenant **non** saranno in grado di accedere. A tale scopo, è necessario [configurare singolarmente ogni tenant](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Registrare un'applicazione

Per abilitare l'accesso per gli utenti con un account Azure AD in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [registrare un'applicazione con la piattaforma di identità Microsoft](../active-directory/develop/quickstart-register-app.md).


1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD aziendale (ad esempio, contoso.com). Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Selezionare **account in qualsiasi directory organizzativa (qualsiasi Azure ad directory – multi-tenant)** per questa applicazione.
1. Per l' **URI di reindirizzamento** accettare il valore di **Web** e immettere l'URL seguente in lettere minuscole, dove `your-B2C-tenant-name` viene sostituito con il nome del tenant Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Se si usa un [dominio personalizzato](custom-domain.md), immettere `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Sostituire `your-domain-name` con il dominio personalizzato e `your-tenant-name` con il nome del tenant.

1. Selezionare **Registra**. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.
1. Selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**.
1. Immettere una **Descrizione** per il segreto, selezionare una scadenza e quindi selezionare **Aggiungi**. Registrare il **valore** del segreto da usare in un passaggio successivo.

## <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si desidera ottenere le `family_name` `given_name` attestazioni, e da Azure ad, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente di portale di Azure o nel manifesto dell'applicazione. Per altre informazioni vedere [Procedura: fornire attestazioni facoltative all'app Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si vogliono configurare le attestazioni facoltative nell'elenco.
1. Nella sezione **Gestisci** selezionare **Configurazione del token**.
1. Selezionare **Aggiungi un'attestazione facoltativa**.
1. Per il **tipo di token** selezionare **ID**.
1. Selezionare le attestazioni facoltative da aggiungere, `family_name` e `given_name` .
1. Scegliere **Aggiungi**.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave dell'applicazione creata nel tenant di Azure AD B2C.

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `AADAppSecret`.  Il prefisso `B2C_1A_` viene aggiunto automaticamente al nome della chiave quando viene creato, quindi il riferimento nel codice XML nella sezione seguente consiste nel *B2C_1A_AADAppSecret*.
1. In **Secret** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Create** (Crea).

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

Per consentire agli utenti di accedere con un account di Azure AD, è necessario definire Azure AD come provider di attestazioni con cui Azure AD B2C possibile comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo Azure AD all'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Nell'elemento **ClaimsProvider** aggiornare il valore di **Domain** con un valore univoco che ne consenta la distinzione rispetto ad altri provider di identità.
1. Nell'elemento **TechnicalProfile** aggiornare il valore di **DisplayName**, ad esempio `Contoso Employee` . Questo valore verrà visualizzato sul pulsante di accesso nella pagina di accesso.
1. Impostare **client_id** sull'ID applicazione della Azure ad applicazione multi-tenant registrata in precedenza.
1. In **CryptographicKeys** aggiornare il valore di **ID riferimento archiviazione** con il nome della chiave dei criteri creata in precedenza. Ad esempio: `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Limitare l'accesso

L'uso di `https://login.microsoftonline.com/` come valore di **ValidTokenIssuerPrefixes** consente a tutti gli utenti di Azure AD di accedere all'applicazione. Aggiornare l'elenco delle autorità emittenti di token valide e limitare l'accesso a un elenco specifico di Azure AD utenti tenant che possono accedere.

Per ottenere i valori, esaminare i metadati di individuazione di OpenID Connect per ognuno dei tenant di Azure AD a cui si vuole accedere gli utenti. Il formato dell'URL dei metadati è simile a `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , dove `your-tenant` è il nome del tenant Azure ad. Ad esempio:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Eseguire questi passaggi per ogni tenant Azure AD da usare per l'accesso:

1. Aprire il browser e passare all'URL dei metadati di OpenID Connect per il tenant. Individuare l'oggetto **emittente** e registrarne il valore. Dovrebbe essere simile a `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Copiare e incollare il valore nella chiave **ValidTokenIssuerPrefixes** . Separare più autorità emittenti con una virgola. Nell'esempio XML precedente viene visualizzato un esempio con due autorità di certificazione `ClaimsProvider` .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](tutorial-register-applications.md). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o di accesso selezionare **AAD comune** per accedere con Azure ad account.

Per testare la funzionalità di accesso multi-tenant, eseguire gli ultimi due passaggi usando le credenziali per un utente che esiste un altro tenant Azure AD. Copiare l' **endpoint Run Now** e aprirlo in una finestra del browser privata, ad esempio in modalità in incognito in Google Chrome o in una finestra InPrivate in Microsoft Edge. L'apertura in una finestra del browser privata consente di testare il percorso utente completo senza usare le credenziali Azure AD attualmente memorizzate nella cache.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

## <a name="next-steps"></a>Passaggi successivi

Quando si usano criteri personalizzati, a volte è necessario disporre di informazioni aggiuntive per la risoluzione dei problemi relativi a un criterio durante lo sviluppo.

Per semplificare la diagnosi dei problemi, è possibile impostare temporaneamente i criteri in "modalità di sviluppo" e raccogliere i log con applicazione Azure Insights. Scopri come [Azure Active Directory B2C: raccolta di log](troubleshoot-with-application-insights.md).

::: zone-end
