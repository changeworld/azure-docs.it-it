---
title: Risolvere i problemi relativi ai criteri personalizzati in Azure Active Directory B2C
description: Informazioni sugli approcci per la risoluzione degli errori quando si lavora con i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103910"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Risolvere i problemi di Azure AD B2C criteri personalizzati

Se si utilizzano i [criteri personalizzati](custom-policy-overview.md)Azure Active Directory B2C (Azure ad B2C), è possibile che si verifichino problemi relativi al formato XML o ai problemi di runtime del linguaggio dei criteri. Questo articolo descrive alcuni strumenti e suggerimenti che consentono di individuare e risolvere i problemi. 

Questo articolo è incentrato sulla risoluzione dei problemi della configurazione dei criteri personalizzati di Azure AD B2C. Non tratta l'applicazione relying party o la relativa libreria di identità.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Panoramica dell'ID di correlazione Azure AD B2C

Azure AD B2C ID di correlazione è un valore di identificatore univoco collegato alle richieste di autorizzazione. Passa attraverso tutti i passaggi dell'orchestrazione eseguiti da un utente. Con l'ID di correlazione è possibile:

- Identificare l'attività di accesso nell'applicazione e tenere traccia delle prestazioni dei criteri.
- Trovare i log di applicazione Azure Insights della richiesta di accesso.
- Passare l'ID di correlazione all'API REST e usarlo per identificare il flusso di accesso. 

L'ID di correlazione viene modificato ogni volta che viene stabilita una nuova sessione. Quando si esegue il debug dei criteri, assicurarsi di chiudere le schede esistenti del browser. In alternativa, aprire un nuovo browser in modalità privata.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Ottenere l'ID di correlazione Azure AD B2C

È possibile trovare l'ID di correlazione nella pagina Azure AD B2C iscrizione o accesso. Nel browser selezionare **Visualizza origine**. La correlazione viene visualizzata come commento nella parte superiore della pagina.

![Screenshot dell'origine della visualizzazione della pagina di accesso Azure AD B2C.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Copiare l'ID di correlazione e continuare il flusso di accesso. Usare l'ID di correlazione per osservare il comportamento di accesso. Per ulteriori informazioni, vedere [risoluzione dei problemi con Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Echo ID correlazione Azure AD B2C

È possibile includere l'ID di correlazione nei token di Azure AD B2C. Per includere l'ID di correlazione:

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'attestazione City all'elemento **ClaimsSchema** .  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Aprire il file di relying party del criterio. Ad esempio, <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> file. L'attestazione di output verrà aggiunta al token dopo un percorso utente completato e inviato all'applicazione. Modificare l'elemento profilo tecnico nella sezione relying party per aggiungere la città come attestazione di output.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Risoluzione dei problemi con Application Insights

Per diagnosticare i problemi relativi ai criteri personalizzati, utilizzare [Application Insights](troubleshoot-with-application-insights.md). Application Insights traccia l'attività del percorso utente del criterio personalizzato. Consente di diagnosticare le eccezioni e osservare lo scambio di attestazioni tra Azure AD B2C e i diversi provider di attestazioni definiti da profili tecnici, ad esempio provider di identità, servizi basati su API, directory utente Azure AD B2C e altri servizi.  

Si consiglia di installare l' [estensione Azure ad B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) per [vs code](https://code.visualstudio.com/). Con l'estensione Azure AD B2C, i log sono organizzati in base al nome dei criteri, all'ID di correlazione (Application Insights presenta la prima cifra dell'ID di correlazione) e al timestamp del log. Questa funzionalità consente di trovare il log pertinente in base al timestamp locale e di visualizzare il percorso utente come eseguito da Azure AD B2C. 

> [!NOTE]
> - Si verifica un breve ritardo, in genere meno di cinque minuti, prima che sia possibile visualizzare nuovi log in Application Insights.
> - La community ha sviluppato l'estensione Visual Studio Code per Azure AD B2C per aiutare gli sviluppatori di identità. L'estensione non è supportata da Microsoft e viene resa disponibile esclusivamente così com'è.

Un flusso di accesso singolo può emettere più di una traccia applicazione Azure Insights. Nello screenshot seguente i criteri di *B2C_1A_signup_signin* hanno tre log. Ogni log rappresenta una parte del flusso di accesso.

Lo screenshot seguente mostra l'estensione Azure AD B2C per VS Code con applicazione Azure Insights Trace Explorer.

![Screenshot dell'estensione Azure AD B2C per VS Code con la traccia di applicazione Azure Insights.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrare il log di traccia

Con lo stato attivo su Esplora traccia Azure AD B2C, iniziare a digitare la prima cifra dell'ID di correlazione o l'ora che si desidera trovare. Verrà visualizzata una casella di filtro in alto a destra della Azure AD B2C Esplora traccia che mostra le informazioni digitate finora e i log di traccia corrispondenti verranno evidenziati.  

![Screenshot dell'estensione Azure AD B2C Azure AD B2C evidenziazione del filtro di Esplora traccia.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Quando si passa il mouse sulla casella di filtro e si seleziona **Abilita filtro sul tipo** , vengono visualizzati solo i log di traccia corrispondenti. Usare il **pulsante Clear "X"** per cancellare il filtro.

![Screenshot del filtro Azure AD B2C estensione Azure AD B2C Trace Explorer.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Dettagli del log di traccia Application Insights

Quando si seleziona una traccia applicazione Azure Insights, l'estensione apre la finestra dei **dettagli Application Insights** con le informazioni seguenti:

- **Application Insights** : informazioni generiche sul log di traccia, inclusi il nome dei criteri, l'ID di correlazione, l'ID di traccia applicazione Azure Insights e il timestamp di traccia.
- **Profili tecnici** : elenco di profili tecnici visualizzati nel log di traccia.
- **Attestazioni** : elenco alfabetico delle attestazioni visualizzate nel log di traccia e relativi valori. Se un'attestazione viene visualizzata più volte nel log di traccia con valori diversi, un `=>` segno designa il valore più recente. È possibile esaminare le attestazioni per determinare se i valori di attestazione previsti sono impostati correttamente. Se, ad esempio, si dispone di una precondizione che controlla un valore di attestazione, la sezione attestazioni consente di determinare perché un flusso previsto si comporta in modo diverso.
- **Trasformazione delle attestazioni** : elenco delle trasformazioni delle attestazioni visualizzate nel log di traccia. Ogni trasformazione delle attestazioni contiene le attestazioni di input, i parametri di input e le attestazioni di output. La sezione relativa alla trasformazione delle attestazioni fornisce informazioni approfondite sui dati inviati e il risultato della trasformazione delle attestazioni.
- **Token** : elenco di token che vengono visualizzati nel log di traccia. I token includono i token dell'OAuth federato sottostante e del provider di identità OpenId Connect. Il token del provider di identità federato fornisce informazioni dettagliate sul modo in cui il provider di identità restituisce le attestazioni a Azure AD B2C in modo da poter mappare le attestazioni di output del profilo tecnico del provider di identità. 
- **Eccezioni** : elenco di eccezioni o errori irreversibili visualizzati nel log di traccia.
- **Application Insights JSON** : dati non elaborati restituiti dall'Application Insights.

Lo screenshot seguente mostra un esempio della finestra dei dettagli del log di traccia Application Insights.  

![Screenshot dell'estensione Azure AD B2C Azure AD B2C report di traccia.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Risolvere i problemi relativi ai token JWT

Per la convalida e il debug del token JWT, il può decodificare token JWT usando un sito come [https://jwt.ms](https://jwt.ms) . Creare un'applicazione di test in grado di reindirizzare a `https://jwt.ms` per l'ispezione dei token. Se non è già stato fatto, [registrare un'applicazione Web](tutorial-register-applications.md)e [abilitare la concessione implicita token ID](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Screenshot dell'anteprima del token JWT.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Usare **Esegui ora** e `https://jwt.ms` per testare i criteri indipendentemente dall'applicazione Web o per dispositivi mobili. Questo sito Web funziona come un'applicazione relying party. Viene visualizzato il contenuto del token JSON Web (JWT) generato dal criterio di Azure AD B2C.

## <a name="troubleshoot-saml-protocol"></a>Risolvere i problemi del protocollo SAML

Per semplificare la configurazione e il debug dell'integrazione con il provider di servizi, è possibile usare un'estensione del browser per il protocollo SAML, ad esempio [SAML devtools Extension](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) for Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) for Firefox o [Edge o IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Lo screenshot seguente illustra il modo in cui l'estensione DevTools SAML presenta la richiesta SAML Azure AD B2C invia al provider di identità e la risposta SAML.

![Screenshot del log di traccia del protocollo SAML.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Usando questi strumenti, è possibile controllare l'integrazione tra l'applicazione e Azure AD B2C. Ad esempio:

- Controllare se la richiesta SAML contiene una firma e determinare quale algoritmo viene usato per firmare la richiesta di autorizzazione.
- Controllare se Azure AD B2C restituisce un messaggio di errore.
- Controllare se la sezione dell'asserzione è crittografata.
- Ottenere il nome delle attestazioni che restituiscono il provider di identità.

È inoltre possibile tracciare lo scambio di messaggi tra il browser client e Azure AD B2C, con [Fiddler](https://www.telerik.com/fiddler). Consente di ottenere un'indicazione del punto in cui il percorso utente genera errori nei passaggi di orchestrazione.

## <a name="troubleshoot-policy-validity"></a>Risolvere i problemi di validità del criterio

Al termine dello sviluppo dei criteri, caricare i criteri in Azure AD B2C. potrebbero verificarsi problemi con i criteri. Usare i metodi seguenti per garantire l'integrità e la validità dei criteri.

L'errore più comune nell'impostazione dei criteri è la formattazione XML non corretta. Un buon editor XML è essenziale. Viene visualizzato il codice XML in modo nativo, i codici colore, precompila i termini comuni, mantiene indicizzati gli elementi XML e può essere convalidato in base a un XML Schema.

Si consiglia di usare [Visual Studio Code](https://code.visualstudio.com/). Installare quindi un'estensione XML, ad esempio il [supporto del linguaggio XML da Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). L'estensione XML consente di convalidare il XML Schema prima di caricare il file XML utilizzando la definizione dello schema [xsd](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) dei criteri personalizzati.

È possibile utilizzare la strategia di associazione file XML per associare il file XML al file XSD aggiungendo le impostazioni seguenti nel file di VS Code `settings.json` . A tale scopo, procedere nel seguente modo:

1. In VS Code fare clic sulle **Impostazioni**. Per ulteriori informazioni, vedere [impostazioni dell'area di lavoro e degli utenti](https://code.visualstudio.com/docs/getstarted/settings).
1. Cercare le **associazioni FileAssociations**, quindi sotto l' **estensione** selezionare il **codice XML**.
1. Selezionare **modifica in settings.js**.

    ![Screenshot della convalida VS Code XML Schema.](./media/troubleshoot-custom-policies/xml-validation.png)
1. Nella settings.jsin aggiungere il codice JSON seguente:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Nell'esempio seguente viene illustrato un errore di convalida XML. Quando si sposta il puntatore del mouse sul nome dell'elemento, l'estensione elenca gli elementi previsti.

![Screenshot dell'indicatore di errore di convalida VS Code XML Schema.](./media/troubleshoot-custom-policies/xml-validation-error.png)

Nel caso seguente, l' `DisplayName` elemento è corretto. Ma nell'ordine errato. `DisplayName`Deve essere precedente all' `Protocol` elemento. Per risolvere il problema, spostare il puntatore del mouse sull' `DisplayName` elemento nell'ordine corretto degli elementi.

![Screenshot della VS Code XML Schema errore dell'ordine di convalida.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Convalida dei criteri e del caricamento dei criteri

La convalida del file di criteri XML viene eseguita automaticamente al caricamento. La maggior parte degli errori causano errori del caricamento. La convalida include il file dei criteri che si sta caricando. Include anche la catena di file a cui si riferisce il file di caricamento (il file dei criteri relying party, il file delle estensioni e il file di base).

> [!TIP]
> Azure AD B2C esegue una convalida aggiuntiva per i criteri di relying party. Quando si verificano problemi con i criteri, anche se si modificano solo i criteri di estensione, è consigliabile caricare anche i criteri di relying party. 

Questa sezione contiene gli errori di convalida comuni e le soluzioni probabili.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Errore di convalida dello schema... contiene l'elemento figlio ' {name}' non valido

Il criterio contiene un elemento XML non valido oppure l'elemento XML è valido, ma sembra che sia nell'ordine errato. Per correggere questo tipo di errore, vedere la sezione [risolvere i problemi relativi alla validità dei criteri](#troubleshoot-policy-validity) .

### <a name="there-is-a-duplicate-key-sequence-number"></a>È presente una sequenza di chiave duplicata ' {Number}' 

Un [percorso](userjourneys.md) utente o un [percorso secondario](subjourneys.md) è costituito da un elenco ordinato di passaggi di orchestrazione che vengono eseguiti in sequenza. Dopo aver modificato il percorso, rinumerare i passaggi in sequenza senza ignorare i numeri interi da 1 a N.

> [!TIP]
> È possibile utilizzare l' [estensione Azure ad B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) per [vs code](https://code.visualstudio.com/) `(Shift+Ctrl+r)` comando per rinumerare tutti i percorsi utente e i passaggi di orchestrazione dei percorsi secondari nei criteri.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... era previsto un passaggio con l'ordine "{Number}", ma non è stato trovato...

Controllare l'errore precedente.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Ordine "{Number}" del passaggio dell'orchestrazione nel percorso utente "{Name}"... è seguito da un passaggio di selezione del provider di attestazioni e deve essere uno scambio di attestazioni, ma è di tipo...

Il tipo di passaggi dell'orchestrazione di `ClaimsProviderSelection` e `CombinedSignInAndSignUp` contiene un elenco di opzioni da cui un utente può scegliere. Deve seguire per tipo `ClaimsExchange` con uno o più scambi di attestazioni.

I passaggi dell'orchestrazione seguenti provocano questo tipo o errore. Il secondo passaggio di orchestrazione deve essere di tipo `ClaimsExchange` , non `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... passaggio {Number} con 2 scambi di attestazioni. Deve essere preceduta da una selezione del provider di attestazioni per determinare quale scambio di attestazioni può essere utilizzato

Un tipo di passaggio dell'orchestrazione di `ClaimsExchange` deve disporre di un singolo oggetto `ClaimsExchange` , a meno che il passaggio precedente non sia di tipo `ClaimsProviderSelection` o `CombinedSignInAndSignUp` . I passaggi dell'orchestrazione seguenti provocano questo tipo di errore. Il sesto passaggio contiene due scambi di attestazioni. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Per correggere questo tipo di errore, utilizzare due passaggi dell'orchestrazione. Ogni passaggio dell'orchestrazione con uno scambio di attestazioni.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>È presente una sequenza di chiave duplicata ' {name}'

Un percorso ha più `ClaimsExchange` con lo stesso `Id` . I passaggi seguenti provocano questo tipo di errore. L'ID *AADUserWrite* viene visualizzato due volte nel percorso utente.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Per correggere questo tipo di errore, modificare lo scambio delle attestazioni dell'ottavo passaggio di orchestrazione in un nome univoco, ad esempio *Call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... Crea un riferimento a ClaimType con ID "{Claim Name}" ma né i criteri né i relativi criteri di base contengono tale elemento

Questo tipo di errore si verifica quando i criteri fanno riferimento a un'attestazione che non è dichiarata nello [schema delle attestazioni](claimsschema.md). Le attestazioni devono essere definite in almeno uno dei file del criterio. 

Ad esempio, un profilo tecnico con l'attestazione di output *schoolId* . Tuttavia, l'attestazione di output *schoolId* non viene mai dichiarata nei criteri o in un criterio predecessore.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Per correggere questo tipo di errore, controllare se il `ClaimTypeReferenceId` valore non è stato digitato correttamente o non esiste nello schema. Se l'attestazione è definita nei criteri delle estensioni, ma viene usata anche nel criterio di base. Verificare che l'attestazione sia definita nei criteri in uso o in un criterio di livello superiore.

L'aggiunta dell'attestazione allo schema delle attestazioni risolve questo tipo di errore.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... Crea un riferimento a un ClaimsTransformation con ID...

La cause di questo errore è simile a quella per l'errore di attestazione. Controllare l'errore precedente.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>L'utente è attualmente registrato come utente del tenant ' yourtenant.onmicrosoft.com '...

Si esegue l'accesso con un account di un tenant diverso dal criterio che si tenta di caricare. Ad esempio, si esegue l'accesso con admin@contoso.onmicrosoft.com , mentre il criterio `TenantId` è impostato su `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Verificare che il `TenantId` valore negli `<TrustFrameworkPolicy\>` elementi e `<BasePolicy\>` corrisponda al tenant Azure ad B2C di destinazione.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Il tipo di attestazione "{Name}" è l'attestazione di output del profilo tecnico del relying party, ma non è un'attestazione di output nei passaggi del percorso utente...

In un criterio di relying party è stata aggiunta un'attestazione di output, ma l'attestazione di output non è un'attestazione di output in uno dei passaggi del percorso utente. Azure AD B2C non è in grado di leggere il valore dell'attestazione dall'elenco delle attestazioni.

Nell'esempio seguente, l'attestazione *schoolId* è un'attestazione di output del profilo tecnico del relying party, ma non è un'attestazione di output in nessuno dei passaggi del percorso utente di *SignUpOrSignIn* .

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Per correggere questo tipo di errore, assicurarsi che le attestazioni di output vengano visualizzate in almeno una raccolta di attestazioni di output del profilo tecnico dei passaggi dell'orchestrazione. Se il percorso utente non è in grado di generare l'attestazione, nel profilo tecnico relying party impostare un valore predefinito, ad esempio una stringa vuota.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Formato della stringa di input non corretto

Impostare un tipo di valore non corretto su un'attestazione di un altro tipo. Ad esempio, si definisce un'attestazione di tipo Integer.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Provare quindi a impostare un valore stringa:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Per correggere questo tipo di errore, assicurarsi di impostare il valore corretto, ad esempio `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Il tenant "{Name}" ha già un criterio con ID "{Name}". Non è possibile archiviare un altro criterio con lo stesso ID

Si tenta di caricare un criterio nel tenant, ma nel tenant è già stato caricato un criterio con lo stesso nome. 

Per correggere questo tipo di errore, quando si carica il criterio, selezionare la casella di controllo **Sovrascrivi il criterio personalizzato se esiste già** .

![Screenshot che illustra come sovrascrivere i criteri personalizzati, se esistenti.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [raccogliere i log di Azure Active Directory B2C con Application Insights](troubleshoot-with-application-insights.md).

