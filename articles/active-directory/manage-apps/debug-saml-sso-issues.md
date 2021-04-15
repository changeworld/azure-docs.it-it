---
title: Eseguire il debug dell'accesso Single Sign-On basato su SAML - Azure Active Directory
description: Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374319"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory

Informazioni su come trovare e risolvere i problemi di accesso [Single Sign-On](what-is-single-sign-on.md) per le applicazioni in Azure Active Directory (Azure AD) che usano l'accesso Single Sign-On basato su SAML. 

## <a name="before-you-begin"></a>Prima di iniziare

È consigliabile installare l'[estensione My Apps Secure Sign-in](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Questa estensione del browser semplifica la raccolta delle informazioni sulla richiesta SAML e sulla risposta SAML necessarie per risolvere i problemi relativi all'accesso Single Sign-On. Nel caso non sia possibile installare l'estensione, questo articolo descrive come risolvere i problemi anche senza l'estensione installata.

Per scaricare e installare l'estensione My Apps Secure Sign-in, usare uno dei collegamenti seguenti.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testare l'accesso Single Sign-On basato su SAML

Per testare l'accesso Single Sign-On basato su SAML tra Azure AD e un'applicazione di destinazione:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale e altro amministratore autorizzato a gestire le applicazioni.
1. Nel pannello sinistro selezionare **Azure Active Directory** e quindi selezionare Applicazioni **aziendali**. 
1. Nell'elenco delle applicazioni aziendali selezionare l'applicazione per cui si vuole testare l'accesso Single Sign-On e quindi nelle opzioni a sinistra selezionare **Single Sign-On.**
1. Per aprire l'esperienza di test dell'accesso Single Sign-On basata su SAML, passare a Testare l'accesso **Single Sign-On** (passaggio 5). Se il **pulsante Test** è in grigio, è necessario compilare e salvare prima gli attributi necessari nella sezione Configurazione **SAML di** base.
1. Nel pannello **Test dell'accesso Single Sign-On** usare le credenziali aziendali per accedere all'applicazione di destinazione. È possibile accedere come utente corrente o come altro utente. Se si accede come altro utente, verrà chiesto di eseguire l'autenticazione.

    ![Screenshot che mostra la pagina saml SSO di test](./media/debug-saml-sso-issues/test-single-sign-on.png)

Se è stato effettuato l'accesso, è stato superato il test. In questo caso, Azure AD ha rilasciato un token di risposta SAML all'applicazione. L'applicazione ha usato il token SAML per consentire l'accesso.

Se si verifica un errore nella pagina di accesso aziendale o nella pagina dell'applicazione, usare una delle sezioni seguenti per correggere l'errore.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Risolvere un errore di accesso nella pagina di accesso aziendale

Quando si prova ad accedere, è possibile che nella pagina di accesso dell'azienda venga visualizzato un errore simile all'esempio seguente.

![Esempio che mostra un errore nella pagina di accesso aziendale](./media/debug-saml-sso-issues/error.png)

Per eseguire il debug di questo errore, sono necessari il messaggio di errore e la richiesta SAML. L'estensione My Apps Secure Sign-in raccoglie automaticamente queste informazioni e visualizza le istruzioni per risolvere il problema in Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Per risolvere l'errore di accesso con l'estensione App personali secure sign-in installata

1. Quando si verifica un errore, l'estensione reindirizza l'utente al Azure AD **Single Sign-On** di test.
1. Nel pannello **Test single sign-on** selezionare **Scarica la richiesta SAML.**
1. Vengono visualizzate le istruzioni di risoluzione specifiche basate sull'errore e sui valori presenti nella richiesta SAML.
1. Verrà visualizzato un pulsante **Correggi per** aggiornare automaticamente la configurazione in Azure AD risolvere il problema. Se questo pulsante non viene visualizzato, il problema di accesso non è dovuto a un errore di configurazione Azure AD.

Se non viene fornita alcuna soluzione per l'errore di accesso, è consigliabile usare la casella di testo feedback per segnalarlo.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Per risolvere l'errore senza installare l'App personali di accesso sicuro

1. Copiare il messaggio di errore nell'angolo inferiore destro della pagina. Il messaggio di errore include:
    - Un ID correlazione e il timestamp. Questi valori sono importanti quando si crea un caso di supporto con Microsoft, perché aiutano i tecnici a identificare il problema e a proporre una risoluzione precisa.
    - Una dichiarazione che identifica la causa radice del problema.
1. Tornare ad Azure AD e individuare il pannello **Test dell'accesso Single Sign-On**.
1. Nella casella di testo sopra **Ottieni procedure per la risoluzione** incollare il messaggio di errore.
1. Fare clic su **Ottieni procedure per la risoluzione** per visualizzare i passaggi per risolvere il problema. Le procedure fornite potrebbero richiedere informazioni dalla richiesta o dalla risposta SAML. Se non si usa l'App personali secure sign-in, potrebbe essere necessario uno strumento come [Fiddler](https://www.telerik.com/fiddler) per recuperare la richiesta e la risposta SAML.
1. Verificare che la destinazione nella richiesta SAML corrisponda all'URL del servizio Single Sign-On SAML ottenuto da Azure AD.
1. Verificare che l'autorità emittente nella richiesta SAML sia lo stesso identificatore configurato per l'applicazione in Azure AD. Azure AD usa l'emittente per trovare un'applicazione nella directory.
1. Verificare che AssertionConsumerServiceURL sia la posizione in cui l'applicazione si aspetta di ricevere il token SAML Azure AD. È possibile configurare questo valore in Azure AD, ma non è obbligatorio se fa parte della richiesta SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Risolvere un errore di accesso nella pagina dell'applicazione

Capita talvolta che si riesca a eseguire l'accesso correttamente ma che successivamente venga visualizzato un errore nella pagina dell'applicazione. Questa situazione si verifica quando Azure AD ha emesso un token per l'applicazione, ma questa non accetta la risposta.

Per risolvere l'errore, seguire questa procedura o guardare questo breve video su come usare Azure AD risolvere i problemi [di SAML SSO:](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)

1. Se l'applicazione si trova in Azure AD Gallery, verificare di aver seguito tutti i passaggi per l'integrazione dell'applicazione con Azure AD. Per trovare le istruzioni di integrazione per l'applicazione, vedere l'[elenco di esercitazioni sull'integrazione di applicazioni SaaS](../saas-apps/tutorial-list.md).
1. Recuperare la risposta SAML.
    - Se l'estensione My Apps Secure Sign-in è installata, nel pannello **Test dell'accesso Single Sign-On** fare clic su **Scaricare la richiesta SAML**.
    - Se l'estensione non è installata, usare uno strumento quale [Fiddler](https://www.telerik.com/fiddler) per recuperare la risposta SAML.
1. Nel token della risposta SAML sono presenti gli elementi seguenti:
   - Identificatore univoco di utente del valore NameID e formato
   - Attestazioni rilasciate nel token
   - Certificato usato per firmare il token.

     Per altre informazioni sulla risposta SAML, vedere [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Dopo aver esaminato la risposta SAML, vedere Errore nella pagina di [un'applicazione dopo l'accesso](application-sign-in-problem-application-error.md) per indicazioni su come risolvere il problema. 
1. Se non si è ancora in grado di accedere correttamente, è possibile chiedere al fornitore dell'applicazione cosa manca nella risposta SAML.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'accesso Single Sign-On funziona per l'applicazione, è possibile automatizzare il provisioning e il deproprotezione degli utenti in applicazioni [SaaS](../app-provisioning/user-provisioning.md) o iniziare a usare [l'accesso condizionale.](../conditional-access/app-based-conditional-access.md)
