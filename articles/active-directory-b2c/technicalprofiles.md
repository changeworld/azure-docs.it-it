---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Specificare l'elemento Technicalprofiles di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5eff20ecb1366114ead80877b684ef512742803b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805395"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico fornisce un Framework con un meccanismo incorporato per la comunicazione con diversi tipi di entità. I profili tecnici vengono usati per comunicare con il tenant di Azure AD B2C, creare un utente o leggere un profilo utente. Un profilo tecnico può essere autocertificato per consentire l'interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso e quindi eseguire il rendering della pagina di iscrizione o di reimpostazione della password.

## <a name="type-of-technical-profiles"></a>Tipi di profili tecnici

Un profilo tecnico supporta i tipi di scenario riportati di seguito.

- [Application Insights](analytics-with-application-insights.md) l'invio di dati di evento a [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md): supporto della gestione degli utenti di Azure Active Directory B2C.
- [Azure AD multi-factor authentication](multi-factor-auth-technical-profile.md) : viene fornito il supporto per la verifica di un numero di telefono utilizzando Azure ad multi-factor authentication (multi-factor authentication). 
- [Trasformazione delle attestazioni](claims-transformation-technical-profile.md): chiamata di trasformazioni delle attestazioni di output per convalidare le attestazioni, modificarne i valori o impostare valori predefiniti per un set di attestazioni di output.
- [Hint token ID](id-token-hint.md) : convalida la `id_token_hint` firma del token JWT, il nome dell'autorità emittente e il destinatario del token ed estrae l'attestazione dal token in ingresso.
- [Autorità emittente di token JWT](jwt-issuer-technical-profile.md): rilascio di un token JWT che viene restituito all'applicazione relying party.
- [OAuth1](oauth1-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md): federazione con qualsiasi provider di identità con protocollo OAuth 2.0.
- [Password una sola volta](one-time-password-technical-profile.md) : fornisce il supporto per la gestione della generazione e della verifica di una password monouso.
- [OpenID Connect](openid-connect-technical-profile.md) -Federazione con qualsiasi provider di identità del protocollo OpenID Connect.
- [Phone Factor](phone-factor-technical-profile.md) : supporto per la registrazione e la verifica dei numeri di telefono.
- [Provider RESTful](restful-technical-profile.md) : è possibile chiamare i servizi API REST, ad esempio convalidare l'input dell'utente, arricchire i dati utente o integrarsi con applicazioni line-of-business.
- [Provider di identità SAML](saml-identity-provider-technical-profile.md) : Federazione con qualsiasi provider di identità del protocollo SAML.
- [Emittente del token SAML](saml-issuer-technical-profile.md) : genera un token SAML restituito all'applicazione relying party.
- [Autocertificazione](self-asserted-technical-profile.md): interazione con l'utente, ad esempio per raccogliere le credenziali dell'utente per l'accesso, eseguire il rendering della pagina di iscrizione o reimpostare la password.
- [Gestione delle sessioni](custom-policy-reference-sso.md): gestione di diversi tipi di sessioni.

## <a name="technical-profile-flow"></a>Flusso dei profili tecnici

Tutti i tipi di profili tecnici condividono lo stesso concetto. Per iniziare, leggere le attestazioni di input, eseguire la trasformazione delle attestazioni. Comunicare quindi con l'entità configurata, ad esempio un provider di identità, un'API REST o Azure AD servizi directory. Al termine del processo, il profilo tecnico restituisce le attestazioni di output ed è possibile che venga eseguita la trasformazione delle attestazioni di output. Il diagramma seguente illustra come vengono elaborati le trasformazioni e i mapping a cui viene fatto riferimento nel profilo tecnico. Dopo l'esecuzione della trasformazione delle attestazioni, le attestazioni di output vengono archiviate immediatamente nell'elenco delle attestazioni. Indipendentemente dall'entità con cui il profilo tecnico interagisce.

![Diagramma che illustra il flusso del profilo tecnico](./media/technical-profiles/technical-profile-flow.png)

1. **Gestione delle sessioni Single Sign-on (SSO)** : Ripristina lo stato della sessione del profilo tecnico, usando la [gestione delle sessioni SSO](custom-policy-reference-sso.md).
1. **Trasformazione delle attestazioni di input** : prima dell'avvio del profilo tecnico, Azure ad B2C esegue la [trasformazione delle attestazioni](claimstransformations.md)di input.
1. **Attestazioni di input** : le attestazioni vengono prelevate dall'elenco delle attestazioni usato per il profilo tecnico.
1. **Esecuzione del profilo tecnico**: il profilo tecnico scambia le attestazioni con l'entità configurata. Ad esempio:
    - Reindirizza l'utente al provider di identità per completare l'accesso. Dopo aver completato l'accesso, l'utente torna indietro e l'esecuzione del profilo tecnico continua.
    - Chiama un'API REST inviando i parametri come InputClaims e ricevendo le informazioni restituite come OutputClaims.
    - Crea o aggiorna l'account utente.
    - Invia e verifica il messaggio di testo di MFA.
1. **Profili tecnici di convalida** : un [profilo tecnico autocertificato](self-asserted-technical-profile.md) può chiamare i [profili tecnici di convalida](validation-technical-profile.md) per convalidare i dati profilati dall'utente.
1. **Attestazioni di output** : le attestazioni vengono restituite all'elenco di attestazioni. È possibile usare queste attestazioni nel passaggio di orchestrazione successivo oppure nelle trasformazioni delle attestazioni di output.
1. **Trasformazioni di attestazioni di output** : dopo il completamento del profilo tecnico, Azure ad B2C esegue la trasformazione output delle [attestazioni](claimstransformations.md). 
1. **Gestione delle sessioni Single Sign-on (SSO)** : rende permanente i dati del profilo tecnico nella sessione, usando la [gestione delle sessioni SSO](custom-policy-reference-sso.md).

Un elemento **TechnicalProfiles** contiene un set di profili tecnici supportati dal provider di attestazione. Ogni provider di attestazioni deve disporre di almeno un profilo tecnico. Il profilo tecnico determina gli endpoint e i protocolli necessari per comunicare con il provider di attestazioni. Un provider di attestazioni può avere più profili tecnici.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L'elemento **TechnicalProfile** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
|---------|---------|---------|
| Id | Sì | Un identificatore univoco del profilo tecnico. È possibile fare riferimento al profilo tecnico usando questo identificatore dagli altri elementi nel file dei criteri. Ad esempio, **OrchestrationSteps** e **ValidationTechnicalProfile**. |

L'elemento **TechnicalProfile** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Dominio | 0:1 | Il nome di dominio per il profilo tecnico. Ad esempio, se il profilo tecnico specifica il provider di identità Facebook, il nome di dominio è Facebook.com. |
| DisplayName | 1:1 | Nome visualizzato del profilo tecnico. |
| Descrizione | 0:1 | Descrizione del profilo tecnico. |
| Protocollo | 1:1 | Il protocollo usato per la comunicazione con altre entità. |
| Metadati | 0:1 | Raccolta di chiave/valore che controlla il comportamento del profilo tecnico. |
| InputTokenFormat | 0:1 | Il formato del token di input. I valori possibili sono: `JSON`, `JWT`, `SAML11` o `SAML2`. Il valore `JWT` rappresenta un Token JSON Web in base alla specifica IETF. Il valore `SAML11` rappresenta un token di sicurezza SAML 1.1 in base alla specifica di OASIS.  Il valore `SAML2` rappresenta un token di sicurezza SAML 2.0 in base alla specifica di OASIS. |
| OutputTokenFormat | 0:1 | Il formato del token di output. I valori possibili sono: `JSON`, `JWT`, `SAML11` o `SAML2`. |
| CryptographicKeys | 0:1 | Un elenco di chiavi di crittografia usate nel profilo tecnico. |
| InputClaimsTransformations | 0:1 | Un elenco di riferimenti definiti in precedenza per le trasformazioni di attestazioni che devono essere eseguiti prima che le attestazioni siano inviate al provider di attestazioni o relying party. |
| InputClaims | 0:1 | Un elenco dei riferimenti definiti in precedenza per i tipi di attestazione eseguiti come input nel profilo tecnico. |
| PersistedClaims | 0:1 | Elenco dei riferimenti definiti in precedenza ai tipi di attestazione che verranno resi permanente dal profilo tecnico. |
| DisplayClaims | 0:1 | Elenco dei riferimenti definiti in precedenza ai tipi di attestazione presentati dal [profilo tecnico autocertificato](self-asserted-technical-profile.md). La funzionalità DisplayClaims è attualmente in fase di **Anteprima**. |
| OutputClaims | 0:1 | Un elenco dei riferimenti definiti in precedenza per i tipi di attestazione eseguiti come output nel profilo tecnico. |
| OutputClaimsTransformations | 0:1 | Un elenco di riferimenti definiti in precedenza per le trasformazioni di attestazioni che devono essere eseguiti prima che le attestazioni siano ricevute dal provider di attestazioni. |
| ValidationTechnicalProfiles | 0:n | Un elenco di riferimenti ad altri profili tecnici che il profilo tecnico usa ai fini della convalida. Per altre informazioni, vedere [profilo tecnico di convalida](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Controlla la produzione del nome del soggetto nei token in cui il nome del soggetto è specificato separatamente dalle attestazioni. Ad esempio OAuth o SAML.  |
| IncludeInSso | 0:1 |  Indica se l'utilizzo di questo profilo tecnico deve applicare il comportamento Single Sign-On (SSO) per la sessione o richiede l'interazione esplicita. Questo elemento è valido solo nei profili SelfAsserted utilizzati all'interno di un profilo tecnico di convalida. I valori possibili sono: `true` (impostazione predefinita) o `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Un identificatore del profilo tecnico da cui si desidera aggiungere tutte le attestazioni di input e output al profilo tecnico. Il profilo tecnico a cui si fa riferimento deve essere definito nello stesso file di criteri. |
| IncludeTechnicalProfile |0:1 | Un identificatore del profilo tecnico da cui si desidera aggiungere tutti i dati al profilo tecnico. |
| UseTechnicalProfileForSessionManagement | 0:1 | Un profilo tecnico diverso da usare per la gestione della sessione. |
|EnabledForUserJourneys| 0:1 |Controlla se il profilo tecnico viene eseguito in un percorso utente.  |

## <a name="protocol"></a>Protocollo

Il **protocollo** specifica il protocollo da utilizzare per la comunicazione con l'altra parte. L'elemento **Protocollo** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Nome | Sì | Nome di un protocollo valido supportato da Azure AD B2C usato come parte del profilo tecnico. Valori possibili: `OAuth1` , `OAuth2` , `SAML2` , `OpenIdConnect` , `Proprietary` o `None` . |
| Gestore | No | Quando il nome del protocollo è impostato su `Proprietary` , specificare il nome dell'assembly usato da Azure ad B2C per determinare il gestore del protocollo. |

## <a name="metadata"></a>Metadati

L'elemento **Metadata** contiene le opzioni di configurazione rilevanti per un protocollo specifico. L'elenco dei metadati supportati è documentato nella specifica del [profilo tecnico](#type-of-technical-profiles) corrispondente. Un elemento **Metadata** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Elemento | 0:n | I metadati che riguardano il profilo tecnico. Ogni tipo di profilo tecnico dispone di un diverso set di elementi di metadati. Per ulteriori informazioni, vedere la sezione tipi di profilo tecnico.  |

### <a name="item"></a>Elemento

L'elemento **Item** dell'elemento **Metadata** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Chiave | Sì | Chiave dei metadati. Per l'elenco di elementi di metadati, vedere ogni [tipo di profilo tecnico](#type-of-technical-profiles). |

Nell'esempio seguente viene illustrato l'utilizzo dei metadati relativi al [profilo tecnico OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

L'esempio seguente illustra l'uso dei metadati rilevanti per il [profilo tecnico dell'API REST](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Chiavi di crittografia

Per stabilire una relazione di trust con i servizi con cui si integra, Azure AD B2C archivia i segreti e i certificati sotto forma di [chiavi dei criteri](policy-keys-overview.md). Durante l'esecuzione del profilo tecnico, Azure AD B2C recupera le chiavi crittografiche da Azure AD B2C chiavi dei criteri. USA quindi le chiavi per stabilire una relazione di trust, crittografare o firmare un token. Queste attendibilità sono costituite da:

- Federazione con provider di identità [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)e [SAML](saml-identity-provider-technical-profile.md#cryptographic-keys)
- Proteggere la connessione con i [Servizi API REST](secure-rest-api.md)
- Firma e crittografia dei token [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) e [SAML](saml-issuer-technical-profile.md#cryptographic-keys)

L'elemento **CryptographicKeys** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Chiave | 1:n | Una chiave di crittografia usata in questo profilo tecnico. |

### <a name="key"></a>Chiave

L'elemento **Chiave** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Id | No | Un identificatore univoco di una coppia di chiavi specifica a cui fanno riferimento altri elementi nel file dei criteri. |
| StorageReferenceId | Sì | Un identificatore di un contenitore di chiavi di archiviazione a cui fanno riferimento altri elementi nel file dei criteri. |

## <a name="input-claims-transformations"></a>Trasformazioni di attestazioni di input

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi di trasformazione delle attestazioni di input utilizzati per modificare le attestazioni di input o generarne una nuova. 

Le attestazioni di output di una trasformazione delle attestazioni precedente nella raccolta delle trasformazioni delle attestazioni possono essere attestazioni di input di una trasformazione delle attestazioni di input successiva, che consente di avere una sequenza di trasformazione delle attestazioni in base all'altra

L'elemento **InputClaimsTransformations** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | L'identificatore di una trasformazione di attestazioni che deve essere eseguita prima che le attestazioni siano inviate al provider di attestazioni o relying party. Una trasformazione delle attestazioni può essere usata per modificare le attestazioni ClaimsSchema esistenti o generarne di nuove. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

L'elemento **InputClaimsTransformation** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Un identificatore di trasformazione delle attestazioni già definito nel file dei criteri o nel file dei criteri padre. |

I seguenti profili tecnici fanno riferimento alla trasformazione delle attestazioni **CreateOtherMailsFromEmail** . La trasformazione delle attestazioni aggiunge il valore dell' `email` attestazione alla `otherMails` raccolta, prima di salvare in modo permanente i dati nella directory.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Attestazioni di input

Il **InputClaims** preleva le attestazioni dal contenitore delle attestazioni e viene usato per il profilo tecnico. Un [profilo tecnico autocertificato](self-asserted-technical-profile.md), ad esempio, usa le attestazioni di input per prepopolare le attestazioni di output fornite dall'utente. Un profilo tecnico API REST usa le attestazioni di input per inviare i parametri di input all'endpoint API REST. Azure Active Directory usa l'attestazione di input come identificatore univoco per la lettura, l'aggiornamento o l'eliminazione di un account.

L'elemento **InputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Un tipo di attestazione di input previsto. |

### <a name="inputclaim"></a>InputClaim

L'elemento **InputClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sì | Identificatore di un tipo di attestazione. L'attestazione è già definita nella sezione schema delle attestazioni nel file di criteri o nel file dei criteri padre. |
| DefaultValue | No | Un valore predefinito da usare per creare un'attestazione se l'attestazione indicata da ClaimTypeReferenceId non esiste, in modo che l'attestazione che ne deriva possa essere usata come InputClaim dal profilo tecnico. |
| PartnerClaimType | No | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se l'attributo PartnerClaimType non è specificato, viene eseguito il mapping del tipo di attestazione di criteri specificato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

## <a name="display-claims"></a>Visualizza attestazioni

L'elemento **DisplayClaims** contiene un elenco di attestazioni da presentare sullo schermo per raccogliere i dati dall'utente. Nella raccolta di attestazioni di visualizzazione è possibile includere un riferimento a un [tipo di attestazione](claimsschema.md)o un [DisplayControl](display-controls.md) creato. 

- Un tipo di attestazione è un riferimento a un'attestazione da visualizzare sullo schermo. 
  - Per forzare l'utente a fornire un valore per un'attestazione specifica, impostare l'attributo **obbligatorio** dell'elemento **DisplayClaim** su `true` .
  - Per prepopolare i valori delle attestazioni di visualizzazione, usare le attestazioni di input descritte in precedenza. L'elemento può contenere anche un valore predefinito.
  - L'elemento **ClaimType** nella raccolta **DisplayClaims** deve impostare l'elemento **tipo** su qualsiasi tipo di input utente supportato da Azure ad B2C. Ad esempio, `TextBox` o `DropdownSingleSelect`.
- Un controllo display è un elemento dell'interfaccia utente che dispone di funzionalità speciali e interagisce con il servizio back-end Azure AD B2C. Consente all'utente di eseguire azioni nella pagina che richiama un profilo tecnico di convalida nel back-end. Ad esempio, verificando un indirizzo di posta elettronica, un numero di telefono o un numero di fedeltà del cliente.

L'ordine degli elementi in **DisplayClaims** specifica l'ordine in cui Azure ad B2C esegue il rendering delle attestazioni sullo schermo. 

L'elemento **DisplayClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Un tipo di attestazione di input previsto. |

### <a name="displayclaim"></a>DisplayClaim

L'elemento **DisplayClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DisplayControlReferenceId | No | Identificatore di un [controllo di visualizzazione](display-controls.md) già definito nella sezione ClaimsSchema del file di criteri o del file di criteri padre. |
| Necessario | No | Indica se l'attestazione di visualizzazione è obbligatoria. |

Nell'esempio seguente viene illustrato l'utilizzo di attestazioni e controlli di visualizzazione con in un profilo tecnico autocertificato.

![Profilo tecnico autocertificato con attestazioni di visualizzazione](./media/technical-profiles/display-claims.png)

Nel profilo tecnico seguente:

- La prima attestazione di visualizzazione fa riferimento al `emailVerificationControl` controllo display, che raccoglie e verifica l'indirizzo di posta elettronica.
- La Quinta attestazione di visualizzazione fa riferimento al `phoneVerificationControl` controllo display, che raccoglie e verifica un numero di telefono.
- Le altre attestazioni di visualizzazione sono ClaimTypes da raccogliere dall'utente.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>Attestazioni persistenti

L'elemento **PersistedClaims** contiene tutti i valori che devono essere salvati in maniera permanente da [Azure ad profilo tecnico](active-directory-technical-profile.md) con le informazioni di mapping possibili tra un tipo di attestazione già definito nella sezione [ClaimsSchema](claimsschema.md) del criterio e il nome dell'attributo Azure ad.

Il nome dell'attestazione è il nome dell' [attributo Azure ad](user-profile-attributes.md) , a meno che non sia specificato l'attributo **PartnerClaimType** , che contiene il nome dell'attributo Azure ad.

L'elemento **PersistedClaims** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Il tipo di attestazione da mantenere. |

### <a name="persistedclaim"></a>PersistedClaim

L'elemento **PersistedClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sì | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DefaultValue | No | Valore predefinito da utilizzare per creare un'attestazione se l'attestazione non esiste. |
| PartnerClaimType | No | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se l'attributo PartnerClaimType non è specificato, viene eseguito il mapping del tipo di attestazione di criteri specificato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

Nell'esempio seguente, il profilo tecnico **AAD-UserWriteUsingLogonEmail** o lo [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), che crea un nuovo account locale, Salva in modo permanente le attestazioni seguenti:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Attestazioni di output

**OutputClaims** è la raccolta di attestazioni restituite al contenitore delle attestazioni dopo il completamento del profilo tecnico. È possibile usare queste attestazioni nel passaggio di orchestrazione successivo oppure nelle trasformazioni delle attestazioni di output. L'elemento **OutputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Un tipo di attestazione di output previsto. |

### <a name="outputclaim"></a>OutputClaim

L'elemento **OutputClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sì | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DefaultValue | No | Valore predefinito da utilizzare per creare un'attestazione se l'attestazione non esiste. |
|AlwaysUseDefaultValue |No |Forzare l'uso del valore predefinito.  |
| PartnerClaimType | No | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se non si specifica l'attributo del tipo di attestazione partner, il tipo di attestazione dei criteri specificato viene mappato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

## <a name="output-claims-transformations"></a>Trasformazioni delle attestazioni di output

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** . Le trasformazioni delle attestazioni di output vengono usate per modificare le attestazioni di output o generarne di nuove. Al termine dell'esecuzione, le attestazioni di output vengono inserite nel contenitore delle attestazioni. È possibile usare tali attestazioni nel passaggio orchestrazioni successive.

Le attestazioni di output di una trasformazione delle attestazioni precedente nella raccolta delle trasformazioni delle attestazioni possono essere attestazioni di input di una trasformazione delle attestazioni di input successiva, che consente di disporre di una sequenza di trasformazione delle attestazioni in base all'altra

L'elemento **OutputClaimsTransformations** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Gli identificatori delle trasformazione di attestazioni che devono essere eseguiti prima che le attestazioni siano inviate al provider di attestazioni o relying party. Una trasformazione delle attestazioni può essere usata per modificare le attestazioni ClaimsSchema esistenti o generarne di nuove. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

L'elemento **OutputClaimsTransformation** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Un identificatore di trasformazione delle attestazioni già definito nel file dei criteri o nel file dei criteri padre. |

Il profilo tecnico seguente fa riferimento alla trasformazione delle attestazioni AssertAccountEnabledIsTrue per valutare se l'account è abilitato o meno dopo aver letto l' `accountEnabled` attestazione dalla directory.    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Profili tecnici di convalida

Un profilo tecnico di convalida viene usato per convalidare le attestazioni di output in un [profilo tecnico autocertificato](self-asserted-technical-profile.md#validation-technical-profiles). Un profilo tecnico di convalida è un profilo tecnico standard di un qualsiasi protocollo, ad esempio [Azure Active Directory](active-directory-technical-profile.md) o un'[API REST](restful-technical-profile.md). Il profilo tecnico di convalida restituisce le attestazioni di output oppure restituisce il codice di errore. Viene eseguito il rendering del messaggio di errore all'utente sullo schermo, consentendo all'utente di riprovare.

Il diagramma seguente illustra il modo in cui Azure AD B2C usa un profilo tecnico di convalida per convalidare le credenziali utente

![Flusso del profilo tecnico di convalida diagramma](./media/technical-profiles/validation-technical-profile.png) 

L'elemento **ValidationTechnicalProfiles** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Gli identificatori dei profili tecnici che consentono di convalidare alcune o tutte le attestazioni di output del profilo tecnico di riferimento. Tutte le attestazioni di input del profilo tecnico a cui si fa riferimento devono essere visualizzate nelle attestazioni di output del profilo tecnico di riferimento. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

L'elemento **ValidationTechnicalProfile** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Un identificatore del profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** definisce il nome del soggetto utilizzato nei token in un [criterio di relying party](relyingparty.md#subjectnaminginfo). Il **SubjectNamingInfo** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimType | Sì | Un identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri. |

## <a name="include-technical-profile"></a>Includi profilo tecnico

Un profilo tecnico può includere un altro profilo tecnico per modificare le impostazioni o aggiungere nuove funzionalità. L'elemento **IncludeTechnicalProfile** è un riferimento al profilo tecnico comune da cui deriva un profilo tecnico. Per ridurre la ridondanza e la complessità degli elementi dei criteri, usare l'inclusione quando si hanno più profili tecnici che condividono gli elementi di base. Usare un profilo tecnico comune con il set di configurazione comune, insieme a profili tecnici specifici per le attività che includono il profilo tecnico comune. 

Si supponga di avere un [profilo tecnico dell'API REST](restful-technical-profile.md) con un singolo endpoint in cui è necessario inviare un set di attestazioni diverso per diversi scenari. Creare un profilo tecnico comune con le funzionalità condivise, ad esempio l'URI dell'endpoint dell'API REST, i metadati, il tipo di autenticazione e le chiavi crittografiche. Creare profili tecnici specifici per le attività che includono il profilo tecnico comune. Aggiungere quindi le attestazioni di input, le attestazioni di output o sovrascrivere l'URI dell'endpoint dell'API REST pertinente per quel profilo tecnico.

L'elemento **IncludeTechnicalProfile** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Identificatore di un profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |


Nell'esempio seguente viene illustrato l'utilizzo dell'inclusione:

- *REST-API-Common* -un profilo tecnico comune con la configurazione di base.
- *Rest-ValidateProfile* : include il profilo tecnico *comune REST-API* e specifica le attestazioni di input e output.
- *Rest-updateprofile in* : include il profilo tecnico *comune REST-API* , specifica le attestazioni di input e sovrascrive i `ServiceUrl` metadati.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>Inclusione a più livelli 

Un profilo tecnico può includere un solo profilo tecnico. Non esiste alcun limite per il numero di livelli di inclusione. Il profilo tecnico **AAD-UserReadUsingAlternativeSecurityId-NoError**, ad esempio, include **AAD-UserReadUsingAlternativeSecurityId**. Questo profilo tecnico imposta l' `RaiseErrorIfClaimsPrincipalDoesNotExist` elemento dei metadati su `true` e genera un errore se un account di social networking non esiste nella directory. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** esegue l'override di questo comportamento e Disabilita il messaggio di errore.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** include il profilo tecnico `AAD-Common`.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NOERROR** e **AAD-UserReadUsingAlternativeSecurityId** non specificano l'elemento del **protocollo** richiesto perché è specificato nel profilo tecnico **comune di AAD** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Usa profilo tecnico per la gestione delle sessioni

Riferimento all'elemento **UseTechnicalProfileForSessionManagement** al [profilo tecnico della sessione Single Sign-on](custom-policy-reference-sso.md). L'elemento **UseTechnicalProfileForSessionManagement** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Un identificatore del profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |

## <a name="enabled-for-user-journeys"></a>Abilitato per i percorsi utente

L'elemento [ClaimsProviderSelections](userjourneys.md#claimsproviderselection) in un percorso utente definisce l'elenco di selezioni di provider di attestazioni e il relativo ordine. Con l'elemento **EnabledForUserJourneys** è possibile filtrare i provider di attestazioni disponibili per l'utente. L'elemento **EnabledForUserJourneys** contiene uno dei valori seguenti:

- **Sempre**, eseguire il profilo tecnico.
- **Mai**, ignorare il profilo tecnico.
- **OnClaimsExistence**, eseguire solo quando esiste una determinata attestazione, specificata nel profilo tecnico.
- **OnItemExistenceInStringCollectionClaim**, eseguire solo quando un elemento esiste in un'attestazione di raccolta di stringhe.
- **OnItemExistenceInStringCollectionClaim**, eseguire solo quando un elemento non esiste in un'attestazione di raccolta di stringhe.

Per usare **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** o **OnItemAbsenceInStringCollectionClaim**, è necessario fornire i metadati seguenti: 

- **ClaimTypeOnWhichToEnable** : specifica il tipo di attestazione da valutare.
- **ClaimValueOnWhichToEnable** -specifica il valore da confrontare.

Il seguente profilo tecnico viene eseguito solo se la raccolta di stringhe **identityProviders** contiene il valore di `facebook.com`:

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
