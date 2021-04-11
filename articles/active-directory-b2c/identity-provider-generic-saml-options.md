---
title: Impostare l'accesso con le opzioni del provider di identità SAML
titleSuffix: Azure Active Directory B2C
description: Configurare le opzioni del provider di identità SAML (IdP) di accesso in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 32f9df410dabf1902e9a7d9aadbf47288bfa90f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798239"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Configurare le opzioni del provider di identità SAML con Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) supporta la Federazione con i provider di identità SAML 2,0. Questo articolo descrive le opzioni di configurazione disponibili quando si Abilita l'accesso con un provider di identità SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mapping delle attestazioni

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità SAML. È necessario mappare il nome dell'attestazione definita nei criteri al nome definito nel provider di identità. Controllare il provider di identità per l'elenco di attestazioni (asserzioni). È anche possibile controllare il contenuto della risposta SAML restituita dal provider di identità. Per altre informazioni, vedere [eseguire il debug dei messaggi SAML](#debug-saml-protocol). Per aggiungere un'attestazione, [definire innanzitutto un'attestazione](claimsschema.md), quindi aggiungere l'attestazione alla raccolta di attestazioni di output.

È anche possibile includere le attestazioni che non vengono restituite dal provider di identità, fino a quando è impostato l'attributo `DefaultValue`. Il valore predefinito può essere statico o dinamico, usando le [attestazioni di contesto](#enable-use-of-context-claims).

L'elemento Claim di output contiene gli attributi seguenti:

- **ClaimTypeReferenceId** è il riferimento a un tipo di attestazione. 
- **PartnerClaimType** è il nome della proprietà che viene visualizzata asserzione SAML. 
- **DefaultValue** è un valore predefinito predefinito. Se l'attestazione è vuota, verrà utilizzato il valore predefinito. È anche possibile usare un [resolver di attestazioni](claim-resolver-overview.md) con un valore contestuale, ad esempio l'ID di correlazione, o l'indirizzo IP dell'utente.

### <a name="subject-name"></a>Nome oggetto

Per leggere l'asserzione SAML **NameID** nell' **oggetto** come attestazione normalizzata, impostare l'attestazione **PartnerClaimType** sul valore dell' `SPNameQualifier` attributo. Se l' `SPNameQualifier` attributo non viene presentato, impostare l'attestazione **PartnerClaimType** sul valore dell' `NameQualifier` attributo.

Asserzione SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Attestazione di output:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Se gli `SPNameQualifier` `NameQualifier` attributi o non vengono presentati nell'asserzione SAML, impostare l'attestazione **PartnerClaimType** su `assertionSubjectName` . Assicurarsi che **NameId** sia il primo valore nell'asserzione XML. Quando si definisce più di un'asserzione, Azure Active Directory B2C sceglie il valore del soggetto dall'ultima asserzione.


## <a name="configure-saml-protocol-bindings"></a>Configurare associazioni di protocollo SAML

Le richieste SAML vengono inviate al provider di identità come specificato nell'elemento dei metadati del provider di identità `SingleSignOnService` . La maggior parte delle richieste di autorizzazione dei provider di identità viene eseguita direttamente nella stringa di query dell'URL di una richiesta HTTP GET (poiché i messaggi sono relativamente brevi). Per informazioni su come configurare i binding per entrambe le richieste SAML, vedere la documentazione del provider di identità.

Di seguito è riportato un esempio di un Azure AD di metadati Single Sign-On servizio con due associazioni. `HTTP-Redirect`Ha la precedenza su `HTTP-POST` perché viene visualizzato per primo nei metadati del provider di identità SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Servizio consumer di asserzione

Il servizio consumer di asserzione (o ACS) è il punto in cui le risposte SAML del provider di identità possono essere inviate e ricevute da Azure AD B2C. Le risposte SAML vengono trasmesse a Azure AD B2C tramite il binding HTTP POST. Il percorso ACS punta ai criteri di base del relying party. Se, ad esempio, i criteri di relying sono *B2C_1A_signup_signin*, ACS è il criterio di base del *B2C_1A_signup_signin*, ad esempio *B2C_1A_TrustFrameworkBase*.

Di seguito è riportato un esempio di un elemento del servizio consumer di asserzione dei metadati dei criteri Azure AD B2C. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Configurare la firma della richiesta SAML

Azure AD B2C firma tutte le richieste di autenticazione in uscita usando la chiave di crittografia **SamlMessageSigning** . Per disabilitare la firma della richiesta SAML, impostare **WantsSignedRequests** su `false` . Se i metadati sono impostati su `false` , i parametri **SigAlg** e **Signature** (stringa di query o parametro post) vengono omessi dalla richiesta.

Questi metadati controllano anche l'attributo **AuthnRequestsSigned** , incluso nei metadati del profilo tecnico Azure ad B2C condiviso con il provider di identità. Azure AD B2C non firma la richiesta se il valore di **WantsSignedRequests** nei metadati del profilo tecnico è impostato su `false` e i metadati del provider di identità **WantAuthnRequestsSigned** sono impostati su `false` o non specificati.

Nell'esempio seguente viene rimossa la firma dalla richiesta SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algoritmo di firma

Azure AD B2C USA `Sha1` per firmare la richiesta SAML. Usare i metadati **XmlSignatureAlgorithm** per configurare l'algoritmo da usare. I valori possibili sono `Sha256` ,, `Sha384` `Sha512` o `Sha1` (impostazione predefinita). Questi metadati controllano il valore del parametro **SigAlg** (stringa di query o parametro Post) nella richiesta SAML. Verificare di configurare l'algoritmo di firma per entrambe le parti con lo stesso valore. Usare solo l'algoritmo supportato dal certificato.

### <a name="include-key-info"></a>Includi informazioni chiave

Quando il provider di identità indica che Azure AD B2C binding è impostato su `HTTP-POST` , Azure ad B2C include la firma e l'algoritmo nel corpo della richiesta SAML. È inoltre possibile configurare Azure AD per includere la chiave pubblica del certificato quando l'associazione è impostata su `HTTP-POST` . Usare i metadati **IncludeKeyInfo** in `true` o `false` . Nell'esempio seguente Azure AD non includerà la chiave pubblica del certificato.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Configurare l'ID del nome della richiesta SAML

L'elemento di richiesta di autorizzazione SAML `<NameID>` indica il formato dell'identificatore del nome SAML. In questa sezione viene descritta la configurazione predefinita e viene illustrato come personalizzare l'elemento ID nome.

## <a name="preferred-username"></a>Nome utente preferito

Durante un percorso utente di accesso, un'applicazione relying party può essere destinata a un utente specifico. Azure AD B2C consente di inviare un nome utente preferito al provider di identità SAML. L'elemento **InputClaims** viene usato per inviare un **NameID** all'interno dell' **oggetto** della richiesta di autorizzazione SAML.

Per includere l'ID del nome del soggetto nella richiesta di autorizzazione, aggiungere l' `<InputClaims>` elemento seguente subito dopo `<CryptographicKeys>` . **PartnerClaimType** deve essere impostato su `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

In questo esempio Azure AD B2C invia il valore dell'attestazione **signInName** con **NameID** all'interno dell' **oggetto** della richiesta di autorizzazione SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

È possibile utilizzare le [attestazioni del contesto](claim-resolver-overview.md), ad esempio `{OIDC:LoginHint}` per popolare il valore dell'attestazione.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Formato criteri ID nome

Per impostazione predefinita, la richiesta di autorizzazione SAML specifica i `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` criteri. Ciò indica che è possibile utilizzare qualsiasi tipo di identificatore supportato dal provider di identità per l'oggetto richiesto.

Per modificare questo comportamento, fare riferimento alla documentazione del provider di identità per informazioni sui criteri di ID nome supportati. Quindi aggiungere i `NameIdPolicyFormat` metadati nel formato dei criteri corrispondente. Ad esempio:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

La richiesta di autorizzazione SAML seguente contiene i criteri ID nome.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Consenti la creazione di nuovi account

Se si specifica il [formato dei criteri ID nome](#name-id-policy-format), è anche possibile specificare la `AllowCreate` proprietà di **NameIDPolicy** per indicare se il provider di identità è autorizzato a creare un nuovo account durante il flusso di accesso. Per informazioni aggiuntive, vedere la documentazione del provider di identità.

`AllowCreate`Per impostazione predefinita, Azure ad B2C omette la proprietà. È possibile modificare questo comportamento usando i `NameIdPolicyAllowCreate` metadati. Il valore di questi metadati è `true` o `false` .

Nell'esempio seguente viene illustrato come impostare la `AllowCreate` proprietà di `NameIDPolicy` su `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Nell'esempio seguente viene illustrata una richiesta di autorizzazione con **AllowCreate** dell'elemento **NameIDPolicy** nella richiesta di autorizzazione.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Includi riferimenti alla classe del contesto di autenticazione

Una richiesta di autorizzazione SAML può contenere un elemento **AuthnContext** , che specifica il contesto di una richiesta di autorizzazione. L'elemento può contenere un riferimento alla classe del contesto di autenticazione, che indica al provider di identità SAML il meccanismo di autenticazione da presentare all'utente.

Per configurare i riferimenti della classe del contesto di autenticazione, aggiungere i metadati **IncludeAuthnContextClassReferences** . Nel valore specificare uno o più riferimenti URI che identificano le classi del contesto di autenticazione. Specificare più URI come elenco delimitato da virgole. Per istruzioni sugli URI **AuthnContextClassRef** supportati, vedere la documentazione del provider di identità.

L'esempio seguente consente agli utenti di accedere con nome utente e password e di accedere con nome utente e password in una sessione protetta (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

La richiesta di autorizzazione SAML seguente contiene i riferimenti della classe del contesto di autenticazione.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Includere dati personalizzati nella richiesta di autorizzazione

Facoltativamente, è possibile includere gli elementi di estensione del messaggio del protocollo concordati da Azure AD BC e dal provider di identità. L'estensione viene presentata in formato XML. Per includere elementi di estensione, è possibile aggiungere dati XML all'interno dell'elemento CDATA `<![CDATA[Your IDP metadata]]>` . Controllare la documentazione del provider di identità per vedere se l'elemento di estensione è supportato.

Nell'esempio seguente viene illustrato l'utilizzo dei dati di estensione:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Quando si usa l'estensione del messaggio del protocollo SAML, la risposta SAML sarà simile all'esempio seguente:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Richiedi risposte SAML firmate

Azure AD B2C richiede la firma di tutte le asserzioni in ingresso. È possibile rimuovere questo requisito impostando **WantsSignedAssertions** su `false` . Il provider di identità non deve firmare le asserzioni in questo caso, ma anche in tal caso, Azure AD B2C non convaliderà la firma.

I metadati **WantsSignedAssertions** controllano il flag di metadati SAML **WantAssertionsSigned**, incluso nei metadati del profilo tecnico Azure ad B2C condiviso con il provider di identità.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Se si disattiva la convalida delle asserzioni, potrebbe essere necessario disabilitare anche la convalida della firma del messaggio di risposta. Impostare i metadati **ResponsesSigned** su `false` . Il provider di identità non deve firmare il messaggio di risposta SAML in questo caso, ma anche in caso contrario, Azure AD B2C non convaliderà la firma.

Nell'esempio seguente vengono rimossi sia il messaggio che la firma dell'asserzione:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Richiedi risposte SAML crittografate

Per richiedere la crittografia di tutte le asserzioni in ingresso, impostare i metadati **WantsEncryptedAssertions** . Quando è richiesta la crittografia, il provider di identità utilizza una chiave pubblica di un certificato di crittografia in un Azure AD B2C profilo tecnico. Azure AD B2C Decrittografa l'asserzione della risposta usando la parte privata del certificato di crittografia.

Se si Abilita la crittografia delle asserzioni, potrebbe anche essere necessario disabilitare la convalida della firma della risposta (per altre informazioni, vedere [richiedere risposte SAML firmate](#require-signed-saml-responses).

Quando i metadati di **WantsEncryptedAssertions** sono impostati su `true` , i metadati del profilo tecnico Azure ad B2C includono la sezione relativa alla **crittografia** . Il provider di identità legge i metadati e crittografa l'asserzione di risposta SAML con la chiave pubblica contenuta nei metadati del profilo tecnico di Azure AD B2C.

L'esempio seguente illustra la sezione del descrittore chiave dei metadati SAML usati per la crittografia:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Per crittografare l'asserzione di risposta SAML:

1. [Creare una chiave dei criteri](identity-provider-generic-saml.md#create-a-policy-key) con un identificatore univoco. Ad esempio: `B2C_1A_SAMLEncryptionCert`.
2. Nella raccolta **CryptographicKeys** del profilo tecnico SAML. Impostare **ID riferimento archiviazione** sul nome della chiave dei criteri creata nel primo passaggio. L' `SamlAssertionDecryption` ID indica l'uso della chiave crittografica per crittografare e decrittografare l'asserzione della risposta SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Impostare i metadati del profilo tecnico **WantsEncryptedAssertions** su `true`.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Aggiornare il provider di identità con i nuovi metadati del profilo tecnico Azure AD B2C. Viene visualizzato l'elemento **KeyDescriptor** con la proprietà **use** impostata su `encryption` contenente la chiave pubblica del certificato.

## <a name="enable-use-of-context-claims"></a>Abilita l'uso di attestazioni di contesto

Nella raccolta di attestazioni di input e output è possibile includere le attestazioni che non vengono restituite dal provider di identità finché si imposta l' `DefaultValue` attributo. È inoltre possibile utilizzare le [attestazioni del contesto](claim-resolver-overview.md) da includere nel profilo tecnico. Per usare un'attestazione di contesto:

1. Aggiungere un tipo di attestazione all'elemento [ClaimsSchema](claimsschema.md) all'interno di [BuildingBlocks](buildingblocks.md).
2. Aggiungere un'attestazione di output alla raccolta di input o output. Nell'esempio seguente, la prima attestazione imposta il valore del provider di identità. La seconda attestazione utilizza le [attestazioni del contesto](claim-resolver-overview.md)dell'indirizzo IP dell'utente.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Disabilitare la disconnessione singola

Al termine di una richiesta di disconnessione dell'applicazione, Azure AD B2C tenta di disconnettersi dal provider di identità SAML. Per ulteriori informazioni, vedere [Azure ad B2C la disconnessione della sessione](session-behavior.md#sign-out).  Per disabilitare il comportamento di disconnessione singolo, impostare i metadati **SingleLogoutEnabled** su `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Debug del protocollo SAML

Per semplificare la configurazione e il debug della Federazione con un provider di identità SAML, è possibile usare un'estensione del browser per il protocollo SAML, ad esempio [SAML devtools Extension](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) for Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox o [Edge o IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Usando questi strumenti, è possibile controllare l'integrazione tra Azure AD B2C e il provider di identità SAML. Ad esempio:

* Controllare se la richiesta SAML contiene una firma e determinare quale algoritmo viene usato per firmare la richiesta di autorizzazione.
* Ottenere le attestazioni (asserzioni) nella `AttributeStatement` sezione.
* Controllare se il provider di identità restituisce un messaggio di errore.
* Controllare se la sezione dell'asserzione è crittografata.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come diagnosticare i problemi relativi ai criteri personalizzati, usando [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
