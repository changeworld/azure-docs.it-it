---
title: Librerie di autenticazione della piattaforma Microsoft Identity | Azure
description: Elenco delle librerie client e del middleware compatibili con la piattaforma di identità Microsoft. Usare queste librerie per aggiungere il supporto per l'accesso utente (autenticazione) e l'accesso all'API Web protetto (autorizzazione) alle applicazioni.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218283"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Librerie di autenticazione della piattaforma Microsoft Identity

Le tabelle seguenti illustrano il supporto della libreria di autenticazione Microsoft per diversi tipi di applicazioni. Includono collegamenti al codice sorgente della libreria, dove ottenere il pacchetto per il progetto dell'app e se la libreria supporta l'accesso utente (autenticazione), l'accesso alle API Web protette (autorizzazione) o entrambi.

La piattaforma Microsoft Identity è stata certificata da OpenID Foundation come [provider OpenID certificato](https://openid.net/certification/). Se si preferisce usare una libreria diversa da Microsoft Authentication Library (MSAL) o da un'altra libreria supportata da Microsoft, sceglierne una con un' [implementazione certificata di OpenID Connect](https://openid.net/developers/certified/).

Se si sceglie di scrivere manualmente l'implementazione a livello di protocollo di [OAuth 2,0 o OpenID Connect 1,0](active-directory-v2-protocols.md), prestare particolare attenzione alle considerazioni sulla sicurezza nella specifica di ogni standard e seguire una metodologia SDL (Software Development Lifecycle) come [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Applicazione a pagina singola (SPA)

Un'applicazione a singola pagina viene eseguita interamente sull'area del browser e recupera i dati della pagina (HTML, CSS e JavaScript) in modo dinamico o in fase di caricamento dell'applicazione. Può chiamare API Web per interagire con le origini dati back-end.

Poiché il codice di una SPA viene eseguito interamente nel browser, viene considerato un *client pubblico* che non è in grado di archiviare i segreti in modo sicuro.

| Linguaggio/Framework | Progetto in<br/>GitHub                                                                                                    | Pacchetto                                                                      | Ottenere<br/>avviata                             | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL angolare 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| Angular              | [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Esercitazione](tutorial-v2-angular.md)              | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| AngularJS            | [AngularJS MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Esercitazione](tutorial-v2-javascript-auth-code.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

## <a name="web-application"></a>Applicazione Web

Un'applicazione Web esegue il codice in un server che genera e Invia HTML, CSS e JavaScript al Web browser di un utente di cui eseguire il rendering. L'identità dell'utente viene mantenuta come una sessione tra il browser dell'utente (front-end) e il server Web (il back-end).

Poiché il codice di un'applicazione Web viene eseguito sul server Web, viene considerato un *client riservato* in grado di archiviare i segreti in modo sicuro.

| Linguaggio/Framework | Progetto in<br/>GitHub                                                                                     | Pacchetto                                                                                                    | Ottenere<br/>avviata                               | Consentire l'accesso degli utenti                                            | Accedere alle API Web                                                    | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET Security](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria non può richiedere token di accesso per le API Web protette.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Guida introduttiva](quickstart-v2-java-webapp.md)        | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| Node.js              | [Node.jsMSAL ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nodo](https://www.npmjs.com/package/@azure/msal-node)                                                | [Guida introduttiva](quickstart-v2-nodejs-webapp-msal.md) | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | Anteprima pubblica                                               |
| Node.js              | [Passport Azure AD](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport-Azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Guida introduttiva](quickstart-v2-nodejs-webapp.md)      | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria non può richiedere token di accesso per le API Web protette.][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [MSAL](https://pypi.org/project/msal)                                                                      | [Guida introduttiva](quickstart-v2-python-webapp.md)      | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

## <a name="desktop-application"></a>Applicazione desktop

Un'applicazione desktop è in genere codice binario (compilato) che espone un'interfaccia utente ed è destinata all'esecuzione sul desktop di un utente.

Poiché un'applicazione desktop viene eseguita sul desktop dell'utente, viene considerato un *client pubblico* che non è in grado di archiviare in modo sicuro i segreti.

| Linguaggio/Framework | Progetto in<br/>GitHub                                                                                     | Pacchetto                                                                               | Ottenere<br/>avviata                        | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [Node.jsMSAL ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| macOS (Swift/obj-C)  | [MSAL per iOS e MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Esercitazione](tutorial-v2-ios.md)             | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Piattaforma UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Esercitazione](tutorial-v2-windows-uwp.md)     | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Esercitazione](tutorial-v2-windows-desktop.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

## <a name="mobile-application"></a>Applicazione per dispositivi mobili

Un'applicazione per dispositivi mobili è in genere codice binario (compilato) che espone un'interfaccia utente ed è destinata all'esecuzione sul dispositivo mobile di un utente.

Poiché un'applicazione per dispositivi mobili viene eseguita sul dispositivo mobile dell'utente, viene considerato un *client pubblico* che non è in grado di archiviare i segreti in modo sicuro.

| Piattaforma          | Progetto in<br/>GitHub                                                                          | Pacchetto                                                                               | Ottenere<br/>avviata                    | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Guida introduttiva](quickstart-v2-android.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| iOS (Swift/obj-C) | [MSAL per iOS e MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Esercitazione](tutorial-v2-ios.md)         | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

## <a name="service--daemon"></a>Servizio/daemon

I servizi e i daemon sono comunemente usati per la comunicazione da server a server e altre comunicazioni automatiche (talvolta denominate senza *intestazioni*). Poiché non è presente alcun utente sulla tastiera per immettere le credenziali o consentire l'accesso alle risorse, queste applicazioni vengono autenticate come se stesse, non come utente, quando richiede l'accesso autorizzato alle risorse di un'API Web.

Un servizio o daemon eseguito in un server è considerato un *client riservato* in grado di archiviare i propri segreti in modo sicuro.

| Linguaggio/Framework | Progetto in<br/>GitHub                                                                 | Pacchetto                                                                                | Ottenere<br/>avviata                           | Consentire l'accesso degli utenti                                            | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Guida introduttiva](quickstart-v2-netcore-daemon.md) | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [MSAL-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Guida introduttiva](quickstart-v2-python-daemon.md)  | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Microsoft Authentication Library, vedere la pagina relativa alla [Panoramica di Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
