---
title: Registrare un'app Web che esegue l'accesso agli utenti | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare un'app Web per l'accesso degli utenti
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 920249aa252469c3db2be284fc010d775d04c921
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578278"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>App Web che esegue l'accesso degli utenti: registrazione dell'app

Questo articolo illustra i passaggi di registrazione delle app per un'app Web che esegue l'accesso agli utenti.

Per registrare l'applicazione, è possibile usare:

- [Guide introduttive per le app Web](#register-an-app-by-using-the-quickstarts). Oltre a essere un'ottima esperienza per la creazione di un'applicazione, le guide introduttive nell'portale di Azure contengono un pulsante denominato **apportare questa modifica**. È possibile usare questo pulsante per impostare le proprietà necessarie, anche per un'app esistente. Adattare i valori di queste proprietà al proprio caso. In particolare, l'URL dell'API Web per l'app è probabilmente diverso dal valore predefinito proposto, che influirà anche sull'URI di disconnessione.
- Portale di Azure per [registrare manualmente l'applicazione](#register-an-app-by-using-the-azure-portal).
- PowerShell e gli strumenti da riga di comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrare un'app usando le guide introduttive

È possibile utilizzare questi collegamenti per avviare la creazione dell'applicazione Web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrare un'app usando il portale di Azure

> [!NOTE]
> Il portale da usare varia a seconda che l'applicazione venga eseguita nel cloud pubblico Microsoft Azure o in un cloud nazionale o sovrano. Per altre informazioni, vedere [cloud nazionali](./authentication-national-cloud.md#app-registration-endpoints).


1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>. 
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. Immettere un **Nome** per l'applicazione, ad esempio `AspNetCore-WebApp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
   1. Scegliere i tipi di account supportati per l'applicazione. Vedere [tipi di account supportati](./v2-supported-account-types.md).
   1. Per **URI di reindirizzamento** aggiungere il tipo di applicazione e la destinazione URI che accetterà le risposte del token restituito dopo l'autenticazione. Immettere ad esempio `https://localhost:44321`.
   1. Selezionare **Registra**.
1. In **Gestisci** selezionare **autenticazione** e quindi aggiungere le informazioni seguenti:
   1. Nella sezione **Web** aggiungere `https://localhost:44321/signin-oidc` come **URI di reindirizzamento**.
   1. In **URL di disconnessione front-Channel**, immettere `https://localhost:44321/signout-oidc` .
   1. In **concessione implicita e flussi ibridi** selezionare **token ID**.
   1. Selezionare **Salva**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   1. Immettere un **Nome** per l'applicazione, ad esempio `MailApp-openidconnect-v2`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
   1. Scegliere i tipi di account supportati per l'applicazione. Vedere [tipi di account supportati](./v2-supported-account-types.md).
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere un URI di **Reindirizzamento** `https://localhost:44326/` .
   1. Selezionare **Registra** per creare l'applicazione.
1. In **Gestisci** selezionare **Autenticazione**.
1. Nella sezione **concessione implicita e flussi ibridi** selezionare **token ID**. Questo esempio richiede che il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md) sia abilitato per l'accesso dell'utente.
1. Selezionare **Salva**.

# <a name="java"></a>[Java](#tab/java)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione: 
    1. Immettere un **Nome** per l'applicazione, ad esempio `java-webapp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento. 
    1. Selezionare **account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**.
    1. Selezionare **Register (registra** ) per registrare l'applicazione.
1. In **Gestisci** selezionare **Autenticazione** > **Aggiungi una piattaforma**.
1. Selezionare **Web**.
1. Per **URI di reindirizzamento**, immettere lo stesso host e numero di porta, seguito da `/msal4jsample/secure/aad` per la pagina di accesso. 
1. Selezionare **Configura**.
1. Nella sezione **Web** usare l'host e il numero di porta, seguiti da `/msal4jsample/graph/me` come **URI di reindirizzamento** per la pagina informazioni utente.
Per impostazione predefinita, l'esempio USA:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Selezionare **Salva**.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Nella sezione **segreti client** selezionare **nuovo segreto client**, quindi:

   1. Immettere una descrizione della chiave.
   1. Selezionare la durata della chiave **in 1 anno**.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo per un momento successivo. Questo valore non verrà visualizzato di nuovo o potrà essere recuperato in qualsiasi altro modo.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   1. Immettere un **Nome** per l'applicazione, ad esempio `node-webapp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
   1. Modificare i **tipi di account supportati** **in account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**.
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere l'URI di reindirizzamento seguente: `http://localhost:3000/redirect` .
   1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Sarà necessario per configurare il file di configurazione per questo progetto.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Nella sezione **segreti client** selezionare **nuovo segreto client**, quindi:
   1. Immettere una descrizione della chiave.
   1. Selezionare **Tra 1 anno** per la durata della chiave.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo. Sarà necessario più avanti.

# <a name="python"></a>[Python](#tab/python)

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   1. Immettere un **Nome** per l'applicazione, ad esempio `python-webapp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
   1. Modificare i **tipi di account supportati** **in account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**.
   1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere l'URI di reindirizzamento seguente: `http://localhost:5000/getAToken` .
   1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Nella sezione **segreti client** selezionare **nuovo segreto client**, quindi:
   1. Immettere una descrizione della chiave.
   1. Selezionare **Tra 1 anno** per la durata della chiave.
   1. Selezionare **Aggiungi**.
   1. Quando viene visualizzato il valore della chiave, copiarlo. Sarà necessario più avanti.
---

## <a name="register-an-app-by-using-powershell"></a>Registrare un'app usando PowerShell

> [!NOTE]
> Attualmente, Azure AD PowerShell crea applicazioni con solo i tipi di conto supportati seguenti:
>
> - MyOrg (solo account in questa directory organizzativa)
> - AnyOrg (account in qualsiasi directory organizzativa)
>
> È possibile creare un'applicazione che esegua l'accesso degli utenti con gli account Microsoft personali (ad esempio, Skype, Xbox o Outlook.com). Per prima cosa, creare un'applicazione multi-tenant. I tipi di account supportati sono account in qualsiasi directory dell'organizzazione. Impostare quindi la [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) proprietà su **2** e la [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) proprietà su `AzureADandPersonalMicrosoftAccount` nel [manifesto dell'applicazione](./reference-app-manifest.md) dal portale di Azure. Per ulteriori informazioni, vedere il [passaggio 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) nell'esercitazione ASP.NET Core. È possibile generalizzare questo passaggio per le app Web in qualsiasi linguaggio.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [configurazione del codice dell'app](scenario-web-app-sign-user-app-configuration.md).
