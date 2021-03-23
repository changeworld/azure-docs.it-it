---
title: Versioni del layout di pagina e JavaScript
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare JavaScript e usare le versioni del layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781365"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versioni del layout di pagina e JavaScript in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C fornisce un set di contenuto in pacchetto contenente HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei flussi utente e nei criteri personalizzati. Per abilitare JavaScript per le applicazioni:

::: zone pivot="b2c-user-flow"

* Selezionare un [layout di pagina](page-layout.md)
* Abilitarlo nel flusso utente usando il portale di Azure
* Usare [b2clogin.com](b2clogin.md) nelle richieste

::: zone-end

::: zone pivot="b2c-custom-policy"

* Selezionare un [layout di pagina](page-layout.md)
* Aggiungere un elemento al [criterio personalizzato](custom-policy-overview.md)
* Usare [b2clogin.com](b2clogin.md) nelle richieste

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Selezionare una versione del layout di pagina

Se si intende abilitare il codice JavaScript lato client, gli elementi su cui si basa il codice JavaScript non devono essere modificabili. Se non sono immutabili, le eventuali modifiche potrebbero provocare comportamenti imprevisti nelle pagine utente. Per evitare questi problemi, imporre l'uso di un layout di pagina e specificare una versione del layout di pagina per assicurarsi che le definizioni di contenuto su cui si basa JavaScript siano non modificabili. Anche se non si intende abilitare JavaScript, è possibile specificare una versione del layout di pagina per le pagine.

::: zone pivot="b2c-user-flow"

Per specificare una versione del layout di pagina per le pagine del flusso utente: 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Selezionare **layout di pagina**. Scegliere un **nome di layout**, quindi scegliere la **versione del layout di pagina (anteprima)**.

Per informazioni sulle diverse versioni del layout di pagina, vedere il [log delle modifiche della versione del layout di pagina](page-layout.md).

![Impostazioni del layout di pagina nel portale che mostra il menu a discesa Versione layout pagina](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Selezionare un [layout di pagina](contentdefinitions.md#select-a-page-layout) per gli elementi dell'interfaccia utente dell'applicazione.

Definire una [versione del layout di pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina `contract` per *tutte* le definizioni di contenuto nel criterio personalizzato. Il formato del valore deve contenere la parola `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**Contract**:p Age-Name: Version_. Informazioni su come eseguire la [migrazione al layout di pagina](contentdefinitions.md#migrating-to-page-layout) con la versione della pagina.

Nell'esempio seguente vengono illustrati gli identificatori delle definizioni di contenuto e il corrispondente **DataUri** con il contratto di pagina: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Abilitare JavaScript

::: zone pivot="b2c-user-flow"

Nelle **Proprietà** del flusso utente è possibile abilitare JavaScript. L'abilitazione di JavaScript impone anche l'uso di un layout di pagina. È quindi possibile impostare la versione del layout di pagina per il flusso utente, come descritto nella sezione successiva.

![Pagina delle proprietà del flusso utente con l'impostazione Abilita JavaScript evidenziata](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Per abilitare l'esecuzione di script, aggiungere l'elemento **ScriptExecution** all'elemento [RelyingParty](relyingparty.md).

1. Aprire il file dei criteri personalizzati. Ad esempio, *SignUpOrSignin.xml*.
1. Aggiungere l'elemento **ScriptExecution** all'elemento **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Salvare e caricare il file.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Linee guida per l'uso di JavaScript

Seguire queste linee guida per personalizzare l'interfaccia dell'applicazione tramite JavaScript:

- Non associare un evento clic in elementi HTML `<a>`.
- Non definire una dipendenza dal codice o da commenti di Azure AD B2C.
- Non modificare l'ordine o la gerarchia di elementi HTML di Azure AD B2C. Usare un criterio di Azure AD B2C per controllare l'ordine degli elementi dell'interfaccia utente.
- È possibile chiamare qualsiasi servizio RESTful tenendo presenti queste considerazioni:
    - Potrebbe essere necessario impostare CORS del servizio RESTful in modo da consentire le chiamate HTTP lato client.
    - Assicurarsi che il servizio RESTful sia sicuro e usi solo il protocollo HTTPS.
    - Non usare JavaScript direttamente per chiamare endpoint di Azure AD B2C.
- È possibile incorporare JavaScript o è possibile eseguire il collegamento a file JavaScript esterni. Quando si usa un file JavaScript esterno, assicurarsi di usare l'URL assoluto e non un URL relativo.
- Framework JavaScript:
    - Azure AD B2C usa una [versione specifica di jQuery](page-layout.md#jquery-version). Non includere un'altra versione di jQuery. L'uso di più versioni nella stessa pagina può provocare problemi.
    - L'uso di RequireJS non è supportato.
    - La maggior parte dei framework JavaScript non è supportata da Azure AD B2C.
- Le impostazioni di Azure AD B2C possono essere lette chiamando oggetti `window.SETTINGS` e `window.CONTENT`, ad esempio la lingua dell'interfaccia utente corrente. Non modificare il valore di questi oggetti.
- Per personalizzare il messaggio di errore di Azure AD B2C, usare la localizzazione in un criterio.
- Se è possibile ottenere il risultato desiderato usando un criterio, questo è in genere l'approccio consigliato.

## <a name="javascript-samples"></a>Esempi JavaScript

### <a name="show-or-hide-a-password"></a>Mostrare o nascondere una password

Un modo comune per aiutare i clienti a effettuare correttamente l'iscrizione consiste nel permettere loro di visualizzare la password immessa. Questa opzione permette agli utenti di effettuare l'iscrizione consentendo loro di visualizzare e correggere la password in tutta semplicità, se necessario. Qualsiasi campo per la digitazione della password contiene una casella di controllo con etichetta **Mostra password**.  Questa casella di controllo permette agli utenti di visualizzare la password in testo normale. Includere questo frammento di codice nel modello di iscrizione o di accesso per una pagina autocertificata:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Includere il codice seguente nella pagina in cui si vuole aggiungere una casella di controllo **Condizioni per l'utilizzo**. Questa casella di controllo è in genere necessaria nelle pagine di iscrizione dell'account locale e di iscrizione dell'account di social networking.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Nel codice sostituire `termsOfUseUrl` con il collegamento alle condizioni per l'utilizzo. Per la directory, creare un nuovo attributo utente denominato **termsOfUse** , quindi includere **termsOfUse** come attributo utente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come personalizzare l'interfaccia utente delle applicazioni, vedere [personalizzare l'interfaccia utente dell'applicazione in Azure Active Directory B2C](customize-ui-with-html.md).
