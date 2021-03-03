---
title: SMS bidirezionale non più supportato-Azure Active Directory
description: Viene illustrato come abilitare un altro metodo per gli utenti che utilizzano ancora SMS bidirezionali.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689029"
---
# <a name="two-way-sms-unsupported"></a>SMS bidirezionale non supportato

SMS bidirezionale per il server Azure AD Multi-Factor Authentication (multi-factor authentication) è stato originariamente deprecato in 2018 e non è più supportato dopo il 24 febbraio 2021. Gli amministratori devono abilitare un altro metodo per gli utenti che usano ancora SMS bidirezionali.

Le notifiche di posta elettronica e le notifiche di integrità del servizio portale di Azure (toast del portale) sono state inviate agli amministratori interessati l'8 dicembre 2020 e il 28 gennaio 2021. Gli avvisi sono passati ai ruoli di proprietario, comproprietario, amministratore e controllo degli accessi in base al ruolo di amministratore del servizio associati alle sottoscrizioni. Se sono già stati completati i passaggi seguenti, non è necessaria alcuna azione.

## <a name="required-actions"></a>Azioni obbligatorie

1. Abilitare l'app per dispositivi mobili per gli utenti, se non è già stato fatto. Per altre informazioni, vedere [abilitare l'autenticazione di app per dispositivi mobili con server](howto-mfaserver-deploy-mobileapp.md)multi-factor authentication.
1. Inviare una notifica agli utenti finali per visitare il [portale utenti](howto-mfaserver-deploy-userportal.md) del server multi-factor authentication per attivare l'app per dispositivi mobili. L' [app Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) è l'opzione di verifica consigliata poiché è più sicura rispetto a SMS bidirezionali. Per ulteriori informazioni, vedere la pagina relativa [al tempo di blocco sui trasporti telefonici per l'autenticazione](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Modificare le impostazioni utente da SMS bidirezionale a app per dispositivi mobili come metodo predefinito.

## <a name="faq"></a>Domande frequenti

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Cosa accade se non si modifica il metodo predefinito da SMS bidirezionali all'app per dispositivi mobili?
Gli SMS bidirezionali hanno esito negativo dopo il 24 febbraio 2021. Gli utenti visualizzeranno un errore quando provano ad accedere e passano l'autenticazione a più fattori.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Ricerca per categorie modificare le impostazioni utente da un messaggio di testo bidirezionale all'app per dispositivi mobili?

È necessario modificare le impostazioni utente attenendosi alla procedura seguente:

1. In server multi-factor authentication filtrare l'elenco di utenti per l'SMS bidirezionale.
1. Selezionare tutti gli utenti.
1. Aprire la finestra di dialogo Modifica utenti.
1. Modificare gli utenti da un messaggio di testo all'app per dispositivi mobili.

   ![Screenshot degli utenti finali](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Gli utenti devono eseguire qualsiasi azione? In caso affermativo, come?
Sì. Gli utenti finali devono visitare il portale per gli utenti del server multi-factor authentication specifico per attivare l'app per dispositivi mobili, se non lo hanno già fatto. Dopo aver completato il passaggio 3, tutti gli utenti che non hanno visitato il portale per gli utenti per configurare l'app per dispositivi mobili inizieranno a non riuscire a eseguire l'accesso fino a quando non visitano il portale utenti per eseguire nuovamente la registrazione.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Cosa accade se gli utenti non riescono a installare l'app per dispositivi mobili? Quali altre opzioni hanno?
L'alternativa all'SMS bidirezionale o all'app per dispositivi mobili è una telefonata. Tuttavia, l'app Microsoft Authenticator è il metodo di verifica consigliato.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Anche gli SMS unidirezionali saranno deprecati?
No, solo SMS bidirezionale verrà deprecato. Per il server multi-factor authentication, SMS unidirezionale funziona per un subset di scenari:

- Adapter AD FS
- Autenticazione IIS (richiede il portale utenti e la configurazione)
- RADIUS (richiede che i client RADIUS supportino la richiesta di accesso e che venga usato il protocollo PAP)

Ci sono alcune limitazioni quando si può usare un SMS unidirezionale che rende l'app per dispositivi mobili un'alternativa migliore perché non richiede la richiesta del codice di verifica.
Se si vuole comunque usare un SMS unidirezionale in alcuni scenari, è possibile lasciare questi controlli, ma modificare la sezione **Impostazioni società** , l' **impostazione predefinita dell'utente** della scheda **generale** per il messaggio di testo a **unidirezionale** anziché **bidirezionale**. Infine, se si usa la sincronizzazione della directory che per impostazione predefinita è SMS, è necessario modificarla in One-Way anziché bidirezionale.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Come è possibile verificare quali utenti utilizzano ancora SMS bidirezionali?
Per elencare questi utenti, avviare il **Server** di autenticazione a più fattori, selezionare la sezione **utenti** , fare clic su **filtro elenco** utenti e filtrare per SMS a **due vie**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Come è possibile nascondere gli SMS bidirezionali come opzione nel portale di autenticazione a più fattori per impedire agli utenti di selezionarli in futuro?
Nel portale per gli utenti del server multi-factor authentication fare clic su **Impostazioni**. è **possibile deselezionare SMS** in modo che non sia disponibile. Lo stesso vale per la sezione **ad FS** se si usa ad FS per la registrazione utente.

