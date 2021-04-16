---
title: Metodi e funzionalità di autenticazione - Azure Active Directory
description: Informazioni sui diversi metodi e le diverse funzionalità di autenticazione disponibili in Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530431"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quali metodi di autenticazione e verifica sono disponibili in Azure Active Directory?

Nell'ambito dell'esperienza di accesso degli account in Azure Active Directory (Azure AD) è possibile eseguire l'autenticazione di un utente in modi diversi. Il modo più comune in cui un utente inserisce le credenziali è tradizionalmente l'uso di un nome utente e di una password. Con le moderne funzionalità di autenticazione e sicurezza Azure AD, tale password di base deve essere integrata o sostituita con metodi di autenticazione più sicuri.

![Tabella dei punti di forza e dei metodi di autenticazione preferiti in Azure AD](media/concept-authentication-methods/authentication-methods.png)

I metodi di autenticazione senza password Windows Hello, le chiavi di sicurezza FIDO2 e l'app Microsoft Authenticator forniscono gli eventi di accesso più sicuri.

Azure AD Multi-Factor Authentication (MFA) aggiunge ulteriore sicurezza solo usando una password quando un utente esegue l'accesso. All'utente possono essere richieste ulteriori forme di autenticazione, ad esempio per rispondere a una notifica push, immettere un codice da un token software o hardware o rispondere a un SMS o a una telefonata.

Per semplificare l'esperienza di on-boarding dell'utente e registrarsi sia per la reimpostazione della password self-service che per la reimpostazione della password self-service, è consigliabile abilitare la registrazione combinata [delle informazioni di sicurezza.](howto-registration-mfa-sspr-combined.md) Per la resilienza, è consigliabile richiedere agli utenti di registrare più metodi di autenticazione. Quando un metodo non è disponibile per un utente durante l'accesso o la SSPR, può scegliere di eseguire l'autenticazione con un altro metodo. Per altre informazioni, vedere Creare una strategia di gestione del controllo [di accesso resiliente in Azure AD](concept-resilient-controls.md).

Ecco un [video creato](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) per aiutare a scegliere il metodo di autenticazione migliore per proteggere l'organizzazione.

## <a name="authentication-method-strength-and-security"></a>Sicurezza e complessità del metodo di autenticazione

Quando si distribuiscono funzionalità come Azure AD Multi-Factor Authentication nell'organizzazione, esaminare i metodi di autenticazione disponibili. Scegliere i metodi che soddisfano o superano i requisiti in termini di sicurezza, usabilità e disponibilità. Laddove possibile, usare i metodi di autenticazione con il massimo livello di sicurezza.

Nella tabella seguente vengono illustrate le considerazioni sulla sicurezza per i metodi di autenticazione disponibili. La disponibilità indica che l'utente può usare il metodo di autenticazione, non la disponibilità del servizio Azure AD:

| Metodo di autenticazione          | Sicurezza | Usabilità | Disponibilità |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | Alto     | Alto      | Alto         |
| App Microsoft Authenticator    | Alto     | Alto      | Alto         |
| Chiave di sicurezza FIDO2             | Alto     | Alto      | Alto         |
| Token hardware OATH (anteprima) | Livello medio   | Livello medio    | Alto         |
| Token software OATH           | Livello medio   | Livello medio    | Alto         |
| SMS                            | Medio   | Alto      | Medio       |
| Chiamata vocale                          | Livello medio   | Livello medio    | Livello medio       |
| Password                       | Basso      | Alto      | Alto         |

Per le informazioni più recenti sulla sicurezza, vedere i post di blog:

- [È il momento di riagganciare i trasporti telefonici per l'autenticazione](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Vulnerabilità di autenticazione e vettori di attacco](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Per flessibilità e usabilità, è consigliabile usare l'app Microsoft Authenticator predefinita. Questo metodo di autenticazione offre la migliore esperienza utente e più modalità, ad esempio senza password, notifiche push MFA e codici OATH.

## <a name="how-each-authentication-method-works"></a>Funzionamento di ogni metodo di autenticazione

Alcuni metodi di autenticazione possono essere usati come fattore primario quando si accede a un'applicazione o a un dispositivo, ad esempio usando una chiave di sicurezza FIDO2 o una password. Altri metodi di autenticazione sono disponibili solo come fattore secondario quando si usa Azure AD Multi-Factor Authentication o SSPR.

La tabella seguente illustra quando è possibile usare un metodo di autenticazione durante un evento di accesso:

| Metodo                         | Autenticazione primaria | Autenticazione secondaria  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | Sì                    | MFA                       |
| App Microsoft Authenticator    | Sì                    | Autenticazione a più fattori e SSPR              |
| Chiave di sicurezza FIDO2             | Sì                    | MFA                       |
| Token hardware OATH (anteprima) | No                     | MFA                       |
| Token software OATH           | No                     | MFA                       |
| SMS                            | Sì                    | Autenticazione a più fattori e SSPR              |
| Chiamata vocale                     | No                     | Autenticazione a più fattori e SSPR              |
| Password                       | Sì                    |                           |

Tutti questi metodi di autenticazione possono essere configurati nel portale di Azure e sempre più usando [l'API REST](/graph/api/resources/authenticationmethods-overview)Microsoft Graph .

Per altre informazioni sul funzionamento di ogni metodo di autenticazione, vedere gli articoli concettuali seguenti:

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview) (Configurare Windows Hello for Business)
* [App Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Chiave di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
* [Token hardware OATH (anteprima)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Token software OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Accesso e verifica TRAMITE](howto-authentication-sms-signin.md) [SMS](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verifica delle chiamate vocali](concept-authentication-phone-options.md)
* Password

> [!NOTE]
> In Azure AD, una password è spesso uno dei metodi di autenticazione principali. Non è possibile disabilitare il metodo di autenticazione con password. Se si usa una password come fattore di autenticazione principale, aumentare la sicurezza degli eventi di accesso usando Azure AD Multi-Factor Authentication.

In determinati scenari è possibile usare i metodi di verifica aggiuntivi seguenti:

* [Password](howto-mfa-app-passwords.md) dell'app: usate per le applicazioni precedenti che non supportano l'autenticazione moderna e possono essere configurate per l'Azure AD Multi-Factor Authentication.
* [Domande di sicurezza:](concept-authentication-security-questions.md) usate solo per la SSPR
* [Indirizzo di posta elettronica:](concept-sspr-howitworks.md#authentication-methods) usato solo per la firma di accesso client

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti relativi all'autenticazione a più fattori, [Azure AD funzionamento di Multi-Factor Authentication.][concept-mfa]

Altre informazioni sulla configurazione dei metodi di autenticazione con [l Microsoft Graph aPI REST](/graph/api/resources/authenticationmethods-overview).

Per esaminare i metodi di autenticazione in uso, vedere l'Azure AD del metodo di autenticazione [Multi-Factor Authentication con PowerShell.](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
