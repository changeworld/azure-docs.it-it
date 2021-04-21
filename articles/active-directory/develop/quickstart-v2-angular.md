---
title: "Avvio rapido: Consentire l'accesso degli utenti nelle applicazioni a pagina singola Angular - Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'app Angular può chiamare un'API che richiede token di accesso generati da Microsoft Identity Platform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814029"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Guida introduttiva: Consentire l'accesso agli utenti e ottenere un token di accesso in un'applicazione a pagina singola Angular

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione a pagina singola Angular che consente agli utenti di accedere e chiamare Microsoft Graph. L'esempio di codice dimostra come ottenere un token di accesso per chiamare l'API Microsoft Graph o qualsiasi API Web.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) per modificare i file di progetto oppure [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) per eseguire il progetto.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrare e scaricare l'app di avvio rapido
>
> Per avviare l'app di avvio rapido, usare una delle opzioni descritte di seguito.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opzione 1 (rapida): Registrare e configurare automaticamente l'app e quindi scaricare il codice di esempio
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app</a>.
> 1. Immettere un nome per l'applicazione e quindi fare clic su **Registra**.
> 1. Nel riquadro di avvio rapido trovare la guida introduttiva di Angular. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opzione 2 (manuale): Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Seguire le istruzioni per [registrare un'applicazione a pagina singola](./scenario-spa-app-registration.md) nel portale di Azure.
> 1. Aggiungere una nuova piattaforma nel riquadro **Autenticazione** della registrazione dell'app e registrare l'URI di reindirizzamento: `http://localhost:4200/`.
> 1. Questo avvio rapido usa il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Nella sezione **Concessione implicita e flussi ibridi** selezionare **Token ID e** Token di **accesso.** I token ID e i token di accesso sono necessari perché questa app consente agli utenti di accedere e chiama un'API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il funzionamento dell'esempio di codice in questa guida introduttiva, è necessario aggiungere un URI di reindirizzamento a `http://localhost:4200/` e abilitare **Concessione implicita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-javascript/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-code-sample"></a>Passaggio 2: Scaricare il codice di esempio
>[!div renderon="docs"]
>Per eseguire il progetto con un server Web usando Node.js, clonare il [repository](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) di esempio o [scaricare i file di progetto principali](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Aprire i file in un editor, ad esempio Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Passaggio 3: Configurare l'app JavaScript
>
> Nella cartella *src/app* modificare *app.module.ts* e impostare i valori di `clientId` e `authority` in `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Sostituire questi valori:
>
>|Nome del valore|Descrizione|
>|---------|---------|
>|Enter_the_Application_Id_Here|Nella pagina **Panoramica** della registrazione dell'applicazione, è il valore di **ID applicazione (client)** . |
>|Enter_the_Cloud_Instance_Id_Here|Si tratta dell'istanza cloud di Azure. Per il cloud di Azure principale o globale immettere `https://login.microsoftonline.com`. Per i cloud nazionali (ad esempio, Cina), vedere [Cloud nazionali](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Impostare su una delle opzioni seguenti: se l'applicazione supporta gli account in questa *directory* organizzativa, sostituire questo valore con l'ID della directory (tenant) o il nome del tenant (ad esempio, `contoso.microsoft.com` ). Se l'applicazione supporta *account in qualsiasi directory organizzativa*, sostituire questo valore con `organizations`. Se l'applicazione supporta *account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con `common`. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con `consumers`. |
>|Enter_the_Redirect_Uri_Here|Sostituire con `http://localhost:4200` .|
>|cacheLocation  | (Facoltativo) Impostare l'archiviazione browser per lo stato di autenticazione. Il valore predefinito è `sessionStorage`.   |
>|storeAuthStateInCookie  | (Facoltativo) Identificare la libreria in cui è archiviato lo stato della richiesta di autenticazione. Questo stato è necessario per convalidare i flussi di autenticazione nei cookie del browser. Questo cookie è impostato per Internet Explorer e Microsoft Edge per supportare questi due browser. Per altri dettagli, vedere i [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** dell'app nel portale di Azure.

> Per altre informazioni sulle opzioni configurabili disponibili, vedere [Inizializzare le applicazioni client](msal-js-initializing-client-applications.md).

> Il codice sorgente per la libreria MSAL.js è disponibile nel repository [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) in GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app.

> [!div renderon="docs"]
>
> Scorrere verso il basso nello stesso file e aggiornare `graphMeEndpoint` . 
> - Sostituire la stringa `Enter_the_Graph_Endpoint_Herev1.0/me` con `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` è l'endpoint a cui verranno eseguite le chiamate API. Per il servizio API Microsoft Graph principale (globale), immettere `https://graph.microsoft.com/` (includere la barra finale). Per altre informazioni, vedere la [documentazione](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Se si usa Node.js:

1. Avviare il server eseguendo i comandi seguenti dalla directory del progetto:

   ```console
   npm install
   npm start
   ```

1. Passare a `http://localhost:4200/`.
1. Selezionare **Accedi**. La prima volta che si accede, viene richiesto di consentire all'applicazione di accedere al profilo e di accedere automaticamente.
1. Selezionare **Profilo** per chiamare Microsoft Graph. Le informazioni sul profilo utente vengono visualizzate nella pagina.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Diagramma che illustra il funzionamento dell'app di esempio in questa guida introduttiva.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come accedere a un utente e acquisire i token, vedere l'esercitazione su Angular:

> [!div class="nextstepaction"]
> [Esercitazioni su Angular](./tutorial-v2-angular.md)
