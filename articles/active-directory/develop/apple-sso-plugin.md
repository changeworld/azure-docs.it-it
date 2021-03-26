---
title: Plug-in Microsoft Enterprise SSO per dispositivi Apple
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sul plug-in Azure Active Directory SSO per i dispositivi iOS, iPados e macOS.
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
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559129"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)

>[!IMPORTANT]
> Questa funzionalità [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Il *plug-in Microsoft Enterprise SSO per i dispositivi Apple* fornisce Single Sign-on (SSO) per gli account Azure Active Directory (Azure ad) in MacOS, iOS e ipados in tutte le applicazioni che supportano la funzionalità [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) di Apple. Il plug-in fornisce SSO per le applicazioni ancora obsolete che possono dipendere dall'azienda ma che non supportano ancora le librerie di identità o i protocolli più recenti. Microsoft ha collaborato con Apple per sviluppare questo plug-in per aumentare l'usabilità dell'applicazione garantendo al tempo stesso la migliore protezione disponibile.

Il plug-in Enterprise SSO è attualmente una funzionalità predefinita delle app seguenti:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, ipados
* [Portale aziendale](/mem/intune/apps/apps-company-portal-macos)Microsoft Intune: MacOS

## <a name="features"></a>Funzionalità

Il plug-in Microsoft Enterprise SSO per i dispositivi Apple offre i vantaggi seguenti:

- Fornisce l'accesso SSO per gli account Azure AD in tutte le applicazioni che supportano la funzionalità Apple Enterprise SSO.
- Può essere abilitato da qualsiasi soluzione di gestione di dispositivi mobili (MDM).
- Consente di estendere l'accesso SSO alle applicazioni che non usano ancora le librerie della piattaforma Microsoft Identity.
- Consente di estendere l'accesso Single Sign-on alle applicazioni che usano OAuth 2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisiti

Per usare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- Il dispositivo deve *supportare* e avere un'app installata con il plug-in Microsoft Enterprise SSO per i dispositivi Apple:
  - iOS 13,0 e versioni successive: [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPados 13,0 e versioni successive: [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 e versioni successive: [app portale aziendale Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Il dispositivo deve essere *registrato in MDM*, ad esempio tramite Microsoft Intune.
- Per abilitare il plug-in Enterprise SSO, è necessario effettuare il push della configurazione *al dispositivo* . Apple richiede questo vincolo di sicurezza.

### <a name="ios-requirements"></a>requisiti di iOS:
- iOS 13,0 o versione successiva deve essere installato nel dispositivo.
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni sono l' [app Microsoft Authenticator](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>requisiti macOS:
- macOS 10,15 o versione successiva deve essere installato nel dispositivo. 
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni includono l' [app portale aziendale Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>Abilitare il plug-in SSO

Usare le informazioni seguenti per abilitare il plug-in SSO tramite MDM.
### <a name="microsoft-intune-configuration"></a>Configurazione di Microsoft Intune

Se si usa Microsoft Intune come servizio MDM, è possibile usare le impostazioni predefinite del profilo di configurazione per abilitare il plug-in Microsoft Enterprise SSO:

1. Configurare le impostazioni dell' [estensione dell'app SSO](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) di un profilo di configurazione. 
1. Se il profilo non è già assegnato, [assegnare il profilo a un utente o a un gruppo di dispositivi](/mem/intune/configuration/device-profile-assign).

Le impostazioni del profilo che abilitano il plug-in SSO vengono applicate automaticamente ai dispositivi del gruppo la volta successiva che ogni dispositivo archivia con Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configurazione manuale per altri servizi MDM

Se non si usa Intune per MDM, usare i parametri seguenti per configurare il plug-in di Microsoft Enterprise SSO per i dispositivi Apple.

Impostazioni iOS:

- **ID estensione**: `com.microsoft.azureauthenticator.ssoextension`
- **ID team**: questo campo non è necessario per iOS.

Impostazioni macOS:

- **ID estensione**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID team**: `UBF8T346G9`

Impostazioni comuni:

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
  
### <a name="more-configuration-options"></a>Altre opzioni di configurazione
È possibile aggiungere altre opzioni di configurazione per estendere la funzionalità SSO ad altre app.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Abilitare l'accesso SSO per le app che non usano una libreria della piattaforma di identità Microsoft

Il plug-in SSO consente a qualsiasi applicazione di partecipare all'accesso SSO anche se non è stato sviluppato utilizzando un SDK Microsoft come Microsoft Authentication Library (MSAL).

Il plug-in SSO viene installato automaticamente dai dispositivi che hanno:
* È stata scaricata l'app Authenticator in iOS o iPados oppure è stata scaricata l'app Portale aziendale Intune in macOS.
* Registrato il dispositivo con l'organizzazione. 

È probabile che l'organizzazione usi l'app Authenticator per scenari come l'autenticazione a più fattori, l'autenticazione con password e l'accesso condizionale. Utilizzando un provider MDM, è possibile attivare il plug-in SSO per le applicazioni. Microsoft ha semplificato la configurazione del plug-in all'interno di Microsoft Endpoint Manager in Intune. Per configurare le applicazioni in modo che utilizzino il plug-in SSO, viene utilizzato un oggetto allow.

>[!IMPORTANT]
> Il plug-in Microsoft Enterprise SSO supporta solo le app che usano le tecnologie di rete Apple native o le WebView. Non supporta le applicazioni che forniscono una propria implementazione del livello di rete.  

Usare i parametri seguenti per configurare il plug-in di Microsoft Enterprise SSO per le app che non usano una libreria della piattaforma di identità Microsoft.

Per fornire un elenco di app specifiche, usare i parametri seguenti:

- **Chiave**: `AppAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di ID bundle applicazione per le applicazioni che possono partecipare a SSO.
- **Esempio**: `com.contoso.workapp, com.contoso.travelapp`

Per fornire un elenco di prefissi, usare i parametri seguenti:
- **Chiave**: `AppPrefixAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di prefissi ID bundle applicazione per le applicazioni che possono partecipare a SSO. Questo parametro consente a tutte le app che iniziano con un prefisso specifico di partecipare a SSO.
- **Esempio**: `com.contoso., com.fabrikam.`

Le [app consentite](./application-consent-experience.md) che l'amministratore MDM consente di partecipare all'accesso SSO possono ottenere automaticamente un token per l'utente finale. Quindi, aggiungere solo le applicazioni attendibili all'oggetto allow. 

>[!NOTE]
> Non è necessario aggiungere applicazioni che usano MSAL o ASWebAuthenticationSession all'elenco di app che possono partecipare a SSO. Queste applicazioni sono abilitate per impostazione predefinita. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Trovare gli identificatori del bundle dell'app nei dispositivi iOS

Apple non fornisce un modo semplice per ottenere gli ID bundle dall'App Store. Il modo più semplice per ottenere gli ID bundle delle app che si vuole usare per SSO è chiedere al fornitore o allo sviluppatore di app. Se questa opzione non è disponibile, è possibile usare la configurazione MDM per trovare gli ID bundle: 

1. Abilitare temporaneamente il flag seguente nella configurazione MDM:

    - **Chiave**: `admin_debug_mode_enabled`
    - **Tipo**: `Integer`
    - **Valore**: 1 o 0
1. Quando questo flag è acceso, accedere alle app iOS nel dispositivo per cui si vuole ottenere informazioni sull'ID bundle. 
1. Nell'app Authenticator selezionare **Guida**  >  **Invia log**  >  **visualizzazione log**. 
1. Nel file di log cercare la riga seguente: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Questa riga dovrebbe acquisire tutti gli ID del bundle di applicazioni visibili all'estensione SSO. 

Usare gli ID bundle per configurare l'accesso SSO per le app. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Consenti agli utenti di accedere da applicazioni sconosciute e dal browser Safari

Per impostazione predefinita, il plug-in Microsoft Enterprise SSO fornisce SSO per le app autorizzate solo quando un utente ha eseguito l'accesso da un'app che usa una libreria della piattaforma di identità Microsoft, ad esempio MSAL o Azure Active Directory Authentication Library (ADAL). Il plug-in Microsoft Enterprise SSO può anche acquisire credenziali condivise quando viene chiamato da un'altra app che usa una libreria della piattaforma di identità Microsoft durante una nuova acquisizione di token.

Quando si Abilita il `browser_sso_interaction_enabled` flag, le app che non usano una libreria di Microsoft Identity Platform possono eseguire il bootstrap iniziale e ottenere le credenziali condivise. Il browser Safari può anche eseguire il bootstrap iniziale e ottenere le credenziali condivise. 

Se il plug-in Microsoft Enterprise SSO non dispone ancora di credenziali condivise, tenterà di ottenerne una ogni volta che viene richiesto un accesso da un URL Azure AD all'interno del browser Safari, ASWebAuthenticationSession, SafariViewController o un'altra applicazione nativa consentita. 

Usare questi parametri per abilitare il flag: 

- **Chiave**: `browser_sso_interaction_enabled`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

macOS richiede questa impostazione, in modo che possa fornire un'esperienza coerente in tutte le app. iOS e iPados non richiedono questa impostazione perché la maggior parte delle app usa l'applicazione Authenticator per l'accesso. Tuttavia è consigliabile abilitare questa impostazione perché se alcune applicazioni non usano l'app Authenticator in iOS o iPados, questo flag consente di migliorare l'esperienza. Per impostazione predefinita, l'impostazione è disabilitata.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Disabilitare la richiesta di autenticazione a più fattori durante il bootstrap iniziale

Per impostazione predefinita, il plug-in Microsoft Enterprise SSO richiede sempre all'utente l'autenticazione a più fattori durante il bootstrap iniziale e durante il recupero di una credenziale condivisa. All'utente viene richiesto di eseguire l'autenticazione a più fattori anche se non è necessario per l'applicazione aperta dall'utente. Questo comportamento consente di usare facilmente le credenziali condivise tra tutte le altre applicazioni senza dover richiedere all'utente se l'autenticazione a più fattori è necessaria in un secondo momento. Poiché l'utente riceve un minor numero di richieste in generale, questa configurazione è in genere una scelta appropriata.

L'abilitazione `browser_sso_disable_mfa` Disattiva l'autenticazione a più fattori durante il bootstrap iniziale e durante il recupero delle credenziali condivise. In questo caso, all'utente viene richiesto solo quando l'autenticazione a più fattori è richiesta da un'applicazione o da una risorsa. 

Per abilitare il flag, usare i parametri seguenti:

- **Chiave**: `browser_sso_disable_mfa`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

È consigliabile mantenere questo flag disabilitato perché riduce il numero di volte in cui viene richiesto all'utente di eseguire l'accesso. Se l'organizzazione usa raramente l'autenticazione a più fattori, potrebbe essere necessario abilitare il flag. È tuttavia consigliabile usare l'autenticazione a più fattori con maggiore frequenza. Per questo motivo, il flag è disabilitato per impostazione predefinita.

#### <a name="disable-oauth-2-application-prompts"></a>Disabilitare i prompt dell'applicazione OAuth 2

Il plug-in Microsoft Enterprise SSO fornisce l'accesso SSO aggiungendo le credenziali condivise alle richieste di rete provenienti dalle applicazioni consentite. Tuttavia, alcune applicazioni OAuth 2 potrebbero erroneamente applicare le richieste degli utenti finali a livello di protocollo. Se viene visualizzato questo problema, si noterà anche che le credenziali condivise vengono ignorate per tali app. All'utente viene richiesto di eseguire l'accesso anche se il plug-in Microsoft Enterprise SSO funziona per altre applicazioni.  

L'abilitazione del `disable_explicit_app_prompt` flag limita la capacità delle applicazioni native e delle applicazioni Web di forzare un prompt dell'utente finale sul livello del protocollo e di ignorare SSO. Per abilitare il flag, usare i parametri seguenti:

- **Chiave**: `disable_explicit_app_prompt`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

È consigliabile abilitare questo flag per ottenere un'esperienza coerente in tutte le app. È disabilitata per impostazione predefinita. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Abilitare l'accesso SSO tramite cookie per un'applicazione specifica

Alcune app potrebbero non essere compatibili con l'estensione SSO. In particolare, le app con impostazioni di rete avanzate potrebbero riscontrare problemi imprevisti quando sono abilitati per SSO. Ad esempio, potrebbe essere visualizzato un errore che indica che la richiesta di rete è stata annullata o interrotta. 

Se si verificano problemi di accesso usando il metodo descritto nella sezione [applicazioni che non usano MSAL](#applications-that-dont-use-msal) , provare una configurazione alternativa. Utilizzare questi parametri per configurare il plug-in:

- **Chiave**: `AppCookieSSOAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di prefissi ID bundle applicazione per le applicazioni che possono partecipare all'accesso SSO. Tutte le app che iniziano con i prefissi elencati saranno autorizzate a partecipare a SSO.
- **Esempio**: `com.contoso.myapp1, com.fabrikam.myapp2`

Le applicazioni abilitate per l'accesso SSO tramite questa installazione devono essere aggiunte sia a `AppCookieSSOAllowList` che a `AppPrefixAllowList` .

Provare questa configurazione solo per le applicazioni in cui si verificano errori di accesso imprevisti. 

#### <a name="use-intune-for-simplified-configuration"></a>Usare Intune per la configurazione semplificata

È possibile usare Intune come servizio MDM per semplificare la configurazione del plug-in di Microsoft Enterprise SSO. Ad esempio, è possibile usare Intune per abilitare il plug-in e aggiungere app obsolete a un oggetto Allow, in modo che ottengano SSO. 

Per ulteriori informazioni, vedere la [documentazione di configurazione di Intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Usare il plug-in SSO nell'applicazione

[MSAL per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versioni 1.1.0 e successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Si tratta del metodo consigliato per aggiungere il supporto per il plug-in Microsoft Enterprise SSO. Consente di ottenere le funzionalità complete della piattaforma di identità Microsoft.

Se si sta creando un'applicazione per gli scenari di lavoro di Frontline, vedere [modalità del dispositivo condiviso per i dispositivi iOS](msal-ios-shared-devices.md) per informazioni sul programma di installazione.

## <a name="understand-how-the-sso-plug-in-works"></a>Informazioni sul funzionamento del plug-in SSO

Il plug-in Microsoft Enterprise SSO si basa sul [Framework di Enterprise SSO di Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). I provider di identità che si uniscono al Framework possono intercettare il traffico di rete per i propri domini e migliorare o modificare il modo in cui tali richieste vengono gestite. Il plug-in SSO, ad esempio, può mostrare più interfacce utente per raccogliere in modo sicuro le credenziali dell'utente finale, richiedere l'autenticazione a più fattori o fornire automaticamente i token all'applicazione.

Le applicazioni native possono inoltre implementare operazioni personalizzate e comunicare direttamente con il plug-in SSO. Per altre informazioni, vedere questo [video di 2019 Worldwide Developer Conference di Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Applicazioni che usano MSAL

[MSAL per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versioni 1.1.0 e successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple in modo nativo per gli account aziendali e dell'Istituto di istruzione. 

Non è necessaria alcuna configurazione speciale se sono stati seguiti [tutti i passaggi consigliati](./quickstart-v2-ios.md) e si è usato il [formato URI di reindirizzamento](./redirect-uris-ios.md)predefinito. Nei dispositivi in cui è installato il plug-in SSO, MSAL lo richiama automaticamente per tutte le richieste di token interattivo e invisibile all'utente. Viene richiamato anche per le operazioni di enumerazione degli account e di rimozione dell'account. Poiché MSAL implementa un protocollo plug-in nativo SSO che si basa su operazioni personalizzate, questa configurazione offre all'utente finale l'esperienza nativa più semplice. 

Se il plug-in SSO non è abilitato da MDM ma l'app Microsoft Authenticator è presente nel dispositivo, MSAL usa invece l'app Authenticator per le richieste di token interattive. Il plug-in SSO condivide l'accesso SSO con l'app Authenticator.

### <a name="applications-that-dont-use-msal"></a>Applicazioni che non usano MSAL

Le applicazioni che non usano una libreria della piattaforma di identità Microsoft, ad esempio MSAL, possono comunque ottenere SSO se un amministratore aggiunge queste applicazioni all'oggetto allow. 

Non è necessario modificare il codice nelle app purché siano soddisfatte le condizioni seguenti:

- L'applicazione usa i framework Apple per eseguire le richieste di rete. Questi Framework includono, ad esempio, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) e [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- L'applicazione usa protocolli standard per comunicare con Azure AD. Questi protocolli includono, ad esempio, OAuth 2, SAML e WS-Federation.
- L'applicazione non raccoglie i nomi utente e le password in testo non crittografato nell'interfaccia utente nativa.

In questo caso, SSO viene fornito quando l'applicazione crea una richiesta di rete e apre un Web browser per l'accesso dell'utente. Quando un utente viene reindirizzato a un URL di accesso Azure AD, il plug-in SSO convalida l'URL e verifica la presenza di una credenziale SSO per tale URL. Se trova le credenziali, il plug-in SSO lo passa a Azure AD, che autorizza l'applicazione a completare la richiesta di rete senza chiedere all'utente di immettere le credenziali. Inoltre, se il dispositivo è noto Azure AD, il plug-in SSO passa il certificato del dispositivo per soddisfare il controllo dell'accesso condizionale basato su dispositivo. 

Per supportare l'accesso SSO per le app non MSAL, il plug-in SSO implementa un protocollo simile al plug-in del browser Windows descritto in [che cos'è un token di aggiornamento primario?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Rispetto alle app basate su MSAL, il plug-in SSO agisce in modo più trasparente per le app non MSAL. Si integra con l'esperienza di accesso del browser esistente fornita dalle app. 

L'utente finale vede l'esperienza familiare e non deve eseguire nuovamente l'accesso in ogni applicazione. Ad esempio, anziché visualizzare la selezione account nativa, il plug-in SSO aggiunge sessioni SSO all'esperienza di selezione account basata sul Web. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [modalità dispositivo condiviso per i dispositivi iOS](msal-ios-shared-devices.md).
