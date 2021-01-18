---
title: "Avvio rapido: Aggiungere l'accesso degli utenti a un'app Web Node.js | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come implementare l'autenticazione in un'applicazione Web Node.js con OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: c9aa73767fcb9d57ada11f5830fec00b10eee812
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017341"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Avvio rapido: Aggiungere l'accesso con OpenID Connect a un'app Web Node.js

In questa guida di avvio rapido si scarica e si esegue un esempio di codice che illustra come configurare l'autenticazione OpenID Connect in un'applicazione Web creata usando Node.js con Express. L'esempio è progettato per l'esecuzione in qualsiasi piattaforma.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
1. Immettere un **Nome** per l'applicazione, ad esempio `MyWebApp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
1. Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .

    Se sono presenti più URI di reindirizzamento, è necessario aggiungerli in un secondo momento dalla scheda **Autenticazione** dopo la creazione dell'app.

1. Selezionare **Registra** per creare l'app.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Questo valore sarà necessario per configurare l'applicazione più avanti in questo progetto.
1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Aggiungi una piattaforma** > **Web** 
1. Nella sezione **URI di reindirizzamento** immettere `http://localhost:3000/auth/openid/return`.
1. Immettere `https://localhost:3000` per **URI di disconnessione**.
1. Nella sezione Concessione implicita selezionare **Token ID** perché con questo esempio è necessario che il [flusso di concessione implicito](./v2-oauth2-implicit-grant-flow.md) sia abilitato per consentire all'utente di accedere.
1. Selezionare **Configura**.
1. In **Gestisci** selezionare **Certificati e segreti** > **Nuovo segreto client**.
1. Immettere una descrizione della chiave, ad esempio segreto dell'app.
1. Selezionare una durata per la chiave, ovvero **Tra 1 anno, Tra 2 anni** oppure **Non scade mai**.
1. Selezionare **Aggiungi**. Verrà visualizzato il valore della chiave. Copiare il valore della chiave e conservarlo in un luogo sicuro per un uso successivo.


## <a name="download-the-sample-application-and-modules"></a>Scaricare l'applicazione e i moduli di esempio

A questo punto, clonare il repository di esempio e installare i moduli npm.

Dalla shell o dalla riga di comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

o

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Dalla directory radice del progetto eseguire il comando:

`$ npm install`

## <a name="configure-the-application"></a>Configurare l'applicazione

Specificare i parametri di `exports.creds` nel file config.js come indicato.

* Aggiornare `<tenant_name>` in `exports.identityMetadata` con il nome del tenant di Azure AD nel formato \*.onmicrosoft.com.
* Aggiornare `exports.clientID` con l'ID applicazione di cui è stato preso nota durante la registrazione dell'app.
* Aggiornare `exports.clientSecret` con il segreto dell'applicazione di cui è stato preso nota durante la registrazione dell'app.
* Aggiornare `exports.redirectUrl` con l'URI di reindirizzamento di cui è stato preso nota durante la registrazione dell'app.

**Configurazione facoltativa per le app di produzione:**

* Aggiornare `exports.destroySessionUrl` nel file config. js, se si vuole usare un valore diverso per `post_logout_redirect_uri`.

* Impostare su true `exports.useMongoDBSessionStore` nel file config.js se si vuole usare [MongoDB](https://www.mongodb.com) o altri [archivi sessioni compatibili](https://github.com/expressjs/session#compatible-session-stores).
L'archivio sessioni predefinito in questo esempio è `express-session`. L'archivio sessioni predefinito non è adatto per la produzione.

* Aggiornare `exports.databaseUri`, se si vuole usare l'archivio sessioni MongoDB e un URI di database diverso.

* Aggiornare `exports.mongoDBSessionMaxAge`. In questo punto è possibile specificare per quanto tempo si vuole mantenere una sessione in MongoDB. L'unità di misura è secondo/i.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

Avviare il servizio MongoDB. Se si usa l'archivio sessioni MongoDB in questa app, è necessario [installare MongoDB](http://www.mongodb.org/) e avviare prima il servizio. Se si usa l'archivio sessioni predefinito, è possibile ignorare questo passaggio.

Per eseguire l'app, usare il comando seguente dalla riga di comando.

```
$ node app.js
```

**Se l'output del server è poco comprensibile:** per la registrazione in questo esempio viene usato `bunyan`. La console non è molto utile a meno che non si installi bunyan e si esegua il server come specificato in precedenza e si invia tramite pipe il file binario di bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>L'operazione è completata.

All'indirizzo `http://localhost:3000` sarà in esecuzione un server.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sullo scenario dell'app Web supportato da Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scenario: App Web che concede l'accesso agli utenti](scenario-web-app-sign-user-overview.md)