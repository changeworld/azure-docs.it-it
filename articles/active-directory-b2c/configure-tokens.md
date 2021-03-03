---
title: Configurare i token - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare le impostazioni di compatibilità e durata dei token in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654375"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurare i token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Questo articolo illustra come configurare [la durata e la compatibilità di un token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Comportamento della durata dei token

È possibile configurare la durata dei token, tra cui:

- **Durate dei token di accesso e ID (minuti)** : durata del bearer token OAuth 2,0 e dei token ID. Il valore predefinito è 60 minuti (1 ora). Il valore minimo (inclusivo) è 5 minuti. Il valore massimo (incluso) è 1.440 minuti (24 ore).
- **Durata del token di aggiornamento (giorni)** : periodo di tempo massimo prima del quale è possibile usare un token di aggiornamento per acquisire un nuovo token di accesso, se all'applicazione è stato concesso l' `offline_access` ambito. Il valore predefinito è 14 giorni. Il valore minimo (inclusivo) è un giorno. Massimo (inclusivo) 90 giorni.
- **Durata della finestra temporale scorrevole del token** di aggiornamento: tipo di finestra temporale scorrevole del token di aggiornamento. `Bounded` indica che il token di aggiornamento può essere esteso come specificato nella **lunghezza della durata (giorni)**. `No expiry` indica che la durata della finestra temporale scorrevole del token di aggiornamento non scade mai.
- Durata **(giorni)** : dopo questo periodo di tempo, l'utente è obbligato a eseguire nuovamente l'autenticazione, indipendentemente dal periodo di validità del token di aggiornamento più recente acquisito dall'applicazione. Il valore deve essere maggiore o uguale al valore della **durata del token di aggiornamento** .

Il diagramma seguente illustra il comportamento della durata della finestra temporale scorrevole del token di aggiornamento.

![Durata del token di aggiornamento](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >Le applicazioni a singola pagina che usano il flusso del codice di autorizzazione con PKCE hanno sempre una durata del token di aggiornamento di 24 ore mentre le app per dispositivi mobili, le app desktop e le app Web non presentano questa limitazione. [Altre informazioni sulle implicazioni di sicurezza dei token di aggiornamento nel browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>Configurare la durata del token

::: zone pivot="b2c-user-flow"

Per configurare la durata del token del flusso utente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente (criteri)** .
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Proprietà**.
1. In **Durata token** modificare le proprietà in base alle esigenze dell'applicazione.
1. Fare clic su **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Per modificare le impostazioni sulla compatibilità dei token, impostare i metadati del profilo tecnico dell' [emittente del token](jwt-issuer-technical-profile.md) nell'estensione oppure il file di relying party del criterio che si desidera influisca. Il profilo tecnico dell'emittente del token ha un aspetto simile al seguente:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Nell'esempio precedente vengono impostati i valori seguenti.

- durate del token di accesso **token_lifetime_secs** (secondi). Il valore predefinito è 3.600 (1 ora). Il valore minimo è 300 (5 minuti). Il valore massimo è 86.400 (24 ore). 
- durata del token **id_token_lifetime_secs** ID (secondi). Il valore predefinito è 3.600 (1 ora). Il valore minimo è 300 (5 minuti). Il valore massimo è 86.400 (24 ore). 
- **refresh_token_lifetime_secs** Durata del token di aggiornamento (secondi). Il valore predefinito è 120, 9600 (14 giorni). Il valore minimo è 86.400 (24 ore). Il valore massimo è 7.776.000 (90 giorni). 
- durata della finestra temporale scorrevole del token di aggiornamento **rolling_refresh_token_lifetime_secs** (secondi). Il valore predefinito è 7.776.000 (90 giorni). Il valore minimo è 86.400 (24 ore). Il valore massimo è 31.536.000 (365 giorni). Se non si desidera applicare una durata della finestra temporale scorrevole, impostare il valore di `allow_infinite_rolling_refresh_token` su `true` . 
- la durata della finestra temporale scorrevole del token di aggiornamento **allow_infinite_rolling_refresh_token** non scade mai. 

::: zone-end


## <a name="token-compatibility-settings"></a>Impostazioni di compatibilità dei token

È possibile configurare la compatibilità dei token, tra cui:

- **Attestazione dell'autorità emittente (ISS)** : il formato di autorità emittente del token di accesso e ID.
- **Attestazione Subject (Sub)** : entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory.
- **Attestazione che rappresenta il flusso utente** : questa attestazione identifica il flusso utente eseguito. I valori possibili sono: `tfp` (impostazione predefinita) o `acr`.

::: zone pivot="b2c-user-flow"

Per configurare le impostazioni di compatibilità del flusso utente:

1. Selezionare **Flussi utente (criteri)** .
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Proprietà**.
1. In **impostazioni di compatibilità dei token** modificare le proprietà in base alle esigenze dell'applicazione.
1. Fare clic su **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Per modificare le impostazioni sulla compatibilità dei token, impostare i metadati del profilo tecnico dell' [emittente del token](jwt-issuer-technical-profile.md) nell'estensione oppure il file di relying party del criterio che si desidera influisca. Il profilo tecnico dell'emittente del token ha un aspetto simile al seguente:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Attestazione autorità di certificazione (iss)**: l'attestazione dell'autorità di certificazione (iss) viene impostata con l'elemento dei metadati **IssuanceClaimPattern**. I valori applicabili sono `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Impostazione dell'attestazione che rappresenta l'ID criteri**: per impostare questo valore sono disponibili le opzioni `TFP` (criteri del framework attendibilità) e `ACR` (riferimento al contesto di autenticazione). Il valore consigliato è `TFP`. Impostare **AuthenticationContextReferenceClaimPattern** con il valore `None`.

    Nell'elemento **ClaimsSchema**, aggiungere l'elemento seguente:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Nell'elemento **OutputClaims** aggiungere questo elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Per ACR, rimuovere l'elemento **AuthenticationContextReferenceClaimPattern**.

- **Attestazione soggetto (sub)**: l'impostazione predefinita di questa opzione è ObjectID. Per modificare l'impostazione in `Not Supported`, sostituire questa riga:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    con la riga seguente:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Fornire attestazioni facoltative all'app

Le attestazioni dell'applicazione sono valori restituiti all'applicazione. Aggiornare il flusso utente per includere le attestazioni desiderate.

::: zone pivot="b2c-user-flow"

1. Selezionare **Flussi utente (criteri)** .
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Attestazioni dell'applicazione**.
1. Scegliere le attestazioni e gli attributi che si desidera inviare nuovamente all'applicazione.
1. Fare clic su **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Le attestazioni di output del [profilo tecnico dei criteri della relying party](relyingparty.md#technicalprofile) sono valori restituiti a un'applicazione. L'aggiunta di attestazioni di output emetterà le attestazioni nel token dopo un percorso utente riuscito e verrà inviata all'applicazione. Modificare l'elemento profilo tecnico nella sezione relying party per aggiungere le attestazioni desiderate come attestazione di output.

1. Aprire il file dei criteri personalizzati. Ad esempio, SignUpOrSignin.xml.
1. Trovare l'elemento OutputClaims. Aggiungere il OutputClaim che si desidera includere nel token. 
1. Impostare gli attributi di attestazione di output. 

Nell'esempio seguente viene aggiunta l' `accountBalance` attestazione. L'attestazione accountBalance viene inviata all'applicazione come equilibrio. 

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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

L'elemento OutputClaim contiene gli attributi seguenti:

- **ClaimTypeReferenceId** : identificatore di un tipo di attestazione già definito nella sezione [ClaimsSchema](claimsschema.md) del file di criteri o del file di criteri padre.
- **PartnerClaimType** : consente di modificare il nome dell'attestazione nel token. 
- **DefaultValue** : valore predefinito. È inoltre possibile impostare il valore predefinito su un [resolver di attestazioni](claim-resolver-overview.md), ad esempio l'ID tenant.
- **AlwaysUseDefaultValue** -forza l'uso del valore predefinito.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [richiedere token di accesso](access-tokens.md).