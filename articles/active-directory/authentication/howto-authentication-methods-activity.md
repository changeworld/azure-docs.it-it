---
title: Attività Metodi di autenticazione - Azure Active Directory
description: Panoramica dei metodi di autenticazione registrati dagli utenti per accedere e reimpostare le password.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530457"
---
# <a name="authentication-methods-activity"></a>Attività dei metodi di autenticazione 

Il nuovo dashboard attività metodi di autenticazione consente agli amministratori di monitorare la registrazione e l'utilizzo dei metodi di autenticazione nell'intera organizzazione. Questa funzionalità di creazione di report offre all'organizzazione i mezzi per comprendere quali metodi vengono registrati e come vengono usati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

I ruoli predefiniti e personalizzati con le autorizzazioni seguenti possono accedere al pannello Attività metodi di autenticazione e alle API:

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

I ruoli seguenti dispongono delle autorizzazioni necessarie:

- Lettore di report
- Ruolo con autorizzazioni di lettura per la sicurezza
- Ruolo con autorizzazioni di lettura globali
- Operatore di sicurezza
- Amministratore della protezione
- Amministratore globale

 È Azure AD Premium una licenza P1 o P2 per accedere all'utilizzo e alle informazioni dettagliate. Azure AD informazioni sulle licenze di Multi-Factor Authentication e reimpostazione della password self-service (SSPR) sono disponibili nel sito Azure Active Directory [prezzi.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="how-it-works"></a>Funzionamento

Per accedere all'utilizzo e alle informazioni dettagliate del metodo di autenticazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare **clic Azure Active Directory** attività  >  **Metodi** di autenticazione  >  **di**  >  **sicurezza**.
1. Nel report sono presenti due schede: **Registrazione** e **Utilizzo**.

   ![Panoramica dell'attività Metodi di autenticazione](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Dettagli della registrazione

È possibile accedere alla [**scheda Registrazione per**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) visualizzare il numero di utenti in grado di eseguire l'autenticazione a più fattori, l'autenticazione senza password e la reimpostazione della password self-service. 

Fare clic su una delle opzioni seguenti per filtrare un elenco di dettagli di registrazione utente:

- **Gli utenti in grado di Azure Multi-Factor Authentication** mostrano la suddivisione degli utenti che sono entrambi:
  - Registrato per un metodo di autenticazione sicuro 
  - Abilitata dai criteri per l'uso di tale metodo per MFA 
  
  Questo numero non riflette gli utenti registrati per L'autenticazione a più fattori al di fuori Azure AD. 
- Gli utenti in grado di eseguire l'autenticazione senza **password** mostrano la suddivisione degli utenti registrati per l'accesso senza password usando l'app fiDO2, Windows Hello for Business o phone senza password con l'app Microsoft Authenticator. 
- **Gli utenti in grado di reimpostare la password self-service** mostrano la suddivisione degli utenti che possono reimpostare le password. Gli utenti possono reimpostare la password se sono entrambi:
  - Registrato per un numero sufficiente di metodi per soddisfare i criteri dell'organizzazione per la reimpostazione della password self-service 
  - Abilitato per reimpostare la password 

  ![Screenshot degli utenti che possono eseguire la registrazione](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Gli utenti registrati dal metodo di autenticazione** mostrano quanti utenti sono registrati per ogni metodo di autenticazione. Fare clic su un metodo di autenticazione per visualizzare chi è registrato per tale metodo.

![Screenshot degli utenti registrati](media/how-to-authentication-methods-usage-insights/users-registered.png)

**La registrazione recente in base al metodo di** autenticazione mostra il numero di registrazioni riuscite e non riuscite, ordinate in base al metodo di autenticazione. Fare clic su un metodo di autenticazione per visualizzare gli eventi di registrazione recenti per tale metodo.

![Screenshot di Registrazione recente](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Dettagli di utilizzo

Il report **Utilizzo** mostra i metodi di autenticazione usati per accedere e reimpostare le password.

![Screenshot della pagina Utilizzo](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Gli account di accesso** in base ai requisiti di autenticazione mostrano il numero di accesso interattivi utente riusciti necessari per l'autenticazione a fattore singolo e a più fattori in Azure AD. Gli accesso in cui è stata applicata l'autenticazione a più fattori da un provider MFA di terze parti non sono inclusi.

![Screenshot degli accesso in base al requisito di autenticazione](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Gli account di accesso tramite metodo di autenticazione** mostrano il numero di accesso interattivi dell'utente (esito positivo e negativo) in base al metodo di autenticazione usato. Non include gli accesso in cui il requisito di autenticazione è stato soddisfatto da un'attestazione nel token.

![Screenshot degli accesso in base al metodo](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Number of password resets and account unlocks** (Numero di reimpostazioni della password e sblocco dell'account) indica il numero di modifiche della password riuscite e reimpostazioni della password (self-service e da amministratore) nel tempo.

![Screenshot delle reimpostazioni e degli sblocchi](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Reimpostazioni delle password in base al metodo di autenticazione** indica il numero di autenticazioni riuscite e non riuscite durante il flusso di reimpostazione della password in base al metodo di autenticazione.

![Screenshot delle reimpostazioni in base al metodo](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Dettagli di registrazione utente 

Usando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

Il report dei dettagli di registrazione mostra le informazioni seguenti per ogni utente:

- Nome dell'entità utente
- Nome
- Idoneo per MFA (idoneo, non idoneo)
- Idoneo senza password (idoneo, non idoneo)
- SSPR registrato (registrato, non registrato)
- SSPR abilitata (abilitata, non abilitata)
- Idoneo per la SSPR (idoneo, non idoneo) 
- Metodi registrati (posta elettronica, telefono cellulare, telefono cellulare alternativo, telefono ufficio, Microsoft Authenticator push, passcode one-time software, FIDO2, chiave di sicurezza, domande di sicurezza)

  ![Screenshot dei dettagli di registrazione utente](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Eventi di registrazione e reimpostazione 

**Gli eventi di registrazione e reimpostazione** mostrano gli eventi di registrazione e reimpostazione delle ultime 24 ore, degli ultimi sette giorni o degli ultimi 30 giorni, tra cui:

- Data
- Nome utente
- User 
- Funzionalità (registrazione, reimpostazione)
- Metodo usato (notifica dell'app, codice dell'app, chiamata telefonica, chiamata dell'ufficio, chiamata mobile alternativa, SMS, posta elettronica, domande di sicurezza)
- Stato (operazione riuscita, esito negativo)
- Motivo dell'errore (spiegazione)

  ![Screenshot degli eventi di registrazione e reimpostazione](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limitazioni

- I dati nel report non vengono aggiornati in tempo reale e possono riflettere una latenza massima di alcune ore.
- Pass di accesso temporaneo registrazioni non vengono riflesse nella scheda registrazione del report perché sono valide solo per un breve periodo di tempo.
- I **metodi PhoneAppNotification** **o PhoneAppOTP** che un utente potrebbe aver configurato non vengono visualizzati nel dashboard. 

## <a name="next-steps"></a>Passaggi successivi

- [Uso dell'API del report sull'utilizzo dei metodi di autenticazione](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Scelta dei metodi di autenticazione per l'organizzazione](concept-authentication-methods.md)
- [Esperienza di registrazione combinata](concept-registration-mfa-sspr-combined.md)
