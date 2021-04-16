---
title: Introduzione all'integrazione di Azure Active Directory con le app
description: Questo articolo è una guida introduttiva per l'integrazione di Azure Active Directory (AD) con applicazioni locali e applicazioni cloud.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374982"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni

Questo argomento riepiloga il processo per l'integrazione di applicazioni con Azure Active Directory (AD). Ciascuna delle sezioni seguenti contiene un breve riepilogo di un argomento più dettagliato, per consentire di identificare le parti di questa guida introduttiva rilevanti per la situazione specifica.

Per scaricare i piani di distribuzione dettagliati, vedere [Passaggi successivi](#next-steps).

## <a name="take-inventory"></a>Inventario
Prima di integrare applicazioni con Azure AD, è importante avere un quadro preciso della situazione attuale e degli obiettivi desiderati.  Le domande seguenti sono utili per valutare i diversi aspetti del progetto di integrazione delle applicazioni con Azure AD.

### <a name="application-inventory"></a>Inventario delle applicazioni
* Dove si trovano tutte le applicazioni? Quali sono i proprietari?
* Che tipo di autenticazione richiedono le applicazioni?
* Chi deve accedere a quali applicazioni?
* Si vuole distribuire una nuova applicazione?
  * L'applicazione verrà compilata internamente e distribuita in un'istanza di calcolo di Azure?
  * Si userà un'applicazione disponibile nella raccolta di applicazioni di Azure?

### <a name="user-and-group-inventory"></a>Inventario di utenti e gruppi
* Dove si trovano gli account utente?
  * Active Directory locale
  * Azure AD
  * In un database dell'applicazione separato di proprietà dell'utente
  * In applicazioni non approvate
  * Tutte le precedenti
* Quali autorizzazioni e assegnazioni di ruoli hanno attualmente i singoli utenti? È necessario verificarne l'accesso o si è sicuri che gli accessi utente e le assegnazioni dei ruoli siano appropriati per la situazione attuale?
* Sono già stati definiti gruppi in Active Directory locale?
  * In che modo sono organizzati i gruppi?
  * Chi sono i membri dei gruppi?
  * Quali autorizzazioni/assegnazioni di ruolo hanno attualmente i gruppi?
* Sarà necessario pulire i database di utenti/gruppi prima dell'integrazione?  Si tratta di una domanda importante. La qualità del risultato dipende dalla qualità dei dati di partenza.

### <a name="access-management-inventory"></a>Inventario della gestione degli accessi
* In che modo si gestisce attualmente l'accesso utente alle applicazioni? È necessario modificarlo?  Sono stati considerati altri modi per gestire l'accesso, ad esempio con il controllo degli accessi in base al ruolo di [Azure?](../../role-based-access-control/role-assignments-portal.md)
* Chi deve accedere a cosa?

È possibile che non siano immediatamente disponibili le risposte a tutte queste domande.  Questa guida è utile per rispondere ad alcune domande e prendere decisioni informate.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Ricercare applicazioni cloud non approvate con Cloud Discovery

Come indicato in precedenza, è possibile che alcune applicazioni non siano state gestite dall'organizzazione fino a oggi.  Come parte del processo di inventario, è possibile individuare applicazioni cloud non approvate. Vedere [Configurare Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrazione di applicazioni con Azure AD
Gli articoli seguenti illustrano i diversi modi in cui le applicazioni si integrano con Azure AD e forniscono alcune indicazioni.

* [Scelta del tipo di Active Directory da usare](../fundamentals/active-directory-whatis.md)
* [Uso delle applicazioni della raccolta di applicazioni di Azure](what-is-single-sign-on.md)
* [Elenco delle esercitazioni sull'integrazione di applicazioni SaaS](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Funzionalità per le app non elencate nella raccolta Azure AD app

È possibile aggiungere qualsiasi applicazione che esiste già nell'organizzazione o qualsiasi applicazione di terze parti di un fornitore che non fa parte della raccolta di Azure AD. A seconda del [contratto di licenza](https://azure.microsoft.com/pricing/details/active-directory/) stipulato, sono disponibili le funzionalità seguenti:

- Integrazione self-service di qualsiasi applicazione che supporta i provider di identità [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) (avviata dal provider di servizi o dal provider di identità)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](sso-options.md#password-based-sso)
- Connessione self-service di applicazioni che usano il [protocollo SCIM (System for Cross-domain Identity Management) per il provisioning utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'icona di avvio delle app di [Office 365](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) o [App personali](https://myapplications.microsoft.com/)

Per indicazioni per gli sviluppatori su come integrare app personalizzate con Azure AD, vedere Scenari di [autenticazione](../develop/authentication-vs-authorization.md)per Azure AD . Quando si sviluppa un'app che usa un protocollo moderno, ad esempio [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) per autenticare gli utenti, è possibile registrarlo con Microsoft Identity Platform usando l'esperienza [Registrazioni app](../develop/quickstart-register-app.md) nel portale di Azure.

### <a name="authentication-types"></a>Tipi di autenticazione
È possibile che ogni applicazione abbia requisiti di autenticazione diversi. Con Azure AD, i certificati di firma possono essere usati con applicazioni che usano SAML 2.0, WS-Federation o OpenID Connect Protocols e Password Single Sign-On. Per altre informazioni sui tipi di autenticazione delle applicazioni, vedere [Managing Certificates for Federated Single Sign-On in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) and Password based single sign [on](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Abilitazione di SSO con il proxy dell'app di Azure AD
Con il proxy di applicazione di Microsoft Azure AD è possibile garantire l'accesso ad applicazioni che si trovano all'interno della rete privata in modo sicuro da qualsiasi posizione e su qualsiasi dispositivo. Dopo aver installato un connettore del proxy di applicazione all'interno dell'ambiente, è possibile configurarlo facilmente con Azure AD.

### <a name="integrating-custom-applications"></a>Integrazione di applicazioni personalizzate
Se si vuole aggiungere l'applicazione personalizzata alla raccolta applicazione Azure, vedere Pubblicare l'app nella raccolta Azure AD [app.](../develop/v2-howto-app-gallery-listing.md)

## <a name="managing-access-to-applications"></a>Gestione dell'accesso alle applicazioni
Gli articoli seguenti illustrano i modi in cui è possibile gestire l'accesso alle applicazioni dopo l'integrazione con Azure AD mediante i connettori di Azure AD e Azure AD.

* [Gestione dell'accesso alle app tramite Azure AD](what-is-access-management.md)
* [Automazione con i connettori di Azure AD](../app-provisioning/user-provisioning.md)
* [Assegnazione di utenti a un'applicazione](./assign-user-or-group-access-portal.md)
* [Assegnazione di gruppi a un'applicazione](./assign-user-or-group-access-portal.md)
* [Condivisione di account](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate, è possibile scaricare i piani di distribuzione di Azure Active Directory da [GitHub](../fundamentals/active-directory-deployment-plans.md). Per le applicazioni della raccolta, è possibile scaricare i piani di distribuzione per l'accesso Single Sign-On, l'accesso condizionale e il provisioning utenti [tramite](https://portal.azure.com)portale di Azure .

Per scaricare un piano di distribuzione dal portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Applicazioni aziendali Selezionare** un piano di  |  **distribuzione** di  |  **app**.
