---
title: Configurare le opzioni del provider di servizi SAML
title-suffix: Azure Active Directory B2C
description: Come configurare Azure Active Directory B2C opzioni del provider di servizi SAML
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 09cfdd026105a34db976118f38b011e2c4578a24
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470782"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opzioni per la registrazione di un'applicazione SAML in Azure AD B2C

Questo articolo descrive le opzioni di configurazione disponibili quando si connettono Azure Active Directory (Azure AD B2C) con l'applicazione SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Asserzioni SAML crittografate

Quando l'applicazione prevede che le asserzioni SAML siano in formato crittografato, è necessario assicurarsi che la crittografia sia abilitata nei criteri di Azure AD B2C.

Azure AD B2C usa il certificato di chiave pubblica del provider di servizi per crittografare l'asserzione SAML. La chiave pubblica deve esistere nell'endpoint dei metadati dell'applicazione SAML con il descrittore ' use ' impostato su' Encryption ', come illustrato nell'esempio seguente:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Per consentire Azure AD B2C di inviare asserzioni crittografate, impostare l'elemento dei metadati **WantsEncryptedAssertion** su `true` nel [profilo tecnico relying party](relyingparty.md#technicalprofile). È anche possibile configurare l'algoritmo usato per crittografare l'asserzione SAML.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>Metodo di crittografia

Per configurare il metodo di crittografia utilizzato per crittografare i dati dell'asserzione SAML, impostare la `DataEncryptionMethod` chiave dei metadati all'interno del relying party. I valori possibili sono `Aes256` (impostazione predefinita),, `Aes192` `Sha512` o `Aes128` . I metadati controllano il valore dell' `<EncryptedData>` elemento nella risposta SAML.

Per configurare il metodo di crittografia utilizzato per crittografare la copia della chiave utilizzata per crittografare i dati dell'asserzione SAML, impostare la `KeyEncryptionMethod` chiave dei metadati all'interno del relying party. I valori possibili sono `Rsa15` (impostazione predefinita): algoritmo RSA Public Key Cryptography Standard (PKCS) versione 1,5 e `RsaOaep` algoritmo di crittografia OAEP (Optimal asimmetrica Encryption Padding).  I metadati controllano il valore dell'  `<EncryptedKey>` elemento nella risposta SAML.

Nell'esempio seguente viene illustrata la `EncryptedAssertion` sezione di un'asserzione SAML. Il metodo dati crittografati è `Aes128` e il metodo della chiave crittografata è `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

È possibile modificare il formato delle asserzioni crittografate. Per configurare il formato di crittografia, impostare la `UseDetachedKeys` chiave dei metadati all'interno del relying party. Valori possibili: `true` o `false` (impostazione predefinita). Quando il valore è impostato su `true` , le chiavi scollegate aggiungono l'asserzione crittografata come elemento figlio di anziché `EncrytedAssertion` `EncryptedData` .

Configurare il metodo e il formato di crittografia, usare le chiavi dei metadati all'interno del [relying party profilo tecnico](relyingparty.md#technicalprofile):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Flusso avviato dal provider di identità

Quando l'applicazione prevede di ricevere un'asserzione SAML senza prima inviare una richiesta AuthN SAML al provider di identità, è necessario configurare Azure AD B2C per il flusso avviato dal provider di identità.

Nel flusso avviato dal provider di identità, il processo di accesso viene avviato dal provider di identità (Azure AD B2C), che invia una risposta SAML non richiesta al provider di servizi (l'applicazione relying party).

Attualmente non sono supportati scenari in cui il provider di identità iniziale è un provider di identità esterno federato con Azure AD B2C, ad esempio [ad FS](identity-provider-adfs.md)o [Salesforce](identity-provider-salesforce-saml.md). È supportato solo per l'autenticazione Azure AD B2C account locale.

Per abilitare il flusso avviato dal provider di identità, impostare l'elemento dei metadati **IdpInitiatedProfileEnabled** su `true` nel [profilo tecnico relying party](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Per eseguire l'accesso o l'iscrizione di un utente tramite un flusso avviato dal provider di identità, usare l'URL seguente:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Sostituire i valori seguenti:

* **nome-tenant** con il nome del tenant
* **Policy-Name** con il nome del criterio di relying party SAML
* **app-Identifier-URI** con l'oggetto `identifierUris` nel file di metadati, ad esempio `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Criterio di esempio

Sono disponibili criteri di esempio completi che è possibile usare per il test con l'app di test SAML.

1. Scaricare i [criteri di esempio per l'accesso avviato da SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Aggiornare `TenantId` in modo che corrisponda al nome del tenant, ad esempio *contoso.b2clogin.com*.
1. Mantieni il nome dei criteri *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algoritmo di firma della risposta SAML

È possibile configurare l'algoritmo di firma usato per firmare l'asserzione SAML. I possibili valori sono `Sha256`, `Sha384`, `Sha512` o `Sha1`. Verificare che il profilo tecnico e l'applicazione usino lo stesso algoritmo di firma. Usare solo l'algoritmo supportato dal certificato.

Configurare l'algoritmo di firma utilizzando la `XmlSignatureAlgorithm` chiave dei metadati all'interno dell'elemento dei metadati relying party.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Durata risposta SAML

È possibile configurare il periodo di tempo per cui la risposta SAML rimane valida. Impostare la durata usando l' `TokenLifeTimeInSeconds` elemento dei metadati all'interno del profilo tecnico dell'emittente del token SAML. Questo valore indica il numero di secondi che possono trascorrere dal `NotBefore` timestamp calcolato al momento del rilascio del token. La durata predefinita è 300 secondi (5 minuti).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>Risposta SAML valida da asimmetria

È possibile configurare lo sfasamento dell'ora applicato al timestamp della risposta SAML `NotBefore` . Questa configurazione garantisce che se gli orari tra due piattaforme non sono sincronizzati, l'asserzione SAML sarà comunque considerata valida quando entro questo sfasamento dell'ora.

Impostare lo sfasamento dell'ora utilizzando l' `TokenNotBeforeSkewInSeconds` elemento dei metadati all'interno del profilo tecnico dell'emittente del token SAML. Il valore di inclinazione viene espresso in secondi e il valore predefinito è 0. Il valore massimo è 3600 (un'ora).

Ad esempio, quando l'oggetto `TokenNotBeforeSkewInSeconds` è impostato su `120` secondi:

- Il token viene emesso alle 13:05:10 UTC
- Il token è valido da 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Rimuovi millisecondi da data e ora

È possibile specificare se i millisecondi verranno rimossi dai valori DateTime entro la risposta SAML, ad esempio IssueInstant, NotBefore, NotOnOrAfter e AuthnInstant. Per rimuovere i millisecondi, impostare la `RemoveMillisecondsFromDateTime
` chiave dei metadati all'interno del relying party. Valori possibili: `false` (impostazione predefinita) o `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>ID autorità emittente Azure AD B2C

Se si dispone di più applicazioni SAML che dipendono da `entityID` valori diversi, è possibile eseguire l'override del `issueruri` valore nel file di relying party. Per eseguire l'override dell'URI dell'autorità emittente, copiare il profilo tecnico con l'ID "Saml2AssertionIssuer" dal file di base ed eseguire l'override del `issueruri` valore.

> [!TIP]
> Copiare la `<ClaimsProviders>` sezione dalla base e conservarne gli elementi all'interno del provider di attestazioni: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` e `<DisplayName>Token Issuer</DisplayName>` .
 
Esempio:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Gestione delle sessioni

È possibile gestire la sessione tra Azure AD B2C e l'applicazione SAML relying party usando l' `UseTechnicalProfileForSessionManagement` elemento e [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="force-users-to-re-authenticate"></a>Forza la ripetizione dell'autenticazione degli utenti 

Per forzare la ripetizione dell'autenticazione degli utenti, l'applicazione può includere l' `ForceAuthn` attributo nella richiesta di autenticazione SAML. L' `ForceAuthn` attributo è un valore booleano. Quando è impostato su true, la sessione utenti verrà invalidata in Azure AD B2C e l'utente dovrà eseguire di nuovo l'autenticazione. La richiesta di autenticazione SAML seguente illustra come impostare l' `ForceAuthn` attributo su true. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="debug-the-saml-protocol"></a>Eseguire il debug del protocollo SAML

Per semplificare la configurazione e il debug dell'integrazione con il provider di servizi, è possibile usare un'estensione del browser per il protocollo SAML, ad esempio [SAML devtools Extension](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) for Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox o [Edge o IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Usando questi strumenti, è possibile controllare l'integrazione tra l'applicazione e Azure AD B2C. Ad esempio:

* Controllare se la richiesta SAML contiene una firma e determinare quale algoritmo viene usato per firmare la richiesta di autorizzazione.
* Controllare se Azure AD B2C restituisce un messaggio di errore.
* Verificare che la sezione asserzione sia crittografata.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul protocollo SAML sono disponibili nel [sito Web Oasis](https://www.oasis-open.org/).
- Ottenere l'app Web di test SAML dal [repository della community Azure ad B2C GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
