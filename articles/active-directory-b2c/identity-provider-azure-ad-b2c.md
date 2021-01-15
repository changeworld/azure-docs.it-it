---
title: Configurare l'iscrizione e l'accesso con un account di Azure AD B2C da un altro tenant Azure AD B2C
titleSuffix: Azure AD B2C
description: Fornire l'iscrizione e l'accesso ai clienti con account di Azure AD B2C da un altro tenant nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/14/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b8be516051f8eed0649064ae0f7c29a4dde85675
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224465"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Configurare l'iscrizione e l'accesso con un account di Azure AD B2C da un altro tenant Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Panoramica

Questo articolo descrive come configurare una federazione con un altro tenant Azure AD B2C. Quando le applicazioni sono protette con la Azure AD B2C, questo consente agli utenti di altri Azure AD B2C di accedere con i propri account esistenti. Nel diagramma seguente gli utenti sono in grado di accedere a un'applicazione protetta da Azure AD B2C di *Contoso*, con un account gestito dal tenant di Azure ad B2C di *Fabrikam* 

![Azure AD B2C Federazione con un altro tenant Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

Per usare un account Azure AD B2C come [provider di identità](openid-connect.md) nel tenant di Azure ad B2C (ad esempio, contoso), nell'altra Azure ad B2C (ad esempio, Fabrikam):

1. Creare un [flusso utente](tutorial-create-user-flows.md)o un [criterio personalizzato](custom-policy-get-started.md).
1. Quindi, creare un'applicazione nel Azure AD B2C, come descritto in questa sezione. 

Per creare un'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene l'altro tenant Azure AD B2C (ad esempio, Fabrikam.com).
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *ContosoApp*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi provider di identità o directory dell'organizzazione (per l'autenticazione di utenti con flussi utente)** .
1. In **URI di reindirizzamento** selezionare **Web**, quindi immettere l'URL seguente in lettere minuscole, dove `your-B2C-tenant-name` viene sostituito con il nome del tenant di Azure ad B2C (ad esempio, contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. In autorizzazioni selezionare la casella **di controllo Concedi il consenso dell'amministratore a OpenID e offline_access le autorizzazioni** .
1. Selezionare **Registra**.
1. Nella pagina **Azure ad B2C-registrazioni app** selezionare l'applicazione creata, ad esempio *ContosoApp*.
1. Annotare **l'ID applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.
1. Nel menu a sinistra selezionare **Certificati e segreti** in **Gestisci**.
1. Selezionare **Nuovo segreto client**.
1. Immettere una descrizione per il segreto client nella casella **Descrizione**. Ad esempio, *clientsecret1*.
1. In **Scadenza** selezionare una durata per la quale il segreto è valido, quindi selezionare **Aggiungi**.
1. Registrare il **Valore** del segreto. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Configurare Azure AD B2C come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant Azure AD B2C per cui si vuole configurare la Federazione, ad esempio contoso. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure ad B2C (ad esempio, contoso).
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Fabrikam*.
1. Per **URL metadati** immettere l'URL seguente sostituendo `{tenant}` con il nome di dominio del tenant di Azure ad B2C (ad esempio, Fabrikam). Sostituire `{policy}` con il nome dei criteri configurati nell'altro tenant:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Ad esempio: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Per **ID client**, immettere l'ID applicazione registrato in precedenza.
1. In **Segreto client** immettere il segreto client registrato in precedenza.
1. In **Ambito**, immettere il `openid`.
1. Lasciare i valori predefiniti per **Tipo di risposta** e **Modalità di risposta**.
1. Opzionale Per l' **hint di dominio**, immettere il nome di dominio che si vuole usare per l' [accesso diretto](direct-signin.md#redirect-sign-in-to-a-social-provider). Ad esempio, *Fabrikam.com*.
1. In **Mapping delle attestazioni del provider di identità** selezionare le attestazioni seguenti:

    - **ID utente**: *Sub*
    - **Nome visualizzato**: *name*
    - **Nome**: *given_name*
    - **Cognome**: *family_name*
    - **Posta elettronica** *:*

1. Selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave dell'applicazione creata in precedenza nel tenant del Azure AD B2C.

1. Assicurarsi di usare la directory che contiene il tenant Azure AD B2C per cui si vuole configurare la Federazione, ad esempio contoso. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure ad B2C (ad esempio, contoso).
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `FabrikamAppSecret`.  Il prefisso `B2C_1A_` viene aggiunto automaticamente al nome della chiave quando viene creato, quindi il riferimento nel codice XML nella sezione seguente consiste nel *B2C_1A_FabrikamAppSecret*.
1. In **Secret** immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Create** (Crea).

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Se si desidera che gli utenti possano accedere utilizzando l'altro Azure AD B2C (Fabrikam), è necessario definire gli altri Azure AD B2C come provider di attestazioni con cui Azure AD B2C può comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD B2C come provider di attestazioni aggiungendo Azure AD B2C all'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aggiornare gli elementi XML seguenti con il valore pertinente:

    |Elemento XML  |Valore  |
    |---------|---------|
    |ClaimsProvider\Domain  | Nome di dominio utilizzato per l' [accesso diretto](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Immettere il nome di dominio che si vuole usare nell'accesso diretto. Ad esempio, *Fabrikam.com*. |
    |TechnicalProfile\DisplayName|Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso. Ad esempio, *Fabrikam*. |
    |Metadati \ client_id|L'identificatore dell'attestazione del provider di identità. Aggiornare l'ID client con l'ID applicazione creato in precedenza nell'altro tenant del Azure AD B2C.|
    |Metadata\METADATA|Un URL che punta a un documento di configurazione del provider di identità OpenID Connect, noto anche come endpoint di configurazione OpenID noto. Immettere l'URL seguente sostituendo `{tenant}` con il nome di dominio dell'altro tenant di Azure ad B2C (Fabrikam). Sostituire `{tenant}` con il nome dei criteri configurati nell'altro tenant e `{policy]` con il nome del criterio: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Ad esempio: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Aggiornare il valore di **ID riferimento archiviazione** con il nome della chiave dei criteri creata in precedenza. Ad esempio: `B2C_1A_FabrikamAppSecret`.| 
    

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C sappiano come comunicare con l'altro tenant di Azure AD B2C. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è ancora disponibile in nessuna delle pagine di iscrizione/accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che disponga anche del provider di identità Azure AD:

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInFabrikam`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante del provider di identità in una pagina di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per Azure ad B2C, viene visualizzato un nuovo pulsante quando un utente atterra nella pagina.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato in *TrustFrameworkExtensions.xml*.
1. In **ClaimsProviderSelections** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `FabrikamExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, prevede che Azure AD B2C comunichi con gli altri Azure AD B2C per ricevere un token. Collegare il pulsante a un'azione collegando il profilo tecnico per il provider di attestazioni Azure AD B2C:

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare lo stesso valore per **ID** usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l' **ID** del profilo tecnico creato in precedenza. Ad esempio: `Fabrikam-OpenIdConnect`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Aggiungere Azure AD B2C provider di identità a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente per cui si desidera aggiungere il provider di identità Azure AD B2C.
1. In provider di identità basati su **Social Network** selezionare **Fabrikam**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**
1. Dalla pagina iscrizione o accesso selezionare *Fabrikam* per accedere con l'altro tenant Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, rinominarlo in *SignUpSignInFabrikam.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInFabrikam`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio: `http://contoso.com/B2C_1A_signup_signin_fabrikam`.
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente creato in precedenza. Ad esempio, *SignUpSignInFabrikam*.
1. Salvare le modifiche e caricare il file.
1. In **criteri personalizzati** selezionare il nuovo criterio nell'elenco.
1. Nell'elenco a discesa **Seleziona applicazione** selezionare l'applicazione Azure ad B2C creata in precedenza. Ad esempio, *testapp1*.
1. Selezionare **Esegui adesso** 
1. Dalla pagina iscrizione o accesso selezionare *Fabrikam* per accedere con l'altro tenant Azure ad B2C.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [passare l'altro token Azure ad B2C all'applicazione](idp-pass-through-user-flow.md).
