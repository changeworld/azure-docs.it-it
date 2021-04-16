---
title: Microsoft Authenticator metodo di autenticazione dell'app - Azure Active Directory
description: Informazioni sull'uso dell'app Microsoft Authenticator in Azure Active Directory per migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530503"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Metodi di autenticazione in Azure Active Directory - Microsoft Authenticator app

L'app Microsoft Authenticator offre un livello di sicurezza aggiuntivo per l'account Azure AD aziendale o dell'istituto di istruzione o l'account Microsoft ed è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS.](https://go.microsoft.com/fwlink/?linkid=866594) Con l'app Microsoft Authenticator, gli utenti possono eseguire l'autenticazione senza password durante l'accesso o come opzione di verifica aggiuntiva durante la reimpostazione della password self-service o gli eventi Azure AD Multi-Factor Authentication.

Gli utenti possono ricevere una notifica tramite l'app per dispositivi mobili per consentire loro di approvare o rifiutare o usare l'app Authenticator per generare un codice di verifica OAUTH che può essere immesso in un'interfaccia di accesso. Se si abilitano sia la notifica che il codice di verifica, gli utenti che registrano l'app Authenticator possono usare uno dei due metodi per verificare la propria identità.

Per usare l'app Authenticator a un prompt di accesso anziché una combinazione di nome utente e password, vedere Abilitare l'accesso senza password con [l'app Microsoft Authenticator.](howto-authentication-passwordless-phone.md)

> [!NOTE]
> Gli utenti non possono registrare l'app per dispositivi mobili quando abilitano la reimpostazione della password self-service. Possono invece registrarla all'indirizzo [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o come parte della registrazione delle informazioni di sicurezza combinate alla pagina [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

## <a name="passwordless-sign-in"></a>Accesso senza password

Invece di visualizzare una richiesta di password dopo aver immesso un nome utente, un utente che ha abilitato l'accesso tramite telefono dall'app Microsoft Authenticator visualizza un messaggio per toccare un numero nell'app. Quando viene selezionato il numero corretto, il processo di accesso è completato.

![Esempio di accesso del browser che chiede all'utente di approvare l'accesso](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Questo metodo di autenticazione offre un elevato livello di sicurezza ed elimina la necessità per l'utente di fornire una password all'accesso. 

Per iniziare a usare l'accesso senza password, vedere [Abilitare l'accesso senza password](howto-authentication-passwordless-phone.md)con l Microsoft Authenticator app .

## <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

L'app Authenticator consente di impedire l'accesso non autorizzato agli account e di arrestare le transazioni illecite eseguendo il push di una notifica allo smartphone o al tablet dell'utente. Gli utenti visualizzano la notifica e, se legittima, selezionano **Verifica**. Altrimenti possono selezionare **Nega**.

![Screenshot di un esempio di prompt del Web browser per la notifica dell'app Authenticator per completare il processo di accesso](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se il personale dell'organizzazione lavora o si sposta in Cina, il metodo Notifica tramite *app* per dispositivi mobili nei dispositivi Android non funziona in quel paese/area geografica perché Google Play Services (incluse le notifiche push) è bloccato nell'area. Tuttavia, la notifica iOS funziona correttamente. Per i dispositivi Android, i metodi di autenticazione alternativi devono essere resi disponibili per tali utenti.

## <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

L'app Authenticator può essere usata come token software per generare un codice di verifica OATH. Dopo aver inserito il nome utente e la password, si immette il codice inviato dall'app Authenticator nell'interfaccia di accesso. Il codice di verifica offre una seconda forma di autenticazione.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

> [!WARNING]
> Per garantire il livello di sicurezza più elevato per la reimpostazione della password self-service quando viene richiesto un solo metodo di ripristino, il codice di verifica rappresenta l'unica opzione disponibile per gli utenti.
>
> Quando vengono richiesti due metodi, gli utenti possono eseguire la reimpostazione usando la notifica o il codice di verifica, oltre a un altro metodo abilitato.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'accesso senza password, vedere [Abilitare l'accesso senza password](howto-authentication-passwordless-phone.md)con l Microsoft Authenticator app .

Altre informazioni sulla configurazione dei metodi di autenticazione con l Microsoft Graph aPI [REST.](/graph/api/resources/authenticationmethods-overview)
