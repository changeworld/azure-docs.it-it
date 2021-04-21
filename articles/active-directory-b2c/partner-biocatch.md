---
title: Esercitazione per configurare BioCatch con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Esercitazione per configurare Azure Active Directory B2C con BioCatch per identificare gli utenti rischiosi e fraudolenti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819444"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Esercitazione: Configurare BioCatch con Azure Active Directory B2C

In questa esercitazione di esempio si apprenderà come integrare l'autenticazione B2C Azure Active Directory (AD) con [BioCatch](https://www.biocatch.com/) per aumentare ulteriormente il percorso di sicurezza di Customer Identity and Access Management (CIAM). BioCatch analizza i comportamenti digitali fisici e cognitivi di un utente per generare informazioni dettagliate che distinguono tra clienti legittimi e cyber-criminali.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un Azure AD B2C tenant](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

- Un account [BioCatch.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di BioCatch include i componenti seguenti:

- **Un'app Web o un servizio Web:** l'utente accede prima a questo servizio Web. Questo servizio Web crea un'istanza di un ID sessione client univoco inviato a BioCatch. L'ID sessione client inizia quindi immediatamente a trasmettere le caratteristiche del comportamento utente a BioCatch.

- **Un metodo:** invia l'ID sessione client univoco Azure AD B2C. Nell'esempio fornito, JavaScript viene usato per immettere il valore in un campo HTML nascosto.

- **Interfaccia Azure AD B2C personalizzata:** nasconde un campo HTML per l'input dell'ID sessione client da JavaScript, se si usa il metodo precedente

- **Azure AD B2C criteri personalizzati**

  - Accetta l'ID sessione client personalizzato dall'interfaccia utente sotto forma di attestazione. Questo risultato viene ottenuto tramite un profilo tecnico auto-asserto

  - Si integra con BioCatch tramite un provider di attestazioni API REST e passa l'ID sessione client alla piattaforma BioCatch

  - Vengono restituite più attestazioni personalizzate da BioCatch per la logica dei criteri personalizzata su cui agire

  - Un userjourney, che valuta un'attestazione restituita, ad esempio il rischio di sessione, ed esegue in modo condizionale un'azione, ad esempio invoke Multi-Factor Authentication (MFA).

![Diagramma dell'architettura bio catch.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Passaggio  | Descrizione |
|:---|:-----------------------|
|1a     | L'utente esplora il servizio Web. Il servizio Web restituisce quindi i valori HTML, CSS o JavaScript e configura per caricare BioCatch JavaScript SDK. JavaScript lato client configura/imposta l'ID sessione client per BioCatch SDK. In alternativa, il servizio Web può pre-configurare l'ID sessione client e inviarlo al client.        |
|1b    |  Configurare BioCatch JavaScript SDK di cui è stata creata un'istanza sulla piattaforma BioCatch. Iniziare immediatamente a inviare le caratteristiche del comportamento dell'utente a BioCatch dal dispositivo client, usando l'ID sessione client del passaggio 1a.    |
|2     |  L'utente tenta di eseguire l'iscrizione/accesso e viene reindirizzato a Azure AD B2C.      |
|3a    | Parte del userjourney è un provider di attestazioni autoasserte, che accetta l'ID sessione client come input. Questo campo è nascosto sullo schermo. È possibile usare JavaScript per immettere l'ID sessione nel campo . Selezionare il *pulsante* Avanti per continuare il processo di iscrizione/accesso.|
|3b     | L'ID sessione client viene inviato alla piattaforma BioCatch per determinare un punteggio di rischio. |
|3c     | BioCatch restituisce informazioni sulla sessione, ad esempio il punteggio di rischio, e una raccomandazione sulle attività da eseguire: consentire o bloccare |
|3d    |Userjourney ha un passaggio di controllo condizionale, che agisce sulle attestazioni restituite|
| 4   | In base al risultato del controllo condizionale, viene richiamata un'azione come *l'autenticazione* a più fattori dettagliata|
|5    | In qualsiasi momento dal momento in cui l'utente raggiunge per la prima volta la pagina del servizio Web, il servizio Web può usare l'ID sessione client per eseguire query sull'API BioCatch per determinare il punteggio di rischio e le informazioni di sessione in tempo reale. |

## <a name="onboard-with-biocatch"></a>Onboard con BioCatch

Contattare [BioCatch e](https://www.biocatch.com/contact-us) creare un account.

## <a name="configure-the-custom-ui"></a>Configurare l'interfaccia utente personalizzata

È consigliabile nascondere il campo dell'ID sessione client. Usare CSS, JavaScript o qualsiasi altro metodo per nascondere il campo. A scopo di test, è possibile scoprire il campo. JavaScript, ad esempio, viene usato per nascondere il campo di input come segue:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Configurare Azure AD B2C Identity Experience Framework criteri

1. Configurare la configurazione [iniziale dei criteri personalizzati.](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

2. Creare un nuovo file che eredita dal file delle estensioni.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Creare un riferimento all'interfaccia utente personalizzata per nascondere la casella di input, nella risorsa BuildingBlocks.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Aggiungere le attestazioni seguenti nella risorsa BuildingBlocks.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Configurare il provider di attestazioni autoasserte per il campo ID sessione client.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Configurare il provider di attestazioni API REST per BioCatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch fornirà l'URL, l'ID cliente e l'ID utente univoco (uuID) da configurare. L'attestazione SessionID del cliente viene passata come parametro querystring a BioCatch. È possibile scegliere il tipo di attività, ad esempio *MAKE_PAYMENT*.

7. Configurare l'utentejourney; seguire l'esempio

   1. Ottenere clientSessionID come attestazione

   1. Chiamare l'API BioCatch per ottenere le informazioni sulla sessione

   1. Se il rischio di *attestazione restituito* è *basso,* ignorare il passaggio per MFA, altrimenti forzare l'autenticazione a più fattori dell'utente

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Configurare in relying party configurazione (facoltativo)

    È utile passare le informazioni restituite da BioCatch all'applicazione come attestazioni nel token, in particolare *risklevel* e *punteggio*.

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

Seguire questa procedura per aggiungere i file dei criteri a Azure AD B2C

1. Accedere al  [**portale di Azure**](https://portal.azure.com/) come amministratore globale del tenant Azure AD B2C servizio.

2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare **Filtro directory e** sottoscrizione nel menu in alto e scegliere la directory che contiene il   tenant.

3. Scegliere **Tutti i** servizi   nell'angolo superiore sinistro del portale di Azure, cercare e selezionare Azure AD B2C.

4. Passare a **Azure AD B2C**   >  **Identity Experience Framework**

3. Caricare tutti i file dei criteri nel tenant.

## <a name="test-the-solution"></a>Testare la soluzione

1. [Registrare un'applicazione fittizia, che reindirizza a JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. Nella **Identity Experience Framework** selezionare il criterio creato

3. Nella finestra dei criteri selezionare l'applicazione JWT.MS fittizia e selezionare **Esegui ora**

4. Eseguire il flusso di iscrizione e creare un account. Il token restituito JWT.MS deve avere attestazioni 2x per riskLevel e score. Seguire l'esempio.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Risorse aggiuntive

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
