---
title: Plug-in Microsoft Enterprise SSO per dispositivi Apple
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sul plug-in Azure Active Directory SSO di Microsoft per dispositivi iOS, iPados e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427097"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)

>[!IMPORTANT]
> Questa funzionalità [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Il *plug-in Microsoft Enterprise SSO per i dispositivi Apple* fornisce Single Sign-on (SSO) per gli account Azure Active Directory (Azure ad) in MacOS, iOS e ipados in tutte le applicazioni che supportano la funzionalità di [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) di Apple. Sono incluse le applicazioni meno recenti che possono dipendere dall'azienda ma che non supportano ancora le librerie di identità o i protocolli più recenti. Microsoft ha collaborato con Apple per sviluppare questo plug-in per aumentare l'usabilità dell'applicazione garantendo al contempo la migliore protezione fornita da Apple e da Microsoft.

Il plug-in Enterprise SSO è attualmente disponibile come funzionalità incorporata delle app seguenti:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) -iOS, ipados
* Microsoft Intune [portale aziendale](/mem/intune/apps/apps-company-portal-macos) -MacOS

## <a name="features"></a>Funzionalità

Il plug-in Microsoft Enterprise SSO per i dispositivi Apple offre i vantaggi seguenti:

- Fornisce SSO per gli account Azure AD in tutte le applicazioni che supportano la funzionalità Enterprise Single Sign-On di Apple.
- Può essere abilitato da qualsiasi soluzione di gestione di dispositivi mobili (MDM).
- Estende l'accesso SSO alle applicazioni che non usano ancora le librerie della piattaforma di identità Microsoft.
- Estende l'accesso Single Sign-on alle applicazioni che usano OAuth2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisiti

Per usare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- Il dispositivo deve **supportare** e avere un'app che include il plug-in Microsoft Enterprise SSO per i dispositivi Apple **installati**:
  - iOS 13.0 +: [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPados 13.0 +: [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [app portale aziendale Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Il dispositivo deve essere **registrato con MDM** , ad esempio con Microsoft Intune.
- Per abilitare il plug-in Enterprise SSO nel dispositivo, è necessario effettuare il push della configurazione **al dispositivo** . Questo vincolo di sicurezza è richiesto da Apple.

### <a name="ios-requirements"></a>requisiti di iOS:
- iOS 13,0 o versione successiva deve essere installato nel dispositivo.
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni sono l' [app Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>requisiti macOS:
- macOS 10,15 o versione successiva deve essere installato nel dispositivo. 
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni includono l' [app portale aziendale Intune](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Abilitare il plug-in SSO con la gestione dei dispositivi mobili (MDM)

### <a name="microsoft-intune-configuration"></a>Configurazione di Microsoft Intune

Se si usa Microsoft Intune come servizio MDM, è possibile usare le impostazioni predefinite del profilo di configurazione per abilitare il plug-in Microsoft Enterprise SSO.

Configurare prima di tutto le impostazioni dell' [estensione dell'app Single Sign-on](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) di un profilo di configurazione e [assegnare il profilo a un utente o a un gruppo di dispositivi](/mem/intune/configuration/device-profile-assign) , se non è già stato assegnato.

Le impostazioni del profilo che abilitano il plug-in SSO vengono applicate automaticamente ai dispositivi del gruppo la volta successiva che ogni dispositivo archivia con Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configurazione manuale per altri servizi MDM

Se non si usa Microsoft Intune per la gestione dei dispositivi mobili, usare i parametri seguenti per configurare il plug-in Microsoft Enterprise SSO per i dispositivi Apple.

#### <a name="ios-settings"></a>Impostazioni iOS:

- **ID estensione**: `com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: (questo campo non è necessario per iOS)

#### <a name="macos-settings"></a>Impostazioni macOS:

- **ID estensione**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID team**: `UBF8T346G9`

#### <a name="common-settings"></a>Impostazioni comuni:

- **Tipo**: Reindirizzamento
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Opzioni di configurazione aggiuntive
È possibile aggiungere altre opzioni di configurazione per estendere la funzionalità SSO ad altre app.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Abilitare l'accesso SSO per le app che non usano una libreria della piattaforma di identità Microsoft

Il plug-in SSO consente a qualsiasi applicazione di partecipare Single Sign-On anche se non è stato sviluppato utilizzando un SDK Microsoft come Microsoft Authentication Library (MSAL).

Il plug-in SSO viene installato automaticamente dai dispositivi che hanno scaricato l'app Microsoft Authenticator in iOS e iPados o Portale aziendale Intune app in macOS e hanno registrato il proprio dispositivo con l'organizzazione. È probabile che l'organizzazione usi attualmente l'app Authenticator per scenari come l'autenticazione a più fattori, l'autenticazione senza password e l'accesso condizionale. Può essere attivata per le applicazioni usando qualsiasi provider MDM, sebbene Microsoft abbia semplificato la configurazione all'interno di Microsoft Endpoint Manager di Intune. Per configurare queste applicazioni per l'uso del plug-in SSO, viene usato un elenco Consenti.

>[!IMPORTANT]
> Sono supportate solo le app che usano tecnologie di rete Apple native o WebView. Se un'applicazione fornisce un'implementazione del proprio livello di rete, il plug-in Microsoft Enterprise SSO non è supportato.  

Usare i parametri seguenti per configurare il plug-in di Microsoft Enterprise SSO per le app che non usano una libreria della piattaforma di identità Microsoft:

Se si vuole fornire un elenco di app specifiche:

- **Chiave**: `AppAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di ID bundle applicazione per le applicazioni che possono partecipare all'accesso SSO
- **Esempio**: `com.contoso.workapp, com.contoso.travelapp`

In alternativa, se si desidera fornire un elenco di prefissi:
- **Chiave**: `AppPrefixAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di prefissi ID bundle applicazione per le applicazioni che possono partecipare all'accesso SSO. Si noti che questa operazione consentirà a tutte le app che iniziano con un prefisso specifico di partecipare all'accesso SSO
- **Esempio**: `com.contoso., com.fabrikam.`

Le [app](./application-consent-experience.md) consentite che sono consentite dall'amministratore MDM per partecipare all'accesso SSO possono ottenere automaticamente un token per l'utente finale. Pertanto, è importante aggiungere solo applicazioni attendibili all'elenco Consenti. 

>[!NOTE]
> Non è necessario aggiungere a questo elenco le applicazioni che usano MSAL o ASWebAuthenticationSession. Queste applicazioni sono abilitate per impostazione predefinita. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Come individuare gli identificatori del bundle dell'app nei dispositivi iOS

Apple non fornisce un modo semplice per individuare gli ID bundle dall'App Store. Il modo più semplice per individuare gli ID bundle delle app che vogliono usare per SSO è chiedere al fornitore o allo sviluppatore di app. Se questa opzione non è disponibile, è possibile usare la configurazione MDM per individuare gli ID bundle. 

Abilitare temporaneamente il flag seguente nella configurazione MDM:

- **Chiave**: `admin_debug_mode_enabled`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

Quando questo flag si trova all'accesso alle app iOS nel dispositivo per cui si vuole conoscerne l'ID. Aprire quindi Microsoft Authenticator app-> Help-> inviare i log > i log di visualizzazione. 

Nel file di log cercare la riga seguente:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Questa operazione dovrebbe acquisire tutti gli identificatori del bundle di applicazioni visibili all'estensione SSO. È quindi possibile usare tali identificatori per configurare l'accesso SSO per tali app. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Consente all'utente di accedere da applicazioni sconosciute e dal browser Safari.

Per impostazione predefinita, il plug-in Microsoft Enterprise SSO fornisce SSO per le app autorizzate solo quando un utente ha eseguito l'accesso da un'app che usa una libreria della piattaforma di identità Microsoft, ad esempio ADAL o MSAL. Il plug-in Microsoft Enterprise SSO può anche acquisire credenziali condivise quando viene chiamato da un'altra app che usa una libreria della piattaforma di identità Microsoft durante una nuova acquisizione di token.

L'abilitazione `browser_sso_interaction_enabled` del flag Abilita l'app che non usa una libreria della piattaforma di identità Microsoft per eseguire il bootstrap iniziale e ottenere le credenziali condivise. Consente inoltre a Safari browser di eseguire il bootstrap iniziale e ottenere le credenziali condivise. Se il plug-in Microsoft Enterprise SSO non dispone ancora di credenziali condivise, tenterà di ottenerne una ogni volta che viene richiesto un accesso da un URL Azure AD all'interno del browser Safari, ASWebAuthenticationSession, SafariViewController o un'altra applicazione nativa consentita.  

- **Chiave**: `browser_sso_interaction_enabled`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

Per macOS questa impostazione è necessaria per ottenere un'esperienza più coerente in tutte le app. Per iOS e iPados questa impostazione non è necessaria perché la maggior parte delle app usa l'applicazione Microsoft Authenticator per l'accesso. Tuttavia, se si dispone di alcune applicazioni che non usano il Microsoft Authenticator su iOS o iPados, questo flag consente di migliorare l'esperienza, quindi è consigliabile abilitare l'impostazione. Questo protocollo è disabilitato per impostazione predefinita.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Disabilitare la richiesta di autenticazione a più fattori al bootstrap iniziale

Per impostazione predefinita, il plug-in Microsoft Enterprise SSO chiede sempre all'utente di eseguire l'autenticazione a più fattori durante il bootstrap iniziale e di ottenere le credenziali condivise, anche se non è necessario per l'applicazione corrente avviata dall'utente. In questo modo, le credenziali condivise possono essere facilmente usate in tutte le applicazioni aggiuntive senza richiedere conferma all'utente se l'autenticazione a più fattori diventa necessaria in un secondo momento. In questo modo si riduce il tempo necessario all'utente per richiedere il dispositivo ed è in genere una decisione efficace.

L'abilitazione `browser_sso_disable_mfa` di disattiva questa opzione e chiede all'utente solo quando l'autenticazione a più fattori è richiesta da un'applicazione o una risorsa. 

- **Chiave**: `browser_sso_disable_mfa`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

Si consiglia di mantenere questo flag disabilitato in quanto riduce le volte in cui l'utente deve essere richiesto sul dispositivo. Se l'organizzazione usa raramente l'autenticazione a più fattori, potrebbe essere necessario abilitare il flag, ma si consiglia di usare più spesso l'autenticazione a più fattori. Per questo motivo, è disabilitato per impostazione predefinita.

#### <a name="disable-oauth2-application-prompts"></a>Disabilitare i prompt dell'applicazione OAuth2

Il plug-in Microsoft Enterprise SSO fornisce l'accesso SSO aggiungendo le credenziali condivise alle richieste di rete provenienti dalle applicazioni consentite. Tuttavia, alcune applicazioni OAuth2 potrebbero applicare erroneamente le richieste degli utenti finali a livello di protocollo. Se si verifica questa situazione, si noterà che le credenziali condivise vengono ignorate per tali app e all'utente viene richiesto di eseguire l'accesso anche se il plug-in Microsoft Enterprise SSO funziona per altre applicazioni.  

L'abilitazione `disable_explicit_app_prompt` del flag limita la capacità di applicazioni native e Web di forzare un prompt dell'utente finale sul livello del protocollo e di ignorare SSO.

- **Chiave**: `disable_explicit_app_prompt`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

È consigliabile abilitare questo flag per ottenere un'esperienza più coerente in tutte le app. Questo protocollo è disabilitato per impostazione predefinita. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Abilitare l'accesso SSO tramite cookie per un'applicazione specifica

Un numero ridotto di app potrebbe non essere compatibile con l'estensione SSO. In particolare, le app con impostazioni di rete avanzate potrebbero riscontrare problemi imprevisti quando sono abilitate per l'accesso SSO, ad esempio potrebbe essere visualizzato un errore che segnala che la richiesta di rete è stata annullata o interrotta. 

Se si verificano problemi di accesso usando il metodo descritto nella `Enable SSO for apps that don't use MSAL` sezione, è possibile provare la configurazione alternativa per tali app. 

Per configurare il plug-in Microsoft Enterprise SSO per le app specifiche, usare i parametri seguenti:

- **Chiave**: `AppCookieSSOAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di prefissi ID bundle applicazione per le applicazioni che possono partecipare all'accesso SSO. Si noti che questa operazione consentirà a tutte le app che iniziano con un prefisso specifico di partecipare all'accesso SSO
- **Esempio**: `com.contoso.myapp1, com.fabrikam.myapp2`

Si noti che le applicazioni abilitate per l'accesso SSO usando questo meccanismo devono essere aggiunte sia a che a `AppCookieSSOAllowList` `AppPrefixAllowList` .

Si consiglia di provare questa opzione solo per le applicazioni in cui si verificano errori di accesso imprevisti. 

#### <a name="use-intune-for-simplified-configuration"></a>Usare Intune per la configurazione semplificata

Come indicato in precedenza, è possibile usare Microsoft Intune come servizio MDM per semplificare la configurazione del plug-in Microsoft Enterprise SSO, tra cui l'abilitazione del plug-in e l'aggiunta di app precedenti a un elenco consentiti in modo che ottengano SSO. Per ulteriori informazioni, vedere la [documentazione di configurazione di Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Uso del plug-in SSO nell'applicazione

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e versioni successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple. È il metodo consigliato per aggiungere il supporto per il plug-in Microsoft Enterprise SSO e consente di ottenere le funzionalità complete della piattaforma di identità Microsoft.

Se si sta creando un'applicazione per gli scenari di lavoro Frontline, vedere [modalità dispositivo condiviso per i dispositivi iOS](msal-ios-shared-devices.md) per ulteriori impostazioni della funzionalità.

## <a name="how-the-sso-plug-in-works"></a>Funzionamento del plug-in SSO

Il plug-in Microsoft Enterprise SSO si basa sul [Framework Enterprise Single Sign-On di Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). I provider di identità che vengono caricati nel Framework possono intercettare il traffico di rete per i domini e migliorare o modificare la modalità di gestione di tali richieste. Il plug-in SSO, ad esempio, può visualizzare un'interfaccia utente aggiuntiva per raccogliere le credenziali dell'utente finale in modo sicuro, richiedere l'autenticazione a più fattori o fornire i token all'applicazione in modo invisibile all'utente.

Le applicazioni native possono inoltre implementare operazioni personalizzate e comunicare direttamente con il plug-in SSO.
È possibile ottenere informazioni sul Framework Single Sign-on in questo [video 2019 WWDC di Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Applicazioni che usano una libreria della piattaforma di identità Microsoft

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e versioni successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple in modo nativo per gli account aziendali e dell'Istituto di istruzione. 

Se sono stati seguiti [tutti i passaggi consigliati](./quickstart-v2-ios.md) e si è usato il [formato URI di reindirizzamento](./redirect-uris-ios.md)predefinito, non è necessaria alcuna configurazione speciale. Quando è in esecuzione in un dispositivo in cui è presente il plug-in SSO, MSAL lo richiamerà automaticamente per tutte le richieste di token interattivo e invisibile, nonché per le operazioni di enumerazione degli account e di rimozione dell'account. Poiché MSAL implementa il protocollo plug-in nativo SSO che si basa sulle operazioni personalizzate, questa configurazione offre all'utente finale l'esperienza nativa più semplice. 

Se il plug-in SSO non è abilitato da MDM, ma l'app Microsoft Authenticator è presente nel dispositivo, MSAL utilizzerà invece l'app Microsoft Authenticator per tutte le richieste di token interattive. Il plug-in SSO condivide l'accesso SSO con l'app Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Applicazioni che non usano una libreria della piattaforma di identità Microsoft

Le applicazioni che non usano una libreria della piattaforma di identità Microsoft come MSAL possono comunque ottenere SSO se un amministratore le aggiunge all'elenco Consenti in modo esplicito. 

Non sono necessarie modifiche al codice in tali app purché siano soddisfatte le condizioni seguenti:

- L'applicazione usa i framework Apple per eseguire richieste di rete (ad esempio, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- L'applicazione usa protocolli standard per comunicare con Azure AD (ad esempio, OAuth2, SAML, WS-Federation)
- L'applicazione non raccoglie il nome utente e la password in testo normale nell'interfaccia utente nativa

In questo caso, SSO viene fornito quando l'applicazione crea una richiesta di rete e apre un Web browser per l'accesso dell'utente. Quando un utente viene reindirizzato a un URL di accesso Azure AD, il plug-in SSO convalida l'URL e controlla se è disponibile una credenziale SSO per tale URL. Se ne esiste uno, il plug-in SSO passa le credenziali SSO a Azure AD, che autorizza l'applicazione a completare la richiesta di rete senza chiedere all'utente di immettere le proprie credenziali. Inoltre, se il dispositivo è noto come Azure AD, il plug-in SSO passa anche il certificato del dispositivo per soddisfare il controllo dell'accesso condizionale basato su dispositivo. 

Per supportare l'accesso SSO per le app non MSAL, il plug-in SSO implementa un protocollo simile al plug-in del browser Windows descritto in [che cos'è un token di aggiornamento primario?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Rispetto alle app basate su MSAL, il plug-in SSO agisce in modo più trasparente per le app non MSAL integrando l'esperienza di accesso del browser esistente fornita dalle app. L'utente finale può vedere la propria esperienza familiare, con il vantaggio di non dover eseguire accessi aggiuntivi in ognuna delle applicazioni. Ad esempio, anziché visualizzare la selezione account nativa, il plug-in SSO aggiunge sessioni SSO all'esperienza di selezione account basata sul Web. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modalità dispositivo condiviso in iOS, vedere [modalità dispositivo condiviso per dispositivi iOS](msal-ios-shared-devices.md).
