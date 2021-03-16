---
title: Aggiungere AD FS come provider di identità SAML usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Configurare AD FS 2016 usando il protocollo SAML e i criteri personalizzati in Azure Active Directory B2C
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
ms.openlocfilehash: 292a244a4804f97e8622d6841c33b153af373290
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489169"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Aggiungere AD FS come provider di identità SAML usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per un account utente AD FS usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure ad B2C). Per abilitare l'accesso, è necessario aggiungere un [provider di identità SAML](identity-provider-generic-saml.md) a un criterio personalizzato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il certificato nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Upload`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SAMLSigningCert`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. Individuare e selezionare il file di certificato con estensione pfx con la chiave privata.
9. Fare clic su **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Se si desidera che gli utenti possano accedere utilizzando un account di AD FS, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C può comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account AD FS come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri. Per altre informazioni, vedere [definire un provider di identità SAML](identity-provider-generic-saml.md).

1. Aprire *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Sostituire `your-AD-FS-domain` con il nome del dominio ad FS e sostituire il valore dell'attestazione di output **IdentityProvider** con il DNS (valore arbitrario che indica il dominio).

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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Configurare un AD FS relying party trust

Per usare AD FS come provider di identità in Azure AD B2C, è necessario creare un AD FS attendibilità del componente con i metadati SAML Azure AD B2C. L'esempio seguente mostra un indirizzo URL che punta ai metadati SAML di un profilo tecnico di Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Quando si usa un [dominio personalizzato](custom-domain.md), usare il formato seguente:

```
https://your-domain-name/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Sostituire i valori seguenti:

- **nome-tenant** con il nome del tenant, ad esempio your-tenant.onmicrosoft.com.
- **nome di dominio** con il nome di dominio personalizzato, ad esempio login.contoso.com.
- **your-policy** con il nome dei criteri. Ad esempio, B2C_1A_signup_signin_adfs.
- **profilo tecnico** con il nome del profilo tecnico del provider di identità SAML. Ad esempio, Contoso-SAML2.

Aprire un browser e passare all'URL. Assicurarsi di digitare l'URL corretto e di avere accesso al file dei metadati XML. Per aggiungere un nuovo trust della relying party usando lo snap-in di gestione di AD FS e configurare manualmente le impostazioni, eseguire la procedura seguente in un server federativo. Per completare questa procedura, è necessaria almeno l'appartenenza al gruppo **Administrators** o a un gruppo equivalente nel computer locale.

1. In Server Manager selezionare **strumenti** e quindi selezionare **gestione ad FS**.
2. Scegliere **Aggiungi attendibilità componente**.
3. Nella pagina **iniziale** scegliere **In grado di riconoscere attestazioni** e quindi fare clic su **Avvia**.
4. Nella pagina **Seleziona origine dati** selezionare **Import data about the relying party publish online or on a local network** (Importa dati sul relying party pubblicata online o in una rete locale), specificare l'URL dei metadati di Azure AD B2C e quindi fare clic su **Avanti**.
5. Nella pagina **Specifica nome visualizzato** digitare un nome in **Nome visualizzato**, in **Note** digitare una descrizione del trust della relying party e quindi fare clic su **Avanti**.
6. Nella pagina **Scegli criteri di controllo di accesso** selezionare i criteri desiderati e fare clic su **Avanti**.
7. Nella pagina **Aggiunta attendibilità** esaminare le impostazioni e quindi fare clic su **Avanti** per salvare le informazioni sul trust della relying party.
8. Nella pagina **Fine** fare clic su **Chiudi**. Viene automaticamente visualizzata la finestra di dialogo **Modifica regole attestazione**.
9. Selezionare **Aggiungi regola**.
10. In **Modello di regola attestazione** selezionare **Inviare attributi LDAP come attestazioni**.
11. Specificare un nome in **Nome regola attestazione**. Per **archivio attributi** selezionare **Seleziona Active Directory**, aggiungere le attestazioni seguenti, quindi fare clic su **fine** e su **OK**.

    | Attributo LDAP | Tipo di attestazione in uscita |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Si noti che questi nomi non verranno visualizzati nell'elenco a discesa tipo di attestazione in uscita. È necessario digitarli manualmente in. (L'elenco a discesa è effettivamente modificabile).

12.  In base al tipo di certificato è possibile che sia necessario impostare l'algoritmo HASH. Nella finestra delle proprietà del trust della relying party (B2C Demo) selezionare la scheda **Avanzate** e impostare **Secure hash algorithm** su `SHA-256`, quindi fare clic su **OK**.
13. In Server Manager selezionare **strumenti** e quindi selezionare **gestione ad FS**.
14. Selezionare il trust della relying party creato, selezionare **Update from Federation Metadata** (Aggiorna da metadati federazione) e quindi fare clic su **Aggiorna**.

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **criteri** selezionare **Framework esperienza di identità**
1. Selezionare, ad esempio, i criteri di relying party `B2C_1A_signup_signin` .
1. Per **applicazione** selezionare un'applicazione Web [registrata in precedenza](tutorial-register-applications.md). L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .
1. Dalla pagina di iscrizione o accesso selezionare **contoso ad FS** per accedere con Contoso ad FS provider di identità.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

## <a name="troubleshooting-ad-fs-service"></a>Risoluzione dei problemi relativi al servizio AD FS  

AD FS è configurato per l'utilizzo del registro applicazioni di Windows. Se si verificano problemi durante la configurazione di AD FS come provider di identità SAML usando criteri personalizzati in Azure AD B2C, è consigliabile controllare il registro eventi di AD FS:

1. Nella barra di **ricerca** di Windows digitare **Visualizzatore eventi** e quindi selezionare l'app desktop **Visualizzatore eventi** .
1. Per visualizzare il registro di un altro computer, fare clic con il pulsante destro del mouse su **Visualizzatore eventi (locale)** . Scegliere **Connetti a un altro computer**, quindi immettere le informazioni necessarie nella finestra di dialogo **Selezione computer**.
1. In **Visualizzatore eventi** aprire **Registri applicazioni e servizi**.
1. Selezionare **ad FS**, quindi selezionare **amministratore**. 
1. Per visualizzare ulteriori informazioni su un evento, fare doppio clic sull'evento stesso.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>La richiesta SAML non è firmata con l'evento di algoritmo di firma previsto

Questo errore indica che la richiesta SAML inviata da Azure AD B2C non è firmata con l'algoritmo di firma previsto configurato nel AD FS. Ad esempio, la richiesta SAML è firmata con l'algoritmo di firma `rsa-sha256` , ma l'algoritmo di firma previsto è `rsa-sha1` . Per risolvere questo problema, assicurarsi che sia Azure AD B2C che AD FS siano configurati con lo stesso algoritmo di firma.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Opzione 1: impostare l'algoritmo di firma in Azure AD B2C  

È possibile configurare la modalità di firma della richiesta SAML in Azure AD B2C. I metadati [XmlSignatureAlgorithm](identity-provider-generic-saml.md) controllano il valore del `SigAlg` parametro (stringa di query o parametro post) nella richiesta SAML. Nell'esempio seguente viene configurato Azure AD B2C per l'utilizzo dell' `rsa-sha256` algoritmo di firma.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Opzione 2: impostare l'algoritmo di firma in AD FS 

In alternativa, è possibile configurare l'algoritmo di firma della richiesta SAML previsto in AD FS.

1. In Server Manager selezionare **strumenti** e quindi selezionare **gestione ad FS**.
1. Selezionare il **trust della relying party** creato in precedenza.
1. Selezionare **Proprietà**, quindi fare clic su **Avanti** .
1. Configurare l' **algoritmo hash sicuro** e selezionare **OK** per salvare le modifiche.

::: zone-end
