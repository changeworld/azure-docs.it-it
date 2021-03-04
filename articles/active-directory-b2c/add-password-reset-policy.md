---
title: Configurare un flusso di reimpostazione della password in Azure AD B2C
titleSuffix: Azure AD B2C
description: Informazioni su come configurare un flusso di reimpostazione della password in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8e4926fb228837c3ec0573af8ee5b15da5fd7431
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033908"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di reimpostazione della password in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Flusso di reimpostazione della password

Il [percorso di iscrizione e accesso](add-sign-up-and-sign-in-policy.md) consente agli utenti di reimpostare la propria password usando il collegamento **password dimenticata?** . Il flusso di reimpostazione della password prevede i passaggi seguenti:

1. Dalla pagina di iscrizione e accesso, l'utente fa clic sul collegamento **password dimenticata?** . Azure AD B2C avvia il flusso di reimpostazione della password. 
2. L'utente fornisce e verifica l'indirizzo di posta elettronica con un codice di accesso temporizzato.
3. L'utente può quindi immettere una nuova password.

![Flusso di reimpostazione della password](./media/add-password-reset-policy/password-reset-flow.png)

Il flusso di reimpostazione della password si applica agli account locali in Azure AD B2C che usano un [indirizzo di posta elettronica](identity-provider-local.md#email-sign-in) o un [nome utente](identity-provider-local.md#username-sign-in) con una password per l'accesso.

Una procedura comune dopo la migrazione degli utenti a Azure AD B2C con password casuali consiste nel fare in modo che gli utenti verifichino gli indirizzi di posta elettronica e reimpostino le password durante il primo accesso. È inoltre frequente forzare l'utente a reimpostare la password dopo che un amministratore ha modificato la password. per abilitare questa funzionalità, vedere [Force reimpostazione della password](force-password-reset.md) .

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Reimpostazione password self-service (scelta consigliata)

La nuova esperienza di reimpostazione della password fa ora parte del criterio di iscrizione o di accesso. Quando l'utente seleziona il collegamento **password dimenticata?** , viene immediatamente inviato all'esperienza di password dimenticata. L'applicazione non deve più gestire il [codice di errore AADB2C90118](#password-reset-policy-legacy)e non è necessario un criterio separato per la reimpostazione della password.

::: zone pivot="b2c-user-flow"

L'esperienza di reimpostazione della password self-service può essere configurata per l' **accesso (scelta consigliata)** o per l'iscrizione **e l'accesso (scelta consigliata)** flussi utente. Se non si dispone di un flusso utente di questo tipo, creare un flusso utente [per l'accesso e l'iscrizione](add-sign-up-and-sign-in-policy.md) . 

Per abilitare la reimpostazione della password self-service per il flusso utente di iscrizione o accesso:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare un flusso utente di iscrizione o di accesso (di tipo **consigliato**) che si desidera personalizzare.
1. In **Impostazioni** nel menu a sinistra selezionare **Proprietà**.
1. In **complessità password** selezionare **reimpostazione password self-service**.
1. Selezionare **Salva**.
1. In **Personalizza** nel menu a sinistra selezionare **layout di pagina**.
1. Nella **versione layout di pagina** scegliere **2.1.2-Current** o versione successiva.
1. Selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Le sezioni seguenti descrivono come aggiungere un'esperienza self-service per la password a un criterio personalizzato. L'esempio è basato sui file di criteri inclusi nello [Starter Pack del criterio personalizzato](./custom-policy-get-started.md). 

> [!TIP]
> È possibile trovare un esempio completo del criterio "iscrizione o accesso con reimpostazione della password" in [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Indicare che un utente ha selezionato la password dimenticata? collegamento

Per indicare ai criteri che l'utente ha selezionato il collegamento **password dimenticata?** , definire un'attestazione booleana. Questa attestazione verrà utilizzata per indirizzare il percorso utente al profilo tecnico password dimenticata. Questa attestazione può anche essere rilasciata al token in modo che l'applicazione sappia che l'utente ha eseguito l'accesso tramite il flusso di password dimenticata.

Si dichiarano le attestazioni nello [schema delle attestazioni](claimsschema.md). Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'attestazione seguente all'elemento **ClaimsSchema** . 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Aggiornare la versione del layout di pagina

Versione del layout di [pagina](contentdefinitions.md#migrating-to-page-layout) `2.1.2` è necessario per abilitare il flusso di reimpostazione della password self-service all'interno del percorso di iscrizione o di accesso.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ContentDefinitions](contentdefinitions.md) . Se l'elemento non esiste, aggiungerlo.
1. Modificare l'elemento **DataURI** all'interno dell'elemento **ContentDefinition** con ID **API. signuporsignin** come illustrato di seguito.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Per avviare l' `isForgotPassword` attestazione, viene utilizzato un profilo tecnico per la trasformazione delle attestazioni. Verrà fatto riferimento a questo profilo tecnico in un secondo momento. Quando viene richiamato, il valore dell' `isForgotPassword` attestazione verrà impostato su `true` . Trovare l'elemento `ClaimsProviders`. Se l'elemento non esiste, aggiungerlo. Aggiungere quindi il provider di attestazioni seguente:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Il `SelfAsserted-LocalAccountSignin-Email` profilo tecnico `setting.forgotPasswordLinkOverride` consente di definire lo scambio di attestazioni per la reimpostazione della password da eseguire nel percorso utente. 

### <a name="add-the-password-reset-sub-journey"></a>Aggiungere il percorso secondario per la reimpostazione della password

Il percorso includerà ora la possibilità per l'utente di accedere, iscriversi ed eseguire la reimpostazione della password. Per organizzare meglio il percorso utente, è possibile usare un [percorso secondario](subjourneys.md) per gestire il flusso di reimpostazione della password.

Il percorso secondario verrà chiamato dal percorso utente e eseguirà i passaggi specifici per recapitare all'utente l'esperienza di reimpostazione della password. Utilizzare il `Call` tipo Sub Journey in modo che al termine del percorso secondario venga restituito il controllo al passaggio di orchestrazione che ha avviato il percorso secondario.

Trovare l'elemento `SubJourneys`. Se l'elemento non esiste, aggiungerlo dopo l' `User Journeys` elemento. Aggiungere quindi il percorso secondario seguente:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Preparare il percorso utente

È necessario connettere il collegamento **password dimenticata?** al percorso secondario della password dimenticata. A tale scopo, fare riferimento all'ID del percorso secondario della password dimenticata nell'elemento **ClaimsProviderSelection** del passaggio **CombinedSignInAndSignUp** .

Se non si ha un percorso utente personalizzato con un passaggio **CombinedSignInAndSignUp** , usare la procedura seguente per duplicare un percorso utente di iscrizione o accesso esistente. In caso contrario, passare alla sezione successiva.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Creare un elemento figlio dell'elemento **UserJourneys** incollando l'intero contenuto dell'elemento **UserJourney** copiato nel passaggio 2.
5. Rinominare l'ID del percorso utente. Ad esempio: `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Connettere il collegamento password dimenticata al percorso secondario per la password dimenticata 

Nel percorso utente è possibile rappresentare il percorso secondario della password dimenticata come **ClaimsProviderSelection**. L'aggiunta di questo elemento connette il collegamento **password dimenticata?** al percorso secondario della password dimenticata.

1. Nel percorso utente individuare l'elemento step dell'orchestrazione che include `Type="CombinedSignInAndSignUp"` o `Type="ClaimsProviderSelection"` . Si tratta in genere del primo passaggio dell'orchestrazione. L'elemento **ClaimsProviderSelections** contiene un elenco di provider di identità che un utente può usare per eseguire l'accesso. Aggiungere la riga seguente:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Nel passaggio successivo dell'orchestrazione aggiungere un elemento **ClaimsExchange** . Aggiungere la riga seguente:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

### <a name="set-the-user-journey-to-be-executed"></a>Imposta il percorso utente da eseguire

Ora che è stato modificato o creato un percorso utente, nella sezione **relying party** specificare il percorso in cui verrà eseguito Azure ad B2C per questo criterio personalizzato. Nell'elemento [RelyingParty](relyingparty.md) trovare l'elemento **DefaultUserJourney** . Aggiornare il  **ReferenceId di DefaultUserJourney** in modo che corrisponda all'ID del percorso utente in cui è stato aggiunto il **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Indica il flusso della password dimenticata all'app

Potrebbe essere necessario che l'applicazione rilevi se l'utente ha eseguito l'accesso tramite il flusso utente con password dimenticata. L'attestazione **isForgotPassword** contiene un valore booleano che indica questa, che può essere rilasciata nel token inviato all'applicazione. Se necessario, aggiungere `isForgotPassword` alle attestazioni di output nella sezione **relying party** . L'applicazione può controllare l' `isForgotPassword` attestazione per determinare se l'utente reimposta la propria password.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Caricare il criterio personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **carica criteri personalizzati**, quindi caricare i due file di criteri modificati nell'ordine seguente:
   1. I criteri di estensione, ad esempio `TrustFrameworkExtensions.xml` .
   2. I criteri di relying party, ad esempio `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testare il flusso di reimpostazione della password

1. Selezionare un flusso utente di iscrizione o di accesso (di tipo consigliato) che si desidera testare.
1. Selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.
1. Dalla pagina di iscrizione o accesso selezionare **password dimenticata**.
1. Verificare l'indirizzo di posta elettronica dell'account creato in precedenza e quindi selezionare **continue (continua**).
1. Ora è possibile modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Controllare il valore dell'attestazione del token restituito `isForgotPassword` . Se esiste e è impostato su true, indica che l'utente ha reimpostato la password.

## <a name="password-reset-policy-legacy"></a>Criteri di reimpostazione password (legacy)

Se l'esperienza di [reimpostazione della password self-service](#self-service-password-reset-recommended) non è abilitata, facendo clic su questo collegamento non viene attivato automaticamente il flusso utente per la reimpostazione della password. Viene invece restituito il codice di errore `AADB2C90118` all'applicazione. L'applicazione deve gestire questo codice di errore reinizializzando la libreria di autenticazione per autenticare un flusso utente Azure AD B2C reimpostazione della password.

Nel diagramma seguente:

1. Dall'applicazione, l'utente fa clic su Accedi. L'app avvia una richiesta di autorizzazione e porta l'utente a Azure AD B2C per completare l'accesso. La richiesta di autorizzazione specifica il nome dei criteri di iscrizione o di accesso, ad esempio **B2C_1_signup_signin**.
1. L'utente seleziona il collegamento **password dimenticata?** . Azure AD B2C restituisce all'applicazione il codice di errore AADB2C90118.
1. L'applicazione gestisce il codice di errore e avvia una nuova richiesta di autorizzazione. La richiesta di autorizzazione specifica il nome dei criteri di reimpostazione della password, ad esempio **B2C_1_pwd_reset**.

![Flusso di reimpostazione della password](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Per visualizzare un esempio, esaminare un [semplice esempio ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), che illustra il collegamento dei flussi utente.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per consentire agli utenti dell'applicazione di reimpostare la password, è necessario creare un flusso utente per la reimpostazione della password.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Reimpostazione password**. 
1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
1. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
1. In **attestazioni applicazione** selezionare **Mostra altro** e scegliere le attestazioni che si desidera vengano restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
1. Selezionare **OK**.
1. Selezionare **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina Panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**, verificare l'indirizzo di posta elettronica dell'account creato in precedenza e quindi selezionare **continua**.
1. È ora possibile modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti, tra cui: iscrizione, accesso, reimpostazione della password e criteri di modifica del profilo. Per ulteriori informazioni, vedere [Introduzione ai criteri personalizzati in Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Configurare una [reimpostazione forzata della password](force-password-reset.md).


