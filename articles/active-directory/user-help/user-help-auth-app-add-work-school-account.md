---
title: Aggiungere un account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator - Azure AD
description: Aggiungere l'account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator per verificare l'identità durante l'uso della verifica a due fattori.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359115"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Aggiungere un account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator

Se l'organizzazione usa la verifica a due fattori, è possibile impostare l'account aziendale o dell'istituto di istruzione in modo da usare l'app Microsoft Authenticator come uno dei metodi di verifica.

>[!Important]
>Prima di poter aggiungere l'account, è necessario scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, seguire i passaggi descritti nell'articolo [Scaricare e installare l'app](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Aggiungere l'account aziendale o dell'istituto di istruzione

È possibile aggiungere l'account aziendale o dell'Istituto di istruzione all'app Microsoft Authenticator eseguendo una delle operazioni seguenti:

- Credenziali Accedi con il tuo account aziendale o dell'istituto di istruzione (anteprima)
- Eseguire la scansione di un codice a matrice

### <a name="sign-in-with-your-credentials"></a>Accedere con le proprie credenziali

>[!Note]
>Questa funzionalità può essere usata solo dagli utenti i cui amministratori hanno abilitato l'accesso tramite telefono tramite l'app Authenticator.

Per aggiungere un account effettuando l'accesso all'account aziendale o dell'Istituto di istruzione usando le credenziali:

1. Aprire l'app Microsoft Authenticator e selezionare il **+** pulsante e toccare **Aggiungi account aziendale o dell'Istituto di istruzione**. Selezionare **Accedi**.

1. Immettere le credenziali dell'account aziendale o dell'Istituto di istruzione. Se si dispone di una sessione di accesso temporanea (TAP), è possibile usarla per accedere. A questo punto, è possibile che si possa impedire il proseguimento di una delle condizioni seguenti:

   - Se non si dispone di un numero sufficiente di metodi di autenticazione per l'account per ottenere un token di autenticazione sicuro, non sarà possibile procedere con l'aggiunta di un account.

   - Se si riceve il messaggio `You might be signing in from a location that is restricted by your admin` , l'utente viene bloccato ed è necessario un amministratore per sbloccare le [informazioni di sicurezza](https://mysignins.microsoft.com/security-info).

   - Se non si è bloccati per l'accesso tramite telefono tramite l'app Authenticator dall'amministratore, sarà possibile passare attraverso la registrazione del dispositivo per configurare l'accesso tramite telefono senza password e Azure Multi-Factor Authentication (multi-factor authentication).

1. A questo punto, è possibile che venga richiesto di eseguire la scansione di un codice a matrice fornito dall'organizzazione per configurare un account di autenticazione a più fattori locale nell'app. Questa operazione è necessaria solo se l'organizzazione usa un server di autenticazione a più fattori locale.

1. Nel dispositivo toccare l'account e verificare nella visualizzazione a schermo intero che l'account sia corretto e che sia presente un codice di verifica a sei cifre associato. Per una maggiore sicurezza, il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte.

## <a name="sign-in-with-a-qr-code"></a>Accedere con un codice a matrice

Per aggiungere un account eseguendo la scansione di un codice a matrice, procedere come segue:

1. Nel computer in uso andare alla pagina **Verifica aggiuntiva di sicurezza**.

   >[!Note]
   >Se non viene visualizzata la pagina **Verifica aggiuntiva di sicurezza**, è possibile che l'amministratore abbia attivato l'esperienza informazioni di sicurezza (anteprima). In tal caso è necessario seguire le istruzioni descritte nella sezione [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md). Se non è questo il caso, è necessario contattare l'help desk dell'organizzazione per ricevere assistenza. Per altre informazioni sulle informazioni di sicurezza, vedere [configurare le info di sicurezza da una richiesta di accesso](security-info-setup-signin.md).

1. Selezionare la casella di controllo accanto a app Authenticator, quindi selezionare **Configura**. Viene visualizzata la pagina **Configurare l'app per dispositivi mobili**.

   ![Schermata che fornisce un codice a matrice](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Aprire l'app Microsoft Authenticator, selezionare l'icona più ![ selezionare l'icona più nei dispositivi iOS o Android ](media/user-help-auth-app-add-work-school-account/plus-icon.png) e selezionare **Aggiungi account**, quindi selezionare account aziendale **o dell'Istituto di istruzione** e quindi **eseguire la scansione di un codice a** matrice.
   Se non si dispone di un account configurato nell'app Authenticator, verrà visualizzato un pulsante blu grande con la dicitura **Aggiungi account**.

Se non viene richiesto di usare la fotocamera per eseguire la scansione di un codice a matrice, nelle impostazioni del telefono assicurarsi che l'app Authenticator abbia accesso alla fotocamera del telefono.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver aggiunto gli account all'app, è possibile accedere usando l'app Authenticator nel dispositivo. Per altre informazioni, consultare [Accedere con l'app](user-help-auth-app-sign-in.md).

- Per i dispositivi che eseguono iOS, è anche possibile eseguire il backup nel cloud delle credenziali dell'account e delle relative impostazioni dell'app, ad esempio l'ordine degli account. Per altre informazioni, consultare [Eseguire il backup e il ripristino con l'app Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
