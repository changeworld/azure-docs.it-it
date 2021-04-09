---
title: Attività metodi di autenticazione-Azure Active Directory
description: Panoramica dei metodi di autenticazione registrati dagli utenti per l'accesso e la reimpostazione delle password.
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
ms.openlocfilehash: 5a1cb71917fdb30ffccda21bedffe1c7f2a428c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557956"
---
# <a name="authentication-methods-activity"></a>Attività dei metodi di autenticazione 

Il dashboard attività nuovi metodi di autenticazione consente agli amministratori di monitorare la registrazione e l'utilizzo del metodo di autenticazione nell'intera organizzazione. Questa funzionalità di creazione di report consente all'organizzazione di individuare i metodi registrati e il modo in cui vengono usati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

I ruoli predefiniti e personalizzati con le autorizzazioni seguenti possono accedere al pannello attività metodi di autenticazione e alle API:

- Microsoft. directory/auditLogs/allProperties/Read
- Microsoft. directory/signInReports/allProperties/Read

I ruoli seguenti dispongono delle autorizzazioni necessarie:

- Lettore di report
- Ruolo con autorizzazioni di lettura per la sicurezza
- Ruolo con autorizzazioni di lettura globali
- Operatore di sicurezza
- Amministratore della protezione
- Amministratore globale

 Per accedere all'utilizzo e alle informazioni dettagliate, è necessaria una licenza Azure AD Premium P1 o P2. Azure AD Multi-Factor Authentication e le informazioni sulle licenze per la reimpostazione della password self-service (SSPR) sono disponibili nel [Azure Active Directory sito dei prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Funzionamento

Per accedere all'utilizzo del metodo di autenticazione e alle informazioni dettagliate:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **Azure Active Directory**  >    >  **attività metodi di autenticazione** di sicurezza  >  .
1. Nel report sono presenti due schede: **registrazione** e **utilizzo**.

   ![Cenni preliminari sull'attività metodi di autenticazione](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Dettagli registrazione

È possibile accedere alla [**scheda registrazione**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) per visualizzare il numero di utenti in grado di eseguire l'autenticazione a più fattori, l'autenticazione passowordless e la reimpostazione della password self-service. 

Fare clic su una delle opzioni seguenti per pre-filtrare un elenco di dettagli della registrazione dell'utente:

- **Gli utenti in grado di multi-factor authentication di Azure** mostrano la suddivisione degli utenti:
  - Registrato per un metodo di autenticazione avanzata 
  - Abilitata dal criterio per l'uso di tale metodo per l'autenticazione a più fattori 
  
  Questo numero non riflette gli utenti registrati per l'autenticazione a più fattori al di fuori della Azure AD. 
- **Gli utenti che supportano l'autenticazione senza password** mostrano la suddivisione degli utenti registrati per l'accesso senza password usando FIDO2, Windows Hello for business o l'accesso tramite telefono senza password con l'app Microsoft Authenticator. 
- **Gli utenti che supportano la reimpostazione della password self-service** mostrano la suddivisione degli utenti che possono reimpostare le password. Gli utenti possono reimpostare la password se sono entrambi:
  - Registrato per un numero sufficiente di metodi per soddisfare i criteri dell'organizzazione per la reimpostazione della password self-service 
  - Abilitato per reimpostare la password 

  ![Screenshot degli utenti che possono eseguire la registrazione](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Gli utenti registrati tramite il metodo di autenticazione** mostrano il numero di utenti registrati per ogni metodo di autenticazione. Fare clic su un metodo di autenticazione per vedere chi è registrato per il metodo.

![Screenshot degli utenti registrati](media/how-to-authentication-methods-usage-insights/users-registered.png)

La **registrazione recente tramite il metodo di autenticazione** Mostra il numero di registrazioni completate e non riuscite, ordinate per metodo di autenticazione. Fare clic su un metodo di autenticazione per visualizzare gli eventi di registrazione recenti per il metodo.

![Screenshot di registrato di recente](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Dettagli di utilizzo

Il report **sull'utilizzo** Mostra i metodi di autenticazione utilizzati per l'accesso e la reimpostazione delle password.

![Screenshot della pagina Usage](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Accessi in base al requisito di autenticazione** indica il numero di accessi interattivi utente riusciti necessari per l'autenticazione a fattore singolo e a più fattori in Azure ad. Gli accessi in cui l'autenticazione a più fattori è stata applicata da un provider di autenticazione a più fattori di terze parti non sono inclusi.

![Screenshot degli accessi in base al requisito di autenticazione](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Accessi in base al metodo di autenticazione** Mostra il numero di accessi interattivi utente (esito positivo e negativo) in base al metodo di autenticazione usato. Non include gli accessi in cui il requisito di autenticazione è stato soddisfatto da un'attestazione nel token.

![Screenshot di accessi per metodo](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

Il **numero di reimpostazioni della password e di sblocco dell'account** Mostra il numero di modifiche apportate alla password e le reimpostazioni della password (self-service e amministratore) riuscite nel corso del tempo.

![Schermata di reimpostazioni e sblocco](media/how-to-authentication-methods-usage-insights/password-changes.png)

Reimpostazione **password in base al metodo di autenticazione** Mostra il numero di autenticazioni riuscite e non riuscite durante il flusso di reimpostazione della password in base al metodo di autenticazione

![Screenshot della reimpostazione del metodo](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Dettagli registrazione utente 

Utilizzando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

Il report Dettagli registrazione Mostra le informazioni seguenti per ogni utente:

- Nome dell'entità utente
- Nome
- Autenticazione a più fattori (idonea, non supportata)
- Supporto senza password (idoneo, non supportato)
- SSPR registrato (registrato, non registrato)
- SSPR abilitato (abilitato, non abilitato)
- SSPR (idoneo, non supportato) 
- Metodi registrati (posta elettronica, telefono cellulare, telefono cellulare alternativo, telefono dell'ufficio, push Microsoft Authenticator, software monouso per il codice, FIDO2, chiave di sicurezza, domande di sicurezza)

  ![Screenshot dei dettagli di registrazione utente](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Eventi di registrazione e reimpostazione 

**Gli eventi di registrazione e reimpostazione** visualizzano eventi di registrazione e reimpostazione delle ultime 24 ore, degli ultimi sette giorni o degli ultimi 30 giorni, tra cui:

- Data
- Nome utente
- User 
- Funzionalità (registrazione, reimpostazione)
- Metodo usato (notifica dell'app, codice dell'app, telefonata, chiamata di Office, chiamata mobile alternativa, SMS, posta elettronica, domande di sicurezza)
- Stato (esito positivo, esito negativo)
- Motivo dell'errore (spiegazione)

  ![Screenshot degli eventi di registrazione e reimpostazione](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limitazioni

- I dati del report non vengono aggiornati in tempo reale e possono riflettere una latenza di poche ore.
- Le registrazioni di pass di accesso temporanee non vengono riflesse nella scheda registrazione del report perché sono valide solo per un breve periodo di tempo.
- I metodi **PhoneAppNotification** o **PhoneAppOTP** che un utente potrebbe avere configurato non vengono visualizzati nel dashboard. 

## <a name="next-steps"></a>Passaggi successivi

- [Utilizzo dell'API report utilizzo metodi di autenticazione](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Scelta dei metodi di autenticazione per l'organizzazione](concept-authentication-methods.md)
- [Esperienza di registrazione combinata](concept-registration-mfa-sspr-combined.md)
