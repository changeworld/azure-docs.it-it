---
title: Incorporare Azure Active Directory B2C interfaccia utente nell'app con criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come incorporare Azure Active Directory B2C interfaccia utente nell'app con criteri personalizzati
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1255c4962de1fce19efa9c0b0e1d28fc348463ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580148"
---
# <a name="embedded-sign-in-experience"></a>Esperienza di accesso incorporata

Per un'esperienza di accesso più semplice, è possibile evitare il reindirizzamento degli utenti a una pagina di accesso separata o la generazione di una finestra popup. Usando l'elemento inline frame `<iframe>` , è possibile incorporare l'interfaccia utente di accesso Azure ad B2C direttamente nell'applicazione Web.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Accesso incorporato all'applicazione Web

L'elemento inline frame `<iframe>` viene usato per incorporare un documento in una pagina Web HTML5. È possibile usare l'elemento iframe per incorporare l'interfaccia utente di accesso Azure AD B2C direttamente nell'applicazione Web, come illustrato nell'esempio seguente:

![Accedi con l'esperienza DIV al passaggio del mouse](media/embedded-login/login-hovering.png)

Quando si usa iframe, tenere presente quanto segue:

- L'accesso incorporato supporta solo gli account locali. La maggior parte dei provider di identità di social networking (ad esempio Google e Facebook) blocca il rendering delle pagine di accesso in frame inline.
- Poiché Azure AD B2C cookie di sessione all'interno di un IFRAME sono considerati cookie di terze parti, alcuni browser (ad esempio Safari o Chrome in modalità Incognito) bloccano o cancellano questi cookie, causando un'esperienza utente indesiderata. Per evitare questo problema, assicurarsi che il nome di dominio dell'applicazione e il dominio di Azure AD B2C abbiano la *stessa origine*. Per usare la stessa origine, [abilitare i domini personalizzati](custom-domain.md) per Azure ad B2C tenant, quindi configurare l'app Web con la stessa origine. Ad esempio, un'applicazione ospitata in https://app.contoso.com ha la stessa origine del Azure ad B2C in esecuzione in https://login.contoso.com .

## <a name="prerequisites"></a>Prerequisiti

* Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
* [Abilitare domini personalizzati](custom-domain.md) per i criteri.

## <a name="configure-your-policy"></a>Configurare i criteri

Per consentire l'incorporamento dell'interfaccia utente Azure AD B2C in un iframe, è necessario includere un criterio di sicurezza `Content-Security-Policy` del contenuto e `X-Frame-Options` le opzioni di frame nelle intestazioni di risposta http Azure ad B2C. Queste intestazioni consentono l'esecuzione dell'interfaccia utente Azure AD B2C con il nome di dominio dell'applicazione.

Aggiungere un elemento **JourneyFraming** all'interno dell'elemento [RelyingParty](relyingparty.md) .  L'elemento **UserJourneyBehaviors** deve seguire **DefaultUserJourney**. L'elemento **UserJourneyBehaviors** dovrebbe essere simile all'esempio seguente:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

L'attributo **Sources** contiene l'URI dell'applicazione Web. Aggiungere uno spazio tra gli URI. Ogni URI deve soddisfare i requisiti seguenti:

- L'URI deve essere attendibile e di proprietà dell'applicazione.
- L'URI deve utilizzare lo schema HTTPS.  
- È necessario specificare l'URI completo dell'app Web. I caratteri jolly non sono supportati.

Inoltre, è consigliabile bloccare il nome di dominio da incorporare in un iframe impostando rispettivamente le intestazioni Content-Security-Policy e X-frame-options nelle pagine dell'applicazione. In questo modo si mitigano i problemi di sicurezza relativi a browser meno recenti correlati all'incorporamento annidato di iframe.

## <a name="adjust-policy-user-interface"></a>Modificare l'interfaccia utente dei criteri

Grazie alla [personalizzazione dell'interfaccia utente](customize-ui.md)di Azure ad B2C, è possibile avere un controllo quasi completo sui contenuti HTML e CSS presentati agli utenti. Seguire i passaggi per personalizzare una pagina HTML usando le definizioni di contenuto. Per adattare l'interfaccia utente Azure AD B2C alla dimensione iframe, fornire una pagina HTML pulita senza spazi di sfondo e aggiuntivi.  

Il codice CSS seguente nasconde gli elementi HTML Azure AD B2C e regola le dimensioni del pannello per riempire l'iframe.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

In alcuni casi, potrebbe essere necessario notificare all'applicazione la quale è attualmente visualizzata la pagina Azure AD B2C. Ad esempio, quando un utente seleziona l'opzione di iscrizione, potrebbe essere necessario che l'applicazione risponda nascondendo i collegamenti per l'accesso con un account di social networking o la modifica delle dimensioni di iframe.

Per inviare una notifica all'applicazione della pagina Azure AD B2C corrente, [abilitare i criteri per JavaScript](./javascript-and-page-layout.md)e quindi usare i messaggi di post HTML5. Il codice JavaScript seguente invia un messaggio post all'app con `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Configurare un'applicazione Web

Quando un utente seleziona il pulsante di accesso, l' [app Web](code-samples.md#web-apps-and-apis) genera una richiesta di autorizzazione che consente all'utente di Azure ad B2C esperienza di accesso. Al termine dell'accesso, Azure AD B2C restituisce un token ID o un codice di autorizzazione all'URI di reindirizzamento configurato all'interno dell'applicazione.

Per supportare l'accesso incorporato, la proprietà **src** di iframe punta al controller di accesso, ad esempio `/account/SignUpSignIn` , che genera la richiesta di autorizzazione e reindirizza l'utente a Azure ad B2C criterio.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Dopo che il token ID viene ricevuto e convalidato dall'applicazione, il flusso di autorizzazione è completo e l'applicazione riconosce e considera attendibile l'utente. Poiché il flusso di autorizzazione si verifica all'interno dell'iframe, è necessario ricaricare la pagina principale. Dopo il ricaricamento della pagina, il pulsante di accesso viene modificato in "Sign out" e il nome utente viene visualizzato nell'interfaccia utente.  

Di seguito è riportato un esempio che illustra come l'URI di reindirizzamento di accesso può aggiornare la pagina principale:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Aggiungere l'accesso con account di social networking a un'app Web

I provider di identità di social networking bloccano le pagine di accesso dal rendering in frame inline. È possibile usare un criterio separato per gli account di social networking oppure è possibile usare un singolo criterio per l'accesso e l'iscrizione con account locali e di social networking. Quindi, è possibile usare il `domain_hint` parametro della stringa di query. Il parametro del suggerimento di dominio porta l'utente direttamente alla pagina di accesso del provider di identità di social networking.

Nell'applicazione aggiungere i pulsanti Accedi con account di social networking. Quando un utente fa clic su uno dei pulsanti dell'account di social networking, il controllo deve modificare il nome del criterio o impostare il parametro dell'hint di dominio.

<!-- TBD: add a diagram -->

L'URI di reindirizzamento può essere lo stesso URI di reindirizzamento usato dall'iframe. È possibile ignorare il ricaricamento della pagina.

## <a name="configure-a-single-page-application"></a>Configurare un'applicazione a pagina singola

Per un'applicazione a singola pagina, è necessario anche una seconda pagina HTML di "accesso" che viene caricata nell'iframe. Questa pagina di accesso ospita il codice della libreria di autenticazione che genera il codice di autorizzazione e restituisce il token.

Quando l'applicazione a pagina singola richiede il token di accesso, usare il codice JavaScript per ottenere il token di accesso dall'iframe e dall'oggetto che lo contiene.

> [!NOTE]
> L'esecuzione di MSAL 2,0 in un iframe non è attualmente supportata.

Il codice seguente è un esempio eseguito nella pagina principale e chiama il codice JavaScript di un iframe:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli correlati seguenti:

- [Personalizzazione dell'interfaccia utente](customize-ui.md)
- Riferimento all'elemento [RelyingParty](relyingparty.md)
- [Abilitare i criteri per JavaScript](./javascript-and-page-layout.md)
- [Esempi di codice](code-samples.md)