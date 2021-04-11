---
title: Gestione del ruolo di lavoro Frontline-Azure Active Directory
description: Informazioni sulle funzionalità di gestione del ruolo di lavoro di Frontline fornite tramite il portale personale.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969241"
---
# <a name="frontline-worker-management"></a>Gestione del ruolo di lavoro Frontline

Un account per i ruoli di lavoro per il fronte del 80% della forza lavoro globale. Tuttavia, a causa di una scalabilità elevata, un rapido turnover e processi frammentati, i ruoli di lavoro di Frontline non hanno spesso gli strumenti necessari per semplificare i processi complessi. La gestione del ruolo di lavoro in Frontline porta la trasformazione digitale all'intera forza lavoro di Frontline. La forza lavoro può includere i responsabili, i ruoli di lavoro, le operazioni e l'IT.

La gestione del ruolo di lavoro in linea di base rende più semplice l'esecuzione delle attività seguenti:
- Semplificazione delle attività IT comuni con il personale
- Caricamento semplificato dei thread di lavoro con autenticazione semplificata
- Provisioning trasparente dei dispositivi condivisi e disconnessione sicura dei thread di lavoro

## <a name="delegated-user-management-through-my-staff"></a>Gestione degli utenti delegata tramite il personale

Azure Active Directory (Azure AD) offre la possibilità di delegare la gestione degli utenti ai responsabili di Frontline tramite il [portale personale](../roles/my-staff-configure.md), contribuendo a risparmiare tempo prezioso e a ridurre i rischi. Abilitando la reimpostazione delle password semplificata e la gestione telefonica direttamente dallo Store o dalla factory, i responsabili possono concedere l'accesso ai dipendenti senza instradare la richiesta tramite il supporto tecnico, l'IT o le operazioni.

![Gestione degli utenti delegata nel portale personale](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Onboarding accelerato con autenticazione semplificata

Il personale consente inoltre ai responsabili della Frontline di registrare i numeri di telefono dei membri del team per l' [accesso a SMS](../authentication/howto-authentication-sms-signin.md). In molti verticali, i lavoratori in prima linea gestiscono una combinazione di nome utente e password locale, una soluzione spesso complessa, costosa e soggetta a errori. Quando Abilita l'autenticazione tramite l'accesso SMS, i thread di lavoro possono accedere con [Single Sign-on (SSO)](../manage-apps/what-is-single-sign-on.md) per Microsoft teams e altre app usando solo il proprio numero di telefono e un codice di accesso monouso (OTP) inviato tramite SMS. In questo modo, è possibile accedere in modo semplice e sicuro ai thread di lavoro, consentendo un rapido accesso alle app di cui hanno bisogno.

![Accesso SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

I responsabili del Frontline possono anche usare l'applicazione della schermata iniziale gestita (MHS) per consentire ai dipendenti di accedere a un set specifico di applicazioni nei dispositivi Android dedicati registrati in Intune. I dispositivi dedicati vengono registrati con [Azure ad modalità dispositivo condiviso](../develop/msal-shared-devices.md). Quando è configurato in modalità tutto schermo per più app nella console di Microsoft Endpoint Manager (MEM), MHS viene avviato automaticamente come schermata iniziale predefinita nel dispositivo e viene visualizzato all'utente finale come *unica* schermata iniziale. Per altre informazioni, vedere come [configurare l'app Home Screen gestita da Microsoft per Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Disconnessione sicura dei thread di lavoro di Frontline da dispositivi condivisi

Molte aziende usano dispositivi condivisi, in modo che i thread di lavoro in linea possano eseguire la gestione dell'inventario e le transazioni POS, senza il carico di lavoro del provisioning e del rilevamento dei singoli dispositivi. Con la disconnessione dei dispositivi condivisi, è facile per un thread di lavoro per la disconnessione in modo sicuro da tutte le app in qualsiasi dispositivo condiviso prima di tornare a un hub o passarlo a un compagno di squadra al turno successivo. Microsoft teams è una delle app che è attualmente supportata nei dispositivi condivisi e consente ai lavoratori Frontline di visualizzare le attività a loro assegnate. Quando un thread di lavoro si disconnette da un dispositivo condiviso, Intune e Azure AD Cancella tutti i dati aziendali, in modo che il dispositivo possa essere passato in modo sicuro al prossimo associato. È possibile scegliere di integrare questa funzionalità in tutte le app line-of-business per [iOS](../develop/msal-ios-shared-devices.md) e [Android](../develop/msal-android-shared-devices.md) usando [Microsoft Authentication Library](../develop/msal-overview.md).

![Disconnessione di dispositivi condivisi](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla gestione degli utenti delegata, vedere [la documentazione personale dell'utente](../user-help/my-staff-team-manager.md).
- Per il provisioning utenti in ingresso da SAP SuccessFactors, vedere l'esercitazione sulla [configurazione di SAP SuccessFactors per Active Directory il provisioning degli utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Per il provisioning utenti in ingresso dalla giornata lavorativa, vedere l'esercitazione sulla [configurazione della giornata lavorativa per il provisioning utenti automatico](../saas-apps/workday-inbound-tutorial.md).
