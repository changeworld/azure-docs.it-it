---
title: Aggiungere attributi utente e personalizzare l'input dell'utente
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'input dell'utente e aggiungere gli attributi utente al percorso di iscrizione o accesso in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4e709719d56aacacf61e247a5dbe215f766a891a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607952"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Aggiungere attributi utente e personalizzare l'input dell'utente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

In questo articolo viene raccolto un nuovo attributo durante il percorso di iscrizione in Azure Active Directory B2C (Azure AD B2C). Si otterrà la città degli utenti, la si configura come un elenco a discesa e si definisce se è necessario fornirla.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Aggiungere attributi utente al flusso utente

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Selezionare **attributi utente** e quindi selezionare l'attributo utente, ad esempio "City". 
1. Selezionare **Salva**.

## <a name="provide-optional-claims-to-your-app"></a>Fornire attestazioni facoltative all'app

Le attestazioni dell'applicazione sono valori restituiti all'applicazione. Aggiornare il flusso utente per includere le attestazioni desiderate.

1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Selezionare **Attestazioni dell'applicazione**.
1. Selezionare gli attributi che si desidera restituire all'applicazione (ad esempio, "City").
1. Selezionare **Salva**.
 
## <a name="configure-user-attributes-input-type"></a>Configurare il tipo di input degli attributi utente

1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Selezionare **layout di pagina**.
1. Selezionare **la pagina di iscrizione dell'account locale**.
1. In **attributi utente** selezionare **città**.
    1. Nell'elenco a discesa **tipo di input utente** selezionare **DropdownSingleSelect**.
    1. Nell'elenco a discesa **facoltativo** selezionare **No**.
1. Selezionare **Salva**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Fornire un elenco di valori usando le raccolte localizzate

Per fornire un elenco set di valori per l'attributo City: 

1. [Abilitare la personalizzazione della lingua nel flusso utente](language-customization.md#support-requested-languages-for-ui_locales)
1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Nella pagina **Lingua** del flusso utente selezionare la lingua che si vuole personalizzare.
1. In **file di risorse a livello di pagina** selezionare **la pagina di iscrizione dell'account locale**.
1. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua).
1. Creare un `LocalizedCollections` attributo.

`LocalizedCollections`È una matrice di `Name` coppie e `Value` . L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati. 

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Caricamento delle modifiche

1. Dopo aver completato le modifiche al file JSON, tornare al tenant B2C.
1. Selezionare **flussi utente** e selezionare il criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Selezionare **Lingue**.
1. Selezionare la lingua in cui si vuole tradurre.
1. Selezionare la **pagina di iscrizione dell'account locale**.
1. Selezionare l'icona della cartella e selezionare il file JSON da caricare. Le modifiche vengono salvate automaticamente nel flusso utente.

## <a name="test-your-user-flow"></a>Testare il flusso utente

1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Questo esempio usa l'attestazione predefinita "City". In alternativa, è possibile scegliere uno degli [attributi predefiniti Azure ad B2C](user-profile-attributes.md) supportati o un attributo personalizzato. Per usare un attributo personalizzato, [abilitare gli attributi personalizzati](user-flow-custom-attributes.md). Per usare un attributo predefinito o personalizzato diverso, sostituire ' City ' con l'attributo desiderato, ad esempio l'attributo predefinito *JobTitle* o un attributo personalizzato come *extension_loyaltyId*.  

È possibile raccogliere i dati iniziali dagli utenti usando il percorso utente di iscrizione o accesso. In un secondo momento è possibile raccogliere attestazioni aggiuntive usando il percorso utente di modifica del profilo. Ogni volta che Azure AD B2C raccoglie informazioni direttamente dall'utente in modo interattivo, il Framework dell'esperienza di gestione delle identità usa il [profilo tecnico autocertificato](self-asserted-technical-profile.md). In questo esempio, è possibile:

1. Definire un'attestazione "City". 
1. Chiedere all'utente la città.
1. Salvare la città nel profilo utente nella directory Azure AD B2C.
1. Leggere l'attestazione City dalla directory Azure AD B2C a ogni accesso.
1. Restituire la città all'applicazione relying party dopo l'accesso o l'iscrizione.  

## <a name="define-a-claim"></a>Definire un'attestazione

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione di un Azure AD B2C criteri. Lo [schema di attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni. Vengono usati gli elementi seguenti per definire l'attestazione:

- **DisplayName**: una stringa che definisce l'etichetta destinata all'utente.
- [DataType](claimsschema.md#datatype) : tipo di attestazione.
- **UserHelpText**: consente all'utente di identificare i requisiti.
- [Tipo](claimsschema.md#userinputtype) : tipo di controllo di input, ad esempio casella di testo, selezione di Radio, elenco a discesa o selezioni multiple.

Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'attestazione City all'elemento **ClaimsSchema** .  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

## <a name="add-a-claim-to-the-user-interface"></a>Aggiungere un'attestazione all'interfaccia utente

I seguenti profili tecnici sono [autofirmati](self-asserted-technical-profile.md), richiamati quando si prevede che un utente fornisca l'input:

- **LocalAccountSignUpWithLogonEmail** -flusso di iscrizione dell'account locale.
- **SelfAsserted-** accesso utente per la prima volta all'account di social network federato.
- **SelfAsserted-ProfileUpdate** -modifica flusso del profilo.

Per raccogliere l'attestazione City durante l'iscrizione, è necessario aggiungerla come attestazione di output al `LocalAccountSignUpWithLogonEmail` profilo tecnico. Eseguire l'override di questo profilo tecnico nel file di estensione. Specificare l'intero elenco di attestazioni di output per controllare l'ordine in cui le attestazioni vengono presentate sullo schermo. Trovare l'elemento **ClaimsProviders**. Aggiungere un nuovo ClaimsProviders come segue:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Per raccogliere l'attestazione City dopo l'accesso iniziale con un account federato, è necessario aggiungerla come attestazione di output al `SelfAsserted-Social` profilo tecnico. Per consentire agli utenti di account locali e federati di modificare i dati di profilo in un secondo momento, aggiungere le attestazioni di input e output al `SelfAsserted-ProfileUpdate` profilo tecnico. Eseguire l'override di questi profili tecnici nel file di estensione. Specificare l'intero elenco delle attestazioni di output per controllare l'ordine in cui le attestazioni vengono presentate sullo schermo. Trovare l'elemento **ClaimsProviders**. Aggiungere un nuovo ClaimsProviders come segue:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Lettura e scrittura di un'attestazione

I profili tecnici seguenti sono [Active Directory profili tecnici](active-directory-technical-profile.md)che leggono e scrivono i dati Azure Active Directory.  
Utilizzare `PersistedClaims` per scrivere i dati nel profilo utente e `OutputClaims` per leggere i dati dal profilo utente all'interno dei rispettivi Active Directory profili tecnici.

Eseguire l'override di questi profili tecnici nel file di estensione. Trovare l'elemento **ClaimsProviders**.  Aggiungere un nuovo ClaimsProviders come segue:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Includere un'attestazione nel token 

Per riportare l'attestazione della città all'applicazione relying party, aggiungere un'attestazione di output al <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> file. L'attestazione di output verrà aggiunta al token dopo un percorso utente completato e verrà inviata all'applicazione. Modificare l'elemento profilo tecnico nella sezione relying party per aggiungere la città come attestazione di output.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Carica criteri personalizzati** e caricare i file dei due criteri modificati.
2. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

::: zone-end

La schermata di iscrizione dovrebbe avere un aspetto simile allo screenshot seguente:

![Screenshot dell'opzione di iscrizione modificata](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Il token inviato all'applicazione include l'attestazione `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'elemento [ClaimsSchema](claimsschema.md) nella Guida di riferimento a Framework dell'esperienza.
- Informazioni su come [usare gli attributi personalizzati in Azure ad B2C](user-flow-custom-attributes.md).

::: zone-end
