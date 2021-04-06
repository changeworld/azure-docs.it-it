---
title: Personalizzazione della lingua in Azure AD B2C
description: Informazioni su come personalizzare l'esperienza del linguaggio nei flussi utente in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686411"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalizzazione della lingua in Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La personalizzazione della lingua in Azure Active Directory B2C (Azure AD B2C) consente al flusso utente di modificare la lingua in base alle esigenze dei clienti. Microsoft fornisce le traduzioni per [36 lingue](#supported-languages), ma è anche possibile inserire le proprie traduzioni per qualsiasi lingua. Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare qualsiasi testo nelle pagine.

## <a name="how-language-customization-works"></a>Funzionamento della personalizzazione della lingua

La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il flusso utente. Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, `ui_locales`, dall'applicazione. Quando si esegue la chiamata ad Azure AD B2C, la pagina viene tradotta nelle impostazioni locali indicate. Questo tipo di configurazione garantisce il controllo completo sulle lingue del flusso utente e ignora le impostazioni della lingua del browser del cliente.

Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente. Se non si specifica un parametro `ui_locales`, l'esperienza del cliente è determinata dalle impostazioni del browser. È comunque possibile controllare le lingue in cui il flusso utente è tradotto aggiungendole come lingue supportate. Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come predefinita nelle impostazioni cultura supportate.

* **UI-impostazioni locali lingua specificata**: dopo aver abilitato la personalizzazione della lingua, il flusso utente viene convertito nella lingua specificata qui.
* **Lingua richiesta dal browser**: se non `ui_locales` è stato specificato alcun parametro, il flusso utente viene convertito nella lingua richiesta dal browser, *se la lingua è supportata*.
* **Lingua predefinita dei criteri**: se il browser non specifica una lingua o ne specifica una non supportata, il flusso utente viene convertito nella lingua predefinita del flusso utente.

> [!NOTE]
> Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per altre informazioni, vedere [Personalizzazione delle stringhe](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

Per la localizzazione sono necessari tre passaggi: 

1. Configurare l'elenco esplicito delle lingue supportate
1. Fornire stringhe e raccolte specifiche della lingua
1. Modificare la [definizione del contenuto](contentdefinitions.md) per la pagina. 

::: zone-end 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Supporto delle lingue richieste per ui_locales

Per i criteri creati prima della disponibilità generale della personalizzazione della lingua occorre prima abilitare questa funzionalità. Per i criteri e i flussi utente creati successivamente la personalizzazione della lingua è abilitata per impostazione predefinita.

Quando si abilita la personalizzazione della lingua in un flusso utente, è possibile controllare la lingua del flusso utente aggiungendo il parametro `ui_locales`.

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente da abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare **Abilita personalizzazione della lingua**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selezionare le lingue abilitate per il flusso utente

Abilitare un set di lingue in cui tradurre il flusso utente quando richiesto dal browser senza il parametro `ui_locales`.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare una lingua che si vuole supportare.
1. Nel riquadro delle proprietà impostare **Attivato** su **Sì**.
1. Scegliere **Salva** nella parte superiore del riquadro delle proprietà.

>[!NOTE]
>Se non viene specificato un parametro `ui_locales`, la pagina viene tradotta nella lingua del browser del cliente solo se è abilitata.
>

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe

La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel flusso utente.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare la lingua che si vuole personalizzare.
1. In **File di risorse a livello di pagina** selezionare la pagina che si vuole modificare.
1. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua).

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="change-any-string-on-the-page"></a>Modificare stringhe nella pagina

1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
1. Trovare l'elemento da modificare. È possibile trovare il valore `StringId` della stringa che si sta cercando o cercare l'attributo `Value` che si vuole modificare.
1. Aggiornare l'attributo `Value` con i dati da visualizzare.
1. Per ogni stringa che si vuole modificare impostare `Override` su `true`.
1. Salvare il file e caricare le modifiche. Il controllo di caricamento si trova nella stessa posizione in cui è stato scaricato il file JSON.

> [!IMPORTANT]
> Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`. Se il valore non viene modificato, la voce viene ignorata.

### <a name="change-extension-attributes"></a>Cambiare gli attributi di estensione

Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Sostituire `<ExtensionAttribute>` con il nome dell'attributo utente personalizzato.

Sostituire `<ExtensionAttributeValue>` con la nuova stringa da visualizzare.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornire un elenco di valori usando LocalizedCollections

Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un attributo `LocalizedCollections`. `LocalizedCollections` è una matrice di coppie `Name`-`Value`. L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati. Per aggiungere `LocalizedCollections`, usare il formato seguente:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche

1. Dopo aver completato le modifiche al file JSON, tornare al tenant B2C.
1. Selezionare **Flussi utente** e fare clic sul flusso utente che si vuole abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare la lingua in cui si vuole tradurre.
1. Selezionare la pagina per cui si vogliono fornire traduzioni.
1. Selezionare l'icona della cartella e selezionare il file JSON da caricare.

Le modifiche vengono salvate automaticamente nel flusso utente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizzare l'interfaccia utente della pagina usando la personalizzazione della lingua

Esistono due modi per localizzare il [contenuto HTML](customize-ui-with-html.md). Un modo consiste nell'attivare la [personalizzazione della lingua](language-customization.md). L'abilitazione di questa funzionalità consente Azure AD B2C di inviare il parametro OpenID Connect, `ui-locales` , all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

In alternativa, è possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS è possibile configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta. Ad esempio, supponiamo che questo sia l'URI della pagina personalizzata:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

È possibile caricare la pagina in `fr`. Quando la pagina esegue il pull del contenuto HTML e CSS, lo fa da:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aggiungere lingue personalizzate

È anche possibile aggiungere per cui attualmente Microsoft non fornisce traduzioni. Sarà necessario fornire le traduzioni per tutte le stringhe contenute nel flusso utente. I codici della lingua e delle impostazioni internazionali sono limitati a quelli nello standard ISO 639-1. Il formato del codice delle impostazioni locali dovrebbe essere "ISO_639-1_code"-"CountryCode", ad esempio `en-GB` . Per ulteriori informazioni sui formati di ID delle impostazioni locali, consultare https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
2. Fare clic sul flusso utente in cui si vuole aggiungere le lingue personalizzate e quindi fare clic su **Lingue**.
3. Selezionare **Aggiungi lingua personalizzata** nella parte superiore della pagina.
4. Nel riquadro del contesto che viene visualizzato identificare la lingua per cui si stanno inserendo le traduzioni immettendo un codice delle impostazioni locali valido.
5. Per ogni pagina è possibile scaricare un set di sostituzioni per la lingua inglese e lavorare sulle traduzioni.
6. Una volta terminato con i file JSON, è possibile caricarli per ogni pagina.
7. Selezionare **Abilita**. Il flusso utente può ora visualizzare la lingua per gli utenti.
8. Salvare la lingua.

>[!IMPORTANT]
>È necessario abilitare le lingue personalizzate o caricare gli override per la lingua prima di poterla salvare.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Configurare l'elenco delle lingue supportate

Aprire il file delle estensioni del criterio, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'elemento `Localization` con le lingue supportate: Inglese (impostazione predefinita) e spagnolo.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Fornire le etichette specifiche della lingua

L'elemento [LocalizedResources](localization.md#localizedresources) dell'elemento `Localization` contiene l'elenco delle stringhe localizzate. L'elemento delle risorse localizzate dispone di un identificatore usato per identificare in modo univoco le risorse localizzate. Questo identificatore viene usato in un secondo momento nell'elemento di [definizione del contenuto](contentdefinitions.md).

Gli elementi delle risorse localizzate vengono configurati per la definizione del contenuto e per tutte le lingue che si intende supportare. Per personalizzare le pagine di iscrizione o di accesso unificate per l'inglese e lo spagnolo, aggiungere gli elementi `LocalizedResources` seguenti dopo la chiusura dell'elemento `</SupportedLanguages>`.

> [!NOTE]
> Nell'esempio seguente è stato aggiunto il simbolo di cancelletto `#` all'inizio di ogni riga, in modo che sia possibile trovare facilmente le etichette localizzate sullo schermo.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Modificare la definizione del contenuto con la localizzazione

Incollare l'intero contenuto dell'elemento ContentDefinitions copiato come figlio dell'elemento BuildingBlocks.

Nell'esempio seguente le stringhe personalizzate in inglese (en) e spagnolo (es) vengono aggiunte alla pagina di iscrizione o di accesso e alla pagina di iscrizione dell'account locale. Il **LocalizedResourcesReferenceId** per ogni **LocalizedResourcesReference** è lo stesso delle impostazioni locali, ma è possibile usare qualsiasi stringa come identificatore. Per ogni combinazione di lingua e di pagina, si sceglie il corrispondente elemento **LocalizedResources** creato in precedenza.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Caricare ed eseguire il test del criterio personalizzato aggiornato

### <a name="upload-the-custom-policy"></a>Caricare il criterio personalizzati

1. Salvare il file delle estensioni.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Cercare e selezionare **Azure Active Directory B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **Carica criteri personalizzati**.
1. Caricare il file delle estensioni modificato in precedenza.

### <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite **Esegui adesso**

1. Selezionare il criterio caricato, quindi selezionare **Esegui adesso**.
1. Dovrebbe essere possibile visualizzare la pagina di iscrizione o di accesso localizzata.
1. Fare clic sul collegamento per l'iscrizione: dovrebbe essere possibile visualizzare la pagina di iscrizione localizzata.
1. Impostare la lingua predefinita del browser sullo spagnolo. In alternativa, è possibile aggiungere il parametro della stringa di query `ui_locales` alla richiesta di autorizzazione. Ad esempio: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Informazioni aggiuntive

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Etichette di personalizzazione dell'interfaccia utente della pagina come sostituzioni

Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rese persistenti in un file JSON per la lingua Inglese (en). È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.

::: zone-end

### <a name="up-to-date-translations"></a>Traduzioni aggiornate

Microsoft si impegna a fornire le traduzioni più aggiornate. Continuerà a migliorare le traduzioni e a garantirne la conformità. Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il flusso utente.

### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra

Microsoft non fornisce attualmente supporto per le lingue da destra a sinistra. A questo scopo, è possibile usare impostazioni locali personalizzate e modificare la modalità di visualizzazione delle stringhe tramite CSS. Se si ha bisogno di questa funzionalità, votarla in [Commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network

Microsoft fornisce il parametro OIDC `ui_locales` per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google.

### <a name="browser-behavior"></a>Comportamento dei browser

Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita. Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.

## <a name="supported-languages"></a>Lingue supportate

Azure AD B2C include il supporto per le lingue seguenti. Le lingue dei flussi utente sono fornite da Azure AD B2C. I linguaggi di notifica dell'autenticazione a più fattori (multi-factor authentication) sono forniti da [Azure ad](../active-directory/authentication/concept-mfa-howitworks.md)autenticazione a più fattori.

| Linguaggio              | Codice lingua | Flussi degli utenti         | Notifiche MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabo                | ar            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Bulgaro             | bg            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Bengalese                | bn            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Catalano               | ca            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Ceco                 | cs            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Danese                | da            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Tedesco                | de            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Greco                 | el            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Inglese               | en            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Spagnolo               | es            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Estone              | et            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Finlandese               | fi            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Francese                | fr            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Galiziano              | gl            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Ebraico                | he            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Croato              | hr            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Ungherese             | hu            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Indonesiano            | id            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Italiano               | it            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Giapponese              | ja            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Kazako                | kk            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Lettone               | lv            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Marathi               | mr            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Malese                 | ms            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Norvegese Bokmål      | nb            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Olandese                 | nl            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Norvegese             | no            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Polacco                | pl            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Portoghese (Brasile)   | pt-br         | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Portoghese (Portogallo) | pt-pt         | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Rumeno              | ro            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Slovacco                | sk            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Sloveno             | sl            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Serbo - Alfabeto cirillico    | sr-cryl-cs    | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Serbo - Alfabeto latino       | sr-latn-cs    | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Svedese               | sv            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Tamil                 | ta            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![X che indica No.](./media/user-flow-language-customization/no.png) |
| Thai                  | th            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Ucraino             | uk            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![X che indica No.](./media/user-flow-language-customization/no.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Cinese (semplificato)  | zh-hans       | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |
| Cinese (tradizionale) | zh-hant       | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) | ![Segno di spunta verde.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Passaggi successivi

::: zone pivot="b2c-user-flow"

Per altre informazioni su come personalizzare l'interfaccia utente delle applicazioni, vedere [personalizzare l'interfaccia utente dell'applicazione in Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Altre informazioni sull'elemento [localizzazione](localization.md) nella documentazione di riferimento di Identity Experience Framework.
- Vedere l'elenco degli [ID stringa di localizzazione](localization-string-ids.md) disponibili in Azure AD B2C.

::: zone-end 
