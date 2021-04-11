---
title: Configurare Azure Active Directory B2C come IdP SAML per le applicazioni
title-suffix: Azure Active Directory B2C
description: Come configurare Azure Active Directory B2C per fornire asserzioni di protocollo SAML alle applicazioni (provider di servizi). Azure AD B2C fungerà da provider di identità (IdP) singolo per l'applicazione SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 97718fef0aecd07dd364677ce1b72eb5bba78475
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384273"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrare un'applicazione SAML in Azure AD B2C

In questo articolo viene illustrato come connettere le applicazioni Security Assertion Markup Language (SAML) (provider di servizi) a Azure Active Directory B2C (Azure AD B2C) per l'autenticazione.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Panoramica

Le organizzazioni che utilizzano Azure AD B2C come soluzione di gestione delle identità e degli accessi dei clienti potrebbero richiedere l'integrazione con applicazioni che eseguono l'autenticazione tramite il protocollo SAML. Il diagramma seguente illustra il modo in cui Azure AD B2C funge da *provider di identità* (IDP) per ottenere l'accesso Single Sign-on (SSO) con le applicazioni basate su SAML.

![Diagramma con B2C come provider di identità a sinistra e B2C come provider di servizi a destra.](media/saml-service-provider/saml-service-provider-integration.png)

1. L'applicazione crea una richiesta di autenticazione SAML inviata a Azure AD endpoint di accesso SAML B2C.
2. L'utente può utilizzare un account locale Azure AD B2C o qualsiasi altro provider di identità federato (se configurato) per l'autenticazione.
3. Se l'utente accede utilizzando un provider di identità federato, viene inviata una risposta di token a Azure AD B2C.
4. Azure AD B2C genera un'asserzione SAML e la invia all'applicazione.

## <a name="prerequisites"></a>Prerequisiti

* Completare la procedura descritta in [Introduzione ai criteri personalizzati in Azure AD B2C](custom-policy-get-started.md). Per i criteri personalizzati descritti nell'articolo, sono necessari i criteri personalizzati *SocialAndLocalAccounts* del pacchetto Starter.
* Conoscenza di base del protocollo SAML e della familiarità con l'implementazione SAML dell'applicazione.
* Applicazione Web configurata come applicazione SAML. Per questa esercitazione, è possibile usare un'[applicazione di test SAML][samltest] fornita.

## <a name="components"></a>Componenti

Per questo scenario sono necessari tre componenti principali:

* Un' **applicazione** SAML con la possibilità di inviare richieste AuthN SAML e ricevere, decodificare e verificare le risposte saml da Azure ad B2C. L'applicazione SAML è nota anche come provider di servizi o applicazione relying party.
* L' **endpoint di metadati** SAML disponibile pubblicamente nell'applicazione SAML o il documento XML.
* [Tenant di Azure ad B2C](tutorial-create-tenant.md)

Se non si dispone ancora di un'applicazione SAML e di un endpoint di metadati associato, è possibile usare questa applicazione SAML di esempio che è stata resa disponibile per il test:

[Applicazione di test SAML][samltest]

## <a name="set-up-certificates"></a>Configurare i certificati

Per creare una relazione di trust tra l'applicazione e Azure AD B2C, entrambi i servizi devono essere in grado di creare e convalidare le firme reciproche. È possibile configurare una configurazione dei certificati X509 in Azure AD B2C e nell'applicazione.

**Certificati delle applicazioni**

| Utilizzo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Firma della richiesta SAML  | No | Un certificato con una chiave privata archiviata nell'app Web, usata dall'applicazione per firmare le richieste SAML inviate a Azure AD B2C. L'app Web deve esporre la chiave pubblica tramite il relativo endpoint dei metadati SAML. Azure AD B2C convalida la firma della richiesta SAML usando la chiave pubblica dei metadati dell'applicazione.|
| Crittografia asserzione SAML  | No | Un certificato con una chiave privata archiviata nell'app Web. L'app Web deve esporre la chiave pubblica tramite il relativo endpoint dei metadati SAML. Azure AD B2C possibile crittografare le asserzioni nell'applicazione usando la chiave pubblica. L'applicazione usa la chiave privata per decrittografare l'asserzione.|

**Certificati di Azure AD B2C**

| Utilizzo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Firma della risposta SAML | Sì  | Un certificato con una chiave privata archiviata in Azure AD B2C. Questo certificato viene usato da Azure AD B2C per firmare la risposta SAML inviata all'applicazione. L'applicazione legge la chiave pubblica dei metadati Azure AD B2C per convalidare la firma della risposta SAML. |
| Firma asserzione SAML | Sì | Un certificato con una chiave privata archiviata in Azure AD B2C. Questo certificato viene usato da Azure AD B2C per firmare l'asserzione della risposta SAML. `<saml:Assertion>`Parte della risposta SAML.  |

In un ambiente di produzione è consigliabile usare i certificati emessi da un'autorità di certificazione pubblica. Tuttavia, è anche possibile completare questa procedura con certificati autofirmati.

### <a name="create-a-policy-key"></a>Creare una chiave dei criteri

Per avere una relazione di trust tra l'applicazione e Azure AD B2C, creare un certificato di firma della risposta SAML. Azure AD B2C usa questo certificato per firmare la risposta SAML inviata all'applicazione. L'applicazione legge la chiave pubblica dei metadati Azure AD B2C per convalidare la firma della risposta SAML. 

> [!TIP]
> È possibile usare la chiave dei criteri creata in questa sezione, per altri scopi, ad esempio l' [asserzione SAML](saml-service-provider-options.md#saml-assertions-signature). 

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
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SamlIdpCert`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
1. Individuare e selezionare il file di certificato con estensione pfx con la chiave privata.
1. Fare clic su **Crea**.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Abilitare i criteri per la connessione a un'applicazione SAML

Per connettersi all'applicazione SAML, Azure AD B2C necessario poter creare risposte SAML.

Aprire `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** nel pacchetto Starter di criteri personalizzati.

Individuare la `<ClaimsProviders>` sezione e aggiungere il frammento di codice XML seguente per implementare il generatore di risposta SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Configurare il IssuerUri della risposta SAML

È possibile modificare il valore dell' `IssuerUri` elemento dei metadati nel profilo tecnico dell'emittente del token SAML. Questa modifica verrà applicata all' `issuerUri` attributo restituito nella risposta SAML da Azure ad B2C. L'applicazione deve essere configurata in modo da accettare lo stesso `issuerUri` durante la convalida della risposta SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configurare i criteri per emettere una risposta SAML

Ora che i criteri possono creare risposte SAML, è necessario configurare il criterio per emettere una risposta SAML invece della risposta JWT predefinita per l'applicazione.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Creare un criterio di iscrizione o di accesso configurato per SAML

1. Creare una copia del file *SignUpOrSignin.xml* nella directory di lavoro del pacchetto Starter e salvarla con un nuovo nome. Ad esempio, *SignUpOrSigninSAML.xml*. Questo file è il file dei criteri di relying party ed è configurato per l'emissione di una risposta JWT per impostazione predefinita.

1. Aprire il file *SignUpOrSigninSAML.xml* in un editor a scelta.

1. Cambiare i valori `PolicyId` e `PublicPolicyUri` dei criteri in _B2C_1A_signup_signin_saml_ e `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`, come illustrato di seguito.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Alla fine del percorso utente, Azure AD B2C contiene un `SendClaims` passaggio. Questo passaggio fa riferimento al profilo tecnico dell'emittente del token. Per emettere una risposta SAML invece della risposta JWT predefinita, modificare il `SendClaims` passaggio in modo che faccia riferimento al nuovo profilo tecnico dell'emittente del token SAML `Saml2AssertionIssuer` .

Aggiungere il frammento di codice XML seguente immediatamente prima dell' `<RelyingParty>` elemento. Questo XML sovrascrive il passaggio di orchestrazione numero 7 nel percorso utente _SignUpOrSignIn_ . Se è stato avviato da una cartella diversa nello Starter Pack oppure è stato personalizzato il percorso utente aggiungendo o rimuovendo passaggi di orchestrazione, verificare che il numero nell' `order` elemento corrisponda al numero specificato nel percorso utente per il passaggio dell'emittente del token. Ad esempio, nelle altre cartelle dello Starter Pack, il numero del passaggio corrispondente è 4 per `LocalAccounts` , 6 per `SocialAccounts` e 9 per `SocialAndLocalAccountsWithMfa` .

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

L'elemento relying party determina il protocollo usato dall'applicazione. Il valore predefinito è `OpenId`. L' `Protocol` elemento deve essere modificato in `SAML` . Le attestazioni di output creeranno il mapping delle attestazioni all'asserzione SAML.

Sostituire l'intero elemento `<TechnicalProfile>` nell'elemento `<RelyingParty>` con il seguente codice XML del profilo tecnico. Aggiornare `tenant-name` con il nome del tenant di Azure AD B2C.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Il file dei criteri di relying party finale dovrebbe essere simile al codice XML seguente:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> È possibile seguire lo stesso processo per implementare altri tipi di flussi utente (ad esempio, accesso, reimpostazione della password o flussi di modifica del profilo).

### <a name="upload-your-policy"></a>Caricare i criteri

Salvare le modifiche e caricare i nuovi file di criteri di **TrustFrameworkExtensions.xml** e **SignUpOrSigninSAML.xml** nel portale di Azure.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testare i metadati SAML di Azure AD B2C IdP

Una volta caricati i file dei criteri, Azure AD B2C usa le informazioni di configurazione per generare il documento di metadati SAML del provider di identità che verrà usato dall'applicazione. Il documento di metadati SAML contiene i percorsi dei servizi, ad esempio i metodi di accesso e disconnessione, i certificati e così via.

I metadati dei criteri Azure AD B2C sono disponibili all'URL seguente:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Sostituire `<tenant-name>` con il nome del tenant Azure ad B2C e `<policy-name>` con il nome (ID) del criterio, ad esempio:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrare l'applicazione SAML in Azure AD B2C

Per Azure AD B2C l'attendibilità dell'applicazione, è necessario creare una registrazione dell'applicazione Azure AD B2C, che contiene informazioni di configurazione, ad esempio l'endpoint dei metadati dell'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *SAMLApp1*.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**.
1. In **URI di reindirizzamento** selezionare **Web** e quindi immettere `https://localhost`. Questo valore verrà modificato in un secondo momento nel manifesto della registrazione dell'applicazione.
1. Selezionare **Registra**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Configurare l'applicazione in Azure AD B2C

Per le app SAML è necessario configurare diverse proprietà nel manifesto della registrazione dell'applicazione.

1. Nel [portale di Azure](https://portal.azure.com) passare alla registrazione dell'applicazione creata nella sezione precedente.
1. In **Gestisci** selezionare **manifesto** per aprire l'editor del manifesto, quindi modificare le proprietà descritte nelle sezioni riportate di seguito.

#### <a name="add-the-identifier"></a>Aggiungere l'identificatore

Quando l'applicazione SAML esegue una richiesta di Azure AD B2C, la richiesta di autenticazione SAML include un `Issuer` attributo, che in genere corrisponde al valore dei metadati dell'applicazione `entityID` . Azure AD B2C utilizza questo valore per cercare la registrazione dell'applicazione nella directory e leggere la configurazione. Affinché la ricerca abbia esito positivo, `identifierUri` è necessario che nella registrazione dell'applicazione venga compilato un valore che corrisponda all' `Issuer` attributo.

Nel manifesto di registrazione individuare il `identifierURIs` parametro e aggiungere il valore appropriato. Questo valore sarà uguale a quello configurato nelle richieste di autenticazione SAML per EntityId in corrispondenza dell'applicazione e il `entityID` valore nei metadati dell'applicazione.

Nell'esempio seguente viene illustrato l'oggetto `entityID` nei metadati SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

La `identifierUris` proprietà accetterà solo URL nel dominio `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Condividere i metadati dell'applicazione con Azure AD B2C

Dopo che la registrazione dell'applicazione è stata caricata da `identifierUri` , Azure ad B2C usa i metadati dell'applicazione per convalidare la richiesta AuthN SAML e determinare la modalità di risposta.

È consigliabile che l'applicazione esponga un endpoint di metadati accessibile pubblicamente.

Se sono presenti proprietà *specificate nell'URL* dei metadati SAML e nel manifesto della registrazione dell'applicazione, vengono *Unite*. Le proprietà specificate nell'URL dei metadati vengono elaborate per prime e hanno la precedenza.

Usando l'applicazione di test SAML come esempio, è possibile usare il valore seguente per `samlMetadataUrl` nel manifesto dell'applicazione:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Eseguire l'override o impostare l'URL del consumer di asserzione (facoltativo)

È possibile configurare l'URL di risposta a cui Azure AD B2C Invia risposte SAML. Gli URL di risposta possono essere configurati nel manifesto dell'applicazione. Questa configurazione è utile quando l'applicazione non espone un endpoint di metadati accessibile pubblicamente.

L'URL di risposta per un'applicazione SAML è l'endpoint a cui l'applicazione prevede di ricevere risposte SAML. L'applicazione in genere fornisce questo URL nel documento di metadati sotto l' `AssertionConsumerServiceUrl` attributo, come illustrato di seguito:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Se si desidera eseguire l'override dei metadati forniti nell' `AssertionConsumerServiceUrl` attributo o l'URL non è presente nel documento di metadati, è possibile configurare l'URL nel manifesto nella `replyUrlsWithType` Proprietà. `BindingType`Verrà impostato su `HTTP POST` .

Usando l'applicazione di test SAML come esempio, impostare la `url` proprietà di sul `replyUrlsWithType` valore mostrato nel frammento di codice JSON seguente.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Eseguire l'override o impostare l'URL di disconnessione (facoltativo)

È possibile configurare l'URL di disconnessione a cui Azure AD B2C invierà l'utente dopo una richiesta di disconnessione. Gli URL di risposta possono essere configurati nel manifesto dell'applicazione.

Se si desidera eseguire l'override dei metadati forniti nell' `SingleLogoutService` attributo o l'URL non è presente nel documento di metadati, è possibile configurarlo nel manifesto sotto la `Logout` Proprietà. `BindingType`Verrà impostato su `Http-Redirect` .

L'applicazione in genere fornisce questo URL nel documento di metadati sotto l' `AssertionConsumerServiceUrl` attributo, come illustrato di seguito:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Usando l'applicazione di test SAML come esempio, è possibile lasciare `logoutUrl` impostato su `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Se si sceglie di configurare l'URL di risposta e l'URL di disconnessione nel manifesto dell'applicazione senza popolare l'endpoint dei metadati dell'applicazione tramite la `samlMetadataUrl` proprietà, Azure ad B2C non convaliderà la firma della richiesta SAML, né verrà crittografata la risposta SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Configurare Azure AD B2C come IdP SAML nell'applicazione SAML

L'ultimo passaggio consiste nell'abilitare Azure AD B2C come IdP SAML nell'applicazione SAML. Ogni applicazione è diversa e i passaggi variano. Per informazioni dettagliate, vedere la documentazione dell'app.

I metadati possono essere configurati nell'applicazione come metadati *statici* o *metadati dinamici*. In modalità statica copiare tutti i metadati o parte di essi dai metadati dei criteri Azure AD B2C. In modalità dinamica specificare l'URL dei metadati e consentire all'applicazione di leggere i metadati in modo dinamico.

In genere sono necessari alcuni o tutti gli elementi seguenti:

* **Metadati**: usare il formato `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Emittente**: il valore della richiesta SAML `issuer` deve corrispondere a uno degli URI configurati nell' `identifierUris` elemento del manifesto di registrazione dell'applicazione. Se il nome della richiesta SAML `issuer` non esiste nell' `identifierUris` elemento, [aggiungerlo al manifesto di registrazione dell'applicazione](#add-the-identifier). Ad esempio: `https://contoso.onmicrosoft.com/app-name`. 
* **URL di accesso/endpoint SAML/URL SAML**: controllare il valore nel file di metadati dei criteri SAML Azure ad B2C per l' `<SingleSignOnService>` elemento XML.
* **Certificato**: questo certificato è *B2C_1A_SamlIdpCert*, ma senza la chiave privata. Per ottenere la chiave pubblica del certificato:

    1. Passare all'URL dei metadati specificato sopra.
    1. Copiare il valore nell'elemento `<X509Certificate>`.
    1. Incollarlo in un file di testo.
    1. Salvare il file di testo come file con estensione *cer*.

### <a name="test-with-the-saml-test-app"></a>Eseguire test con l'app di test SAML

È possibile usare l' [applicazione di test SAML][samltest] per testare la configurazione:

* Aggiornare il nome del tenant.
* Aggiornare il nome del criterio, ad esempio *B2C_1A_signup_signin_saml*.
* Specificare l'URI dell'autorità emittente. Usare uno degli URI trovati nell' `identifierUris` elemento nel manifesto di registrazione dell'applicazione, ad esempio `https://contoso.onmicrosoft.com/app-name` .

Selezionare **Accedi** e dovrebbe essere visualizzata una schermata di accesso utente. Quando si esegue l'accesso, viene rilasciata una risposta SAML all'applicazione di esempio.

## <a name="supported-and-unsupported-saml-modalities"></a>Modalità SAML supportate e non

Gli scenari di applicazione SAML seguenti sono supportati tramite un endpoint di metadati personalizzato:

* Più URL di disconnessione o POST-binding per l'URL di disconnessione nell'oggetto applicazione/entità servizio.
* Specificare una chiave di firma per verificare relying party richieste (RP) nell'oggetto applicazione/entità servizio.
* Specificare una chiave di crittografia del token nell'oggetto applicazione/entità servizio.
* Accesso avviato dal provider di identità, in cui il provider di identità viene Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere l'app Web di test SAML nel [repository della community GitHub per Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).
- Vedere le [Opzioni per la registrazione di un'applicazione SAML in Azure ad B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
