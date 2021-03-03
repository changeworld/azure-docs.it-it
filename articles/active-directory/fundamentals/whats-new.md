---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità con Azure Active Directory; quali le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c767d114ad6a1b5cb8b9c6bf4c0fe4f2ccd1dae6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650125"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Per ricevere notifiche che indicano che occorre visitare di nuovo questa pagina per visualizzare gli aggiornamenti, copiare e incollare questo URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` nel lettore di feed ![icona del lettore di feed RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si stanno cercando elementi più vecchi di sei mesi, è possibile trovarli in [Archivio per le novità di Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2021"></a>Gennaio 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Il token segreto sarà un campo obbligatorio durante la configurazione del provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

In passato, il campo token segreto poteva rimanere vuoto durante la configurazione del provisioning nell'applicazione personalizzata/BYOA. Questa funzione è stata progettata per essere utilizzata esclusivamente per i test. L'interfaccia utente verrà aggiornata in modo da rendere obbligatorio il campo. 

I clienti possono ovviare a questo requisito a scopo di test usando un flag di funzionalità nell'URL del browser. [Altre informazioni](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Anteprima pubblica: personalizzare e configurare i dispositivi condivisi Android per i prima riga Worker su larga scala

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
I team di Azure AD e Microsoft Endpoint Manager sono stati combinati per consentire la personalizzazione, la scalabilità e la protezione dei dispositivi prima riga Worker.

Le funzionalità di anteprima seguenti consentiranno di:
- Effettuare il provisioning di dispositivi condivisi Android su larga scala con Microsoft Endpoint Manager
- Proteggere l'accesso per i ruoli di lavoro Shift usando l'accesso condizionale basato su dispositivo
- Personalizzare le esperienze di accesso per i ruoli di lavoro di spostamento con la schermata iniziale gestita

Per altre informazioni, vedere [personalizzare e configurare i dispositivi condivisi per i prima riga Worker su larga scala](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Anteprima pubblica: è ora possibile scaricare i log di provisioning come CSV o JSON

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

I clienti possono scaricare i log di provisioning come file CSV o JSON tramite l'interfaccia utente e tramite l'API Graph. Per altre informazioni, vedere [report di provisioning nel portale di Azure Active Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Anteprima pubblica: assegnare gruppi di cloud a Azure AD ruoli personalizzati e ruoli con ambito unità amministrativa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
I clienti possono assegnare un gruppo cloud a Azure AD ruoli personalizzati o a un ruolo con ambito unità amministrativa. Per informazioni su come usare questa funzionalità, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Disponibilità generale-Azure AD Connect Cloud Sync (noto in precedenza come provisioning cloud)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Sincronizzazione Cloud Azure AD Connect  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Azure AD Connect sincronizzazione cloud è ora disponibile a livello generale per tutti i clienti.

Azure AD Connect Cloud sposta il carico elevato della logica di trasformazione nel cloud, riducendo il footprint locale. Sono inoltre disponibili più distribuzioni di agenti leggeri per una maggiore disponibilità di sincronizzazione. [Altre informazioni](https://aka.ms/cloudsyncGA)
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Disponibilità generale: ruoli predefiniti per l'amministratore della simulazione di attacco e l'autore del payload di attacco

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Sono disponibili due nuovi ruoli in Role-Based controllo di accesso per l'assegnazione a utenti, l'amministratore della simulazione di attacco e l'autore del payload di attacco. 

Gli utenti del ruolo [amministratore simulazione attacchi](../roles/permissions-reference.md#attack-simulation-administrator) hanno accesso a tutte le simulazioni nel tenant e possono:
- creare e gestire tutti gli aspetti della creazione della simulazione di attacco
- avvio/pianificazione di una simulazione
-  esaminare i risultati della simulazione. 

Gli utenti nel ruolo [autore del payload di attacchi](../roles/permissions-reference.md#attack-payload-author) possono creare payload di attacco ma non avviarli o pianificarli. I payload di attacco sono quindi disponibili per tutti gli amministratori del tenant che possono usarli per creare una simulazione.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Disponibilità generale-ruolo predefinito lettore report di riepilogo utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo lettore report di riepilogo utilizzo possono accedere ai dati aggregati a livello di tenant e alle informazioni dettagliate associate nel Microsoft 365 interfaccia di amministrazione per il Punteggio di utilizzo e produttività. Tuttavia, non possono accedere a dettagli o informazioni dettagliate a livello di utente. 

Nell'interfaccia di amministrazione di Microsoft 365 per i due report, si distingue tra i dati aggregati a livello di tenant e i dettagli a livello di utente. Questo ruolo aggiunge un ulteriore livello di protezione ai dati individuali identificabili dall'utente. [Altre informazioni](../roles/permissions-reference.md#usage-summary-reports-reader)

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Disponibilità generale: richiedere la concessione dei criteri di protezione delle app in Azure AD l'accesso condizionale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure AD concessione dell'accesso condizionale per "Richiedi i criteri di protezione delle app" è ora GA. 

Il criterio fornisce le seguenti funzionalità:
- Consente l'accesso solo quando si usa un'applicazione per dispositivi mobili che supporta la protezione app di Intune
- Consente l'accesso solo quando un utente dispone di un criterio di protezione delle app di Intune recapitato all'applicazione per dispositivi mobili

Per altre informazioni su come configurare un criterio di accesso condizionale per la protezione delle app, vedere [qui](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Disponibilità generale-codice di posta elettronica One-Time

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
OTP della posta elettronica consente alle organizzazioni di tutto il mondo di collaborare con chiunque inviando un collegamento o un invito tramite posta elettronica. Gli utenti invitati possono verificare la propria identità con il codice di accesso monouso inviato ai messaggi di posta elettronica per accedere alle risorse del partner. [Altre informazioni](../external-identities/one-time-passcode.md) 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2021 gennaio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Per altre informazioni, vedere [che cos'è il provisioning utenti di app Saas automatizzato in Azure ad?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2021 gennaio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel gennaio 2021 abbiamo aggiunto le seguenti 29 nuove applicazioni nella raccolta di app con supporto federativo:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue formation](https://formation.bienvenue.pro/login), [Aida Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO Dam](../saas-apps/ephoto-dam-tutorial.md), [noidea](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [urloo Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), Assigny [filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise ad Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [inpervertito Data Security](../saas-apps/imperva-data-security-tutorial.md), [illusoria reti](../saas-apps/illusive-networks-tutorial.md), [ProWare](../saas-apps/proware-tutorial.md), [splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Insight User Experience](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [perimetrale 81](../saas-apps/perimeter-81-tutorial.md), [rutto](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Anteprima pubblica: il responsabile di secondo livello può essere impostato come responsabile approvazione alternativo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Un'opzione aggiuntiva quando si seleziona responsabili approvazione è ora disponibile nella gestione dei diritti. Se si seleziona "responsabile come responsabile approvazione" per il primo responsabile approvazione, sarà disponibile un'altra opzione, ovvero "secondo livello gestione come responsabile approvazione alternativo", disponibile per scegliere nel campo responsabile approvazione alternativo. Se si seleziona questa opzione, è necessario aggiungere un responsabile approvazione del fallback per inviare la richiesta a nel caso in cui il sistema non riesca a trovare il gestore di secondo livello. [Scopri di più](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Disponibilità generale: accedere ai team direttamente dal portale di accesso personale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
È ora possibile avviare i team direttamente dal portale di accesso personale. 

A tale scopo, accedere a My Access ( https://myaccess.microsoft.com/) , passare a "Access Packages", quindi passare alla scheda "Active" per visualizzare tutti i pacchetti di accesso a cui si ha già accesso. Quando si espande il pacchetto di Access selezionato e si passa il mouse sui team, è possibile avviarlo facendo clic sul pulsante "Apri". [Altre informazioni](../governance/entitlement-management-request-access.md)
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>& di registrazione migliorata End-User richieste di utenti Guest rischiosi

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Sono state aggiornate le richieste di registrazione e End-User per gli utenti Guest rischiosi. Altre informazioni sono disponibili in [Identity Protection e utenti B2B](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Dicembre 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Anteprima pubblica: iscrizione e accesso Azure AD B2C Phone usando i criteri predefiniti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'iscrizione e l'accesso tramite telefono B2C con criteri predefiniti consentono agli amministratori IT e agli sviluppatori di organizzazioni di consentire agli utenti finali di effettuare l'accesso e di iscriversi usando un numero di telefono nei flussi utente. Per altre informazioni, vedere [configurare l'iscrizione e l'accesso tramite telefono per i flussi utente (anteprima)](../../active-directory-b2c/phone-authentication-user-flows.md) .

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Disponibilità generale: le impostazioni predefinite di sicurezza sono ora abilitate per tutti i nuovi tenant per impostazione predefinita

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Per proteggere gli account utente, tutti i nuovi tenant creati il 12 novembre 2020 o successivi verranno con i valori predefiniti di sicurezza abilitati. Le impostazioni predefinite di sicurezza applicano più criteri, tra cui:
- Richiede a tutti gli utenti e amministratori di registrarsi per l'autenticazione a più fattori usando l'app Microsoft Authenticator
- Richiede ruoli di amministratore critici per usare l'autenticazione a più fattori ogni volta che accedono. Tutti gli altri utenti verranno richiesti per l'autenticazione a più fattori ogni volta che è necessario. 
- L'autenticazione legacy verrà bloccata a livello di tenant. 

Per altre informazioni, vedere [quali sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Disponibilità generale-supporto per i gruppi con membri fino a 250K in AADConnect

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Microsoft ha distribuito un nuovo endpoint (API) per Azure AD Connect che consente di migliorare le prestazioni delle operazioni del servizio di sincronizzazione per Azure Active Directory. Quando si usa il nuovo [endpoint V2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), si riscontrano miglioramenti delle prestazioni evidenti per l'esportazione e l'importazione in Azure ad. Questo nuovo endpoint supporta gli scenari seguenti:

- Sincronizzazione di gruppi con membri fino a 250K
- Miglioramento delle prestazioni per l'esportazione e l'importazione in Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Disponibilità generale-gestione dei diritti disponibile per i tenant nel cloud di Azure Cina

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 

Le funzionalità di gestione dei diritti sono ora disponibili per tutti i tenant nel cloud di Azure China. Per informazioni, visitare il sito della [documentazione relativa alla governance delle identità](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 dicembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 dicembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel dicembre 2020 abbiamo aggiunto le 18 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB One Legal Holding](https://www.zylab.com/en/product/legal-hold), [guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [PIMS 365](http://www.omega365.com/pims), [InformaCast, RetrieverMediaDatabase](../saas-apps/informacast-tutorial.md) [, Vonage](../saas-apps/retrievermediadatabase-tutorial.md), [Count me in-Operations dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd della forza lavoro](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md) [, Shutterstock,](../saas-apps/shutterstock-tutorial.md) [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation app](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md) [](../saas-apps/vonage-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Passare a team direttamente dal portale di accesso personale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** **Funzionalità del prodotto** gestione accesso utenti: gestione dei diritti

È ora possibile avviare i team direttamente dal portale di accesso personale. A tale scopo, accedere a [accesso personale](https://myaccess.microsoft.com/), passare a accedere ai **pacchetti**, quindi passare alla scheda **attiva** per visualizzare tutti i pacchetti di accesso a cui si ha già accesso. Quando si espande il pacchetto di accesso e si passa il mouse sui team, è possibile avviarlo facendo clic sul pulsante **Apri** . 

Per altre informazioni sull'uso del portale di accesso personale, vedere [richiedere l'accesso a un pacchetto di accesso in Azure ad gestione dei diritti](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Anteprima pubblica: il responsabile di secondo livello può essere impostato come responsabile approvazione alternativo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti

Un'opzione aggiuntiva è ora disponibile nel processo di approvazione nella gestione dei diritti. Se si seleziona responsabile come responsabile approvazione per il primo responsabile approvazione, sarà disponibile un'altra opzione, ovvero un responsabile di secondo livello come responsabile approvazione alternativo, disponibile per la scelta nel campo responsabile approvazione alternativo. Quando si seleziona questa opzione, è necessario aggiungere un responsabile approvazione del fallback per inviare la richiesta a se il sistema non riesce a trovare il gestore di secondo livello.

Per ulteriori informazioni, vedere [modificare le impostazioni di approvazione per un pacchetto di accesso in Azure ad gestione dei diritti](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Novembre 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory la deprecazione di TLS 1,0, TLS 1,1 e 3DES

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte le applicazioni Azure AD  
**Funzionalità del prodotto:** Standard

Azure Active Directory depreca i protocolli seguenti in Azure Active Directory aree mondiali entro il 30 giugno 2021:

- TLS 1.0
- TLS 1.1
- pacchetto di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Gli ambienti interessati sono:
- Cloud commerciale di Azure
- Office 365 GCC e WW

Annuncio correlato tutte le combinazioni client-server e browser-server devono usare TLS 1,2 e i pacchetti di crittografia moderni per mantenere una connessione sicura a Azure Active Directory per Azure, Office 365 e Microsoft 365 Services. Questa modifica è correlata a [Azure Active Directory TLS 1,0 & 1,1 e alla deprecazione del pacchetto di crittografia 3DES in US gov cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 novembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel novembre 2020 abbiamo aggiunto le seguenti 52 nuove applicazioni nella raccolta di app con supporto federativo:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [tribeloo](../saas-apps/tribeloo-tutorial.md), [selettore file Itslearning](https://pmteam.itslearning.com/), [controllo Crisis](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum-Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [timu](../saas-apps/timu-tutorial.md), [guarder Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-Recruit](https://recruit.talview.com/login), Real time translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [ExpenseManager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [it-Trak tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [Eva check-in](https://my.evacheckin.com/organization), [HowNow webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [coupa Risk valutazione](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (tutti i prodotti)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), SailPoint [IDENTITYNOW](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md) [, ProdPad,](../saas-apps/prodpad-tutorial.md) [AWS Scarica](../saas-apps/aws-clientvpn-tutorial.md), [appsec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [bonifica cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signing](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md) [, Prolorus, Hirebridge](../saas-apps/prolorus-tutorial.md) [ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Anteprima pubblica: ruoli personalizzati per le app aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
 [I ruoli RBAC personalizzati per la gestione delle applicazioni aziendali delegate](../roles/custom-available-permissions.md) sono ora in anteprima pubblica. Queste nuove autorizzazioni si basano sui ruoli personalizzati per la gestione della registrazione delle app, che consente un controllo con granularità fine sugli accessi degli amministratori. Nel corso del tempo, verranno rilasciate autorizzazioni aggiuntive per delegare la gestione dei Azure AD.

Alcuni scenari di delega comuni:
- assegnazione di utenti e gruppi che possono accedere alle applicazioni Single Sign-On basate su SAML
- creazione di applicazioni della raccolta Azure AD
- aggiornamento e lettura delle configurazioni SAML di base per applicazioni Single Sign-On basate su SAML
- gestione dei certificati di firma per applicazioni Single Sign-On basate su SAML
- aggiornamento degli indirizzi di posta elettronica di notifica dei certificati per l'accesso in scadenza per le applicazioni Single Sign-On basate su SAML
- aggiornamento della firma del token SAML e dell'algoritmo di accesso per le applicazioni Single Sign-On basate su SAML
- creare, eliminare e aggiornare attributi utente e attestazioni per applicazioni Single Sign-On basate su SAML
- possibilità di attivare, disattivare e riavviare i processi di provisioning
- aggiornamenti al mapping degli attributi
- possibilità di leggere le impostazioni di provisioning associate all'oggetto
- possibilità di leggere le impostazioni di provisioning associate all'entità servizio
- possibilità di autorizzare l'accesso alle applicazioni per il provisioning

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Anteprima pubblica: il proxy di applicazione Azure AD supporta in modo nativo Single Sign-On l'accesso alle applicazioni che usano intestazioni per l'autenticazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Il proxy dell'applicazione Azure Active Directory (Azure AD) supporta in modo nativo Single Sign-On l'accesso alle applicazioni che usano intestazioni per l'autenticazione. È possibile configurare i valori di intestazione richiesti dall'applicazione in Azure AD. I valori dell'intestazione verranno inviati all'applicazione tramite il proxy di applicazione. Per altre informazioni, vedere [Single Sign-on basato su intestazione per le app locali con app Azure ad proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilità generale-iscrizione e accesso a Azure AD B2C Phone con criteri personalizzati

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Con l'iscrizione e l'accesso ai numeri di telefono, gli sviluppatori e le aziende possono consentire ai clienti di iscriversi e accedere usando una password monouso inviata al numero di telefono dell'utente tramite SMS. Questa funzionalità consente inoltre al cliente di modificare il numero di telefono se perde l'accesso al telefono. Grazie alla potenza dei criteri personalizzati, gli sviluppatori e le aziende possono comunicare il proprio marchio tramite la personalizzazione delle pagine. Informazioni su come [configurare l'iscrizione e l'accesso tramite telefono con criteri personalizzati in Azure ad B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 novembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blog in](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [TIC-TAC mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Per altre informazioni, vedere [automatizzare il provisioning degli utenti in applicazioni SaaS con Azure ad](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Anteprima pubblica: Sign-In di posta elettronica con ProxyAddresses ora distribuibile tramite implementazione temporanea

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli amministratori tenant possono ora usare l'implementazione temporanea per distribuire Sign-In di posta elettronica con ProxyAddresses a gruppi di Azure AD specifici. Questo può essere utile quando si prova la funzionalità prima di distribuirla nell'intero tenant tramite i criteri di individuazione dell'area di autenticazione principale. Le istruzioni per la distribuzione di Sign-In di posta elettronica con ProxyAddresses tramite l'implementazione temporanea sono disponibili nella [documentazione](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Anteprima limitata-diagnostica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Con la versione di anteprima iniziale della diagnostica di accesso, gli amministratori possono ora esaminare gli accessi degli utenti. Gli amministratori possono ricevere informazioni contestuali, specifiche e pertinenti e istruzioni su ciò che si è verificato durante l'accesso e su come risolvere i problemi. Il livello di diagnostica è disponibile sia nel pannello di Azure AD sia nella diagnostica e nell'accesso condizionale. Gli scenari di diagnostica descritti in questa versione sono l'accesso condizionale, la Multi-Factor Authentication e l'accesso riuscito.

Per ulteriori informazioni, vedere informazioni sulla [diagnostica di accesso in Azure ad](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Miglioramento delle proprietà di accesso non note

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

  Rilevamento delle proprietà di accesso non noto aggiornato. I clienti possono riscontrare rilevamenti delle proprietà di accesso non note più ad alto rischio. Per ulteriori informazioni, vedere [che cos'è il rischio?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>È ora disponibile l'aggiornamento dell'anteprima pubblica dell'agente di provisioning cloud (versione: 1.1.281.0)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di Azure AD cloud  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
L'agente di provisioning cloud è stato rilasciato in anteprima pubblica ed è ora disponibile tramite il portale. Questa versione contiene diversi miglioramenti, tra cui il supporto per GMSA per i domini, che offre una migliore sicurezza, cicli di sincronizzazione iniziale migliorati e supporto per gruppi di grandi dimensioni. Per altri dettagli, vedere la [cronologia](../app-provisioning/provisioning-agent-release-version-history.md) delle versioni di rilascio. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Endpoint API chiave di ripristino di BitLocker ora in/informationProtection

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
In precedenza era possibile ripristinare le chiavi BitLocker tramite l'endpoint/BitLocker. Alla fine verrà deprecato questo endpoint e i clienti devono iniziare a consumare l'API che ora rientra in/informationProtection. 

Per riflettere queste modifiche, vedere [API di ripristino di BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) per gli aggiornamenti della documentazione.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilità generale del supporto del proxy di applicazione per Servizi Desktop remoto client Web HTML5

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Il supporto del proxy di applicazione Azure AD per il client Web di Servizi Desktop remoto (RDS) è ora disponibile a livello generale. Il client Web RDS consente agli utenti di accedere a Desktop remoto infrastruttura tramite qualsiasi browser che supporta HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con le app Remote o con i desktop, come avviene con un dispositivo locale da qualsiasi luogo. 

Utilizzando Azure AD proxy di applicazione, è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto applicando i criteri di pre-autenticazione e di accesso condizionale per tutti i tipi di app rich client. Per altre informazioni, vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Il nuovo servizio Dynamic Group migliorato è in versione di anteprima pubblica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 
Il servizio Dynamic Group migliorato è ora disponibile in anteprima pubblica. I nuovi clienti che creano gruppi dinamici nei tenant utilizzeranno il nuovo servizio. Il tempo necessario per creare un gruppo dinamico sarà proporzionale alle dimensioni del gruppo che viene creato al posto delle dimensioni del tenant. Questo aggiornamento migliorerà significativamente le prestazioni per i tenant di grandi dimensioni quando i clienti creeranno gruppi più piccoli. 

Il nuovo servizio mira anche a completare l'aggiunta e la rimozione dei membri a causa di modifiche degli attributi entro pochi minuti. Inoltre, i singoli errori di elaborazione non bloccano l'elaborazione del tenant. Per ulteriori informazioni sulla creazione di gruppi dinamici, vedere la [documentazione](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Ottobre 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD agenti ibridi locali interessati dalle modifiche ai certificati TLS di Azure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Questo aggiornamento è dovuto ai certificati della CA correnti non conformi a uno dei requisiti di base del forum CA/browser. Questa modifica influirà Azure AD gli agenti ibridi installati in locale che hanno ambienti finalizzati con un elenco fisso di certificati radice e dovranno essere aggiornati per considerare attendibile le nuove autorità emittenti di certificati.

Questa modifica provocherà un'interferenza del servizio se non si esegue immediatamente un'azione. Questi agenti includono i [connettori del proxy di applicazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto agli agenti di [autenticazione pass-through](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) locali che consentono agli utenti di accedere alle applicazioni usando le stesse password e gli agenti di anteprima del [provisioning del cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che eseguono ad Azure ad la sincronizzazione. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo per il download di un elenco di revoche di certificati (CRL) specifico, sarà necessario consentire i seguenti URL CRL e OCSP. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP per consentire l'accesso a, vedere  [modifiche ai certificati TLS di Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Gli eventi di provisioning verranno rimossi dai log di controllo e pubblicati esclusivamente nei log di provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
L'attività da parte del [servizio di provisioning](../app-provisioning/user-provisioning.md) scim viene registrata sia nei log di controllo che nei log di provisioning. Sono incluse attività quali la creazione di un utente in ServiceNow, il gruppo in GSuite o l'importazione di un ruolo da AWS. In futuro, questi eventi verranno pubblicati solo nei log di provisioning. Questa modifica viene implementata per evitare eventi duplicati nei log e costi aggiuntivi sostenuti dai clienti che utilizzano i log in log Analytics. 

Al termine di una data verrà fornito un aggiornamento. Questa deprecazione non è pianificata per l'anno di calendario 2020. 

> [!NOTE]
> Questa operazione non influisca sugli eventi nei log di controllo al di fuori degli eventi di sincronizzazione generati dal servizio di provisioning. Gli eventi come la creazione di un'applicazione, i criteri di accesso condizionale, un utente nella directory e così via continueranno a essere generati nei log di controllo. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD agenti ibridi locali interessati dalle modifiche ai certificati di Azure Transport Layer Security (TLS)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma
 
Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Verrà aggiornato a causa dei certificati correnti della CA che non seguono uno dei requisiti di base del forum CA/browser. Questa modifica avrà un effetto Azure AD gli agenti ibridi installati in locale che hanno ambienti con protezione avanzata con un elenco fisso di certificati radice. Questi agenti dovranno essere aggiornati per considerare attendibili le nuove autorità di certificazione.

Questa modifica provocherà un'interferenza del servizio se non si esegue immediatamente un'azione. Questi agenti includono: 
- [Connettori del proxy di applicazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto a locale 
- Agenti di [autenticazione pass-through](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che consentono agli utenti di accedere alle applicazioni usando le stesse password
- Gli agenti di [anteprima del provisioning nel cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che eseguono ad Azure ad la sincronizzazione. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo per il download di un elenco di revoche di certificati (CRL) specifico, è necessario consentire CRL e URL OCSP. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP per consentire l'accesso a, vedere  [modifiche ai certificati TLS di Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory la deprecazione di TLS 1,0, TLS 1,1 e 3DES in US Gov cloud

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte le applicazioni Azure AD  
**Funzionalità del prodotto:** Standard
 
Azure Active Directory depreca i protocolli seguenti entro il 31 marzo 2021:
- TLS 1.0
- TLS 1.1
- pacchetto di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Tutte le combinazioni client-server e browser-server devono usare TLS 1,2 e i pacchetti di crittografia moderni per mantenere una connessione sicura a Azure Active Directory per Azure, Office 365 e Microsoft 365 Services.

Gli ambienti interessati sono:
- US Gov di Azure
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Assegnare le applicazioni ai ruoli in AU e nell'ambito dell'oggetto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Questa funzionalità consente di assegnare un'applicazione (SPN) a un ruolo di amministratore nell'ambito dell'unità amministrativa. Per altre informazioni, vedere [assegnare ruoli con ambito a un'unità amministrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>A questo punto è possibile disabilitare ed eliminare gli utenti Guest quando viene negato l'accesso a una risorsa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
Disable ed Delete è un controllo avanzato delle verifiche di accesso Azure AD per aiutare le organizzazioni a gestire al meglio gli utenti esterni in gruppi e app. Se Guest viene negato in una verifica di accesso, **Disable ed Delete** li bloccherà automaticamente per 30 giorni. Dopo 30 giorni, verranno rimossi completamente dal tenant.

Per altre informazioni su questa funzionalità, vedere [disabilitare ed eliminare identità esterne con le verifiche di accesso Azure ad (anteprima)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Gli autori della verifica di accesso possono aggiungere messaggi personalizzati nei messaggi di posta elettronica ai revisori

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
In Azure AD verifiche di accesso, gli amministratori che creano le recensioni possono ora scrivere un messaggio personalizzato nei revisori. I revisori visualizzeranno il messaggio nel messaggio di posta elettronica ricevuto che richiede di completare la revisione. Per altre informazioni sull'uso di questa funzionalità, vedere il passaggio 14 della sezione [creare una o più](../governance/create-access-review.md#create-one-or-more-access-reviews) verifiche di accesso.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-ottobre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integration Assistant per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza di Integration Assistant (anteprima) è ora disponibile per Azure AD B2C Registrazioni app. Questa esperienza consente di configurare l'applicazione per scenari comuni. Altre informazioni sulle procedure consigliate e sulle raccomandazioni per la [piattaforma di identità Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Visualizzare l'ID del modello di ruolo nell'interfaccia utente portale di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 

È ora possibile visualizzare l'ID modello di ogni ruolo Azure AD nel portale di Azure. In Azure AD selezionare  **Descrizione** del ruolo selezionato. 

È consigliabile che i clienti usino gli ID dei modelli di ruolo nel codice e nello script di PowerShell, anziché il nome visualizzato. L'ID del modello di ruolo è supportato per l'uso degli oggetti [directoryRoles](/graph/api/resources/directoryrole) e [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Per ulteriori informazioni sugli ID dei modelli di ruolo, vedere [ID dei modelli di ruolo](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>I connettori API per Azure AD B2C i flussi utente di iscrizione sono ora in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 

I connettori API sono ora disponibili per l'uso con Azure Active Directory B2C. I connettori API consentono di usare le API Web per personalizzare i flussi utente di iscrizione e l'integrazione con i sistemi cloud esterni. È possibile usare i connettori API per:

- Integrazione con flussi di lavoro di approvazione personalizzati
- Convalida dati di input utente
- Sovrascrivi attributi utente 
- Eseguire la logica di business personalizzata 

 Per altre informazioni, vedere [usare i connettori API per personalizzare ed estendere](../../active-directory-b2c/api-connectors-overview.md) la documentazione di iscrizione.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Proprietà state per le organizzazioni connesse nella gestione dei diritti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** per la gestione delle directory: gestione dei diritti
 

 Tutte le organizzazioni connesse avranno ora una proprietà aggiuntiva denominata "state". Lo stato controllerà il modo in cui l'organizzazione connessa verrà usata nei criteri che fanno riferimento a "tutte le organizzazioni connesse configurate". Il valore sarà "configurata" (ovvero l'organizzazione è nell'ambito dei criteri che usano la clausola "All") o "proposed", ovvero l'organizzazione non è nell'ambito.  

Le organizzazioni connesse create manualmente avranno un'impostazione predefinita "configurata". Nel frattempo, gli utenti creati automaticamente (creati tramite criteri che consentono a qualsiasi utente da Internet di richiedere l'accesso) verranno impostati per impostazione predefinita su "proposto".  Tutte le organizzazioni connesse create prima del 9 2020 settembre verranno impostate su "configurate". Gli amministratori possono aggiornare questa proprietà in base alle esigenze. [Altre informazioni](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory le identità esterne hanno ora impostazioni di sicurezza avanzate Premium per B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di accesso condizionale e di rilevamento dei rischi basate sul rischio di Identity Protection sono ora disponibili in [Azure ad B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Con queste funzionalità di sicurezza avanzate, i clienti possono ora:
- Usare Intelligent Insights per valutare i rischi con le app B2C e gli account degli utenti finali. I rilevamenti includono viaggi atipici, indirizzi IP anonimi, indirizzi IP collegati al malware e Azure AD Intelligence per le minacce. Sono disponibili anche i report basati sull'API e sul portale.
- Risolvere automaticamente i rischi configurando criteri di autenticazione adattiva per gli utenti B2C. Gli sviluppatori e gli amministratori di app possono attenuare i rischi in tempo reale richiedendo l'autenticazione a più fattori o bloccando l'accesso a seconda del livello di rischio utente rilevato, con controlli aggiuntivi disponibili in base alla posizione, al gruppo e all'app.
- Integrazione con Azure AD B2C flussi utente e criteri personalizzati. Le condizioni possono essere attivate da flussi utente predefiniti in Azure AD B2C o possono essere incorporati in criteri personalizzati B2C. Come per gli altri aspetti del flusso utente B2C, la messaggistica dell'esperienza utente finale può essere personalizzata. La personalizzazione è secondo le alternative per la voce, il marchio e la mitigazione dell'organizzazione.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-ottobre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel 2020 ottobre sono state aggiunte le seguenti 27 nuove applicazioni nella raccolta di app con supporto federativo:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ecoChallenge.org](https://events.ecochallenge.org/users/login), [AtSpoke](http://atspoke.com/login), [appuntamento promemoria](https://app.appointmentreminder.co.nz/account/login), [cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [loop flow CRM](../saas-apps/loop-flow-crm-tutorial.md), Starmind [, Workstem,](https://hrm.workstem.com/login) [retail Zipline](../saas-apps/retail-zipline-tutorial.md) [, Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) [](../saas-apps/starmind-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>È ora possibile trasmettere i log di provisioning a log Analytics

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

Pubblicare i log di provisioning in log Analytics per:
- Archiviare i log di provisioning per più di 30 giorni
- Definire avvisi e notifiche personalizzati
- Creare dashboard per visualizzare i log
- Eseguire query complesse per analizzare i log 

Per informazioni su come usare la funzionalità, vedere comprendere il modo in cui il [provisioning si integra con i log di monitoraggio di Azure](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>I log di provisioning possono ora essere visualizzati dai proprietari delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È ora possibile consentire ai proprietari delle applicazioni di monitorare l'attività del servizio di provisioning e risolvere i problemi senza fornire loro un ruolo privilegiato o crearne un collo di bottiglia. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md)
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Ridenominazione di 10 ruoli Azure Active Directory

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 
Alcuni ruoli predefiniti di Azure Active Directory (AD) hanno nomi diversi da quelli visualizzati nell'interfaccia di amministrazione di Microsoft 365, nel portale di Azure AD e Microsoft Graph. Questa incoerenza può causare problemi nei processi automatizzati. Con questo aggiornamento, verranno rinominati 10 nomi di ruolo per renderli coerenti. Nella tabella seguente sono presenti i nuovi nomi di ruolo:

![Tabella dei nuovi nomi di ruolo](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Supporto di Azure AD B2C per il flusso del codice di autenticazione per le spa con MSAL JS 2. x

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
MSAL.js versione 2. x ora include il supporto per il flusso del codice di autorizzazione per le app Web a pagina singola (Spa). Azure AD B2C supporta ora l'uso del tipo di app SPA sul portale di Azure e l'uso del flusso del codice di autorizzazione MSAL.js con PKCE per le app a singola pagina. Questo consentirà alle Spa di usare Azure AD B2C per gestire l'accesso SSO con browser più recenti e rispettare le raccomandazioni del protocollo di autenticazione più recenti. Introduzione alla registrazione di [un'applicazione a singola pagina (Spa) in Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) esercitazione.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aggiornamenti da ricordare Multi-Factor Authentication (multi-factor authentication) in un'impostazione attendibile del dispositivo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Recentemente è stato aggiornato il [ricordare multi-factor authentication (](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) multi-factor authentication) in una funzionalità del dispositivo attendibile per estendere l'autenticazione per un massimo di 365 giorni. Le licenze Azure Active Directory (Azure AD) Premium possono anche usare i [criteri di frequenza di accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) , che offrono maggiore flessibilità per le impostazioni di riautenticazione.

Per un'esperienza utente ottimale, è consigliabile usare la frequenza di accesso con accesso condizionale per estendere le durate della sessione su dispositivi attendibili, posizioni o sessioni a basso rischio come alternativa all'autenticazione a più fattori in un'impostazione attendibile del dispositivo. Per iniziare, esaminare [le linee guida più recenti per ottimizzare l'esperienza di riautenticazione](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Settembre 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Sensibilizzazione sulla sicurezza di Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Aggiornamento dell'anteprima pubblica del provisioning cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD **funzionalità del prodotto** per il provisioning cloud: gestione del ciclo di vita delle identità
 
Azure AD Connect aggiornamento dell'anteprima pubblica del provisioning nel cloud offre due miglioramenti principali sviluppati dai commenti dei clienti: 

- Esperienza di mapping degli attributi tramite portale di Azure

    Con questa funzionalità gli amministratori IT possono eseguire il mapping degli attributi utente, gruppo o contatto da AD a Azure AD usando diversi tipi di mapping attualmente presenti. Il mapping degli attributi è una funzionalità usata per standardizzare i valori degli attributi che fluiscono da Active Directory a Azure Active Directory. È possibile determinare se eseguire direttamente il mapping del valore dell'attributo come da AD a Azure AD o utilizzare espressioni per trasformare i valori dell'attributo durante il provisioning degli utenti. [Scopri di più](../cloud-sync/how-to-attribute-mapping.md)

- Esperienza utente di test o provisioning su richiesta

    Dopo aver configurato la configurazione, è possibile eseguire un test per verificare se la trasformazione utente funziona come previsto prima di applicarla a tutti gli utenti nell'ambito. Con il provisioning su richiesta, gli amministratori IT possono immettere il nome distinto (DN) di un utente di Active Directory e verificare se vengono sincronizzati come previsto. Il provisioning su richiesta offre un ottimo modo per garantire che i mapping degli attributi in precedenza funzionino come previsto. [Altre informazioni](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Ripristino di BitLocker controllato in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
Quando gli amministratori IT o gli utenti finali leggono le chiavi di ripristino di BitLocker a cui hanno accesso, Azure Active Directory ora genera un log di controllo che acquisisce chi ha eseguito l'accesso alla chiave di ripristino. Lo stesso controllo fornisce i dettagli del dispositivo a cui è stata associata la chiave BitLocker.

Gli utenti finali possono [accedere alle chiavi di ripristino tramite il mio account](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Gli amministratori IT possono accedere alle chiavi di ripristino tramite l' [API della chiave di ripristino di BitLocker in beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) o tramite il portale di Azure ad. Per altre informazioni, vedere [visualizzare o copiare le chiavi BitLocker nel portale di Azure ad](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Ruolo predefinito amministratore dispositivi Teams

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo di [amministratore dei dispositivi team](../roles/permissions-reference.md#teams-devices-administrator) possono gestire i [dispositivi certificati](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) dai team dall'interfaccia di amministrazione dei team. 

Questo ruolo consente all'utente di visualizzare tutti i dispositivi a colpo d'occhio, con la possibilità di cercare e filtrare i dispositivi. L'utente può anche controllare i dettagli di ogni dispositivo, inclusi l'account connesso e la marca e il modello del dispositivo. L'utente può modificare le impostazioni del dispositivo e aggiornare le versioni del software. Questo ruolo non concede le autorizzazioni per controllare l'attività dei team e la qualità della chiamata del dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Funzionalità di query avanzate per oggetti directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Tutte le nuove funzionalità di query introdotte per gli oggetti directory in API Azure AD sono ora disponibili nell'endpoint v 1.0 e pronto per la produzione. Gli sviluppatori possono contare, cercare, filtrare e ordinare gli oggetti directory e i collegamenti correlati usando gli operatori OData standard.

Per altre informazioni, vedere la documentazione [qui](https://aka.ms/BlogPostMezzoGA)ed è anche possibile inviare commenti e suggerimenti con questo [breve sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Anteprima pubblica: valutazione di accesso continuo per i tenant che hanno configurato i criteri di accesso condizionale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
La versione di valutazione dell'accesso continuo (CAE) è ora disponibile in anteprima pubblica per Azure AD tenant con criteri di accesso condizionale. Con CAE, gli eventi e i criteri di sicurezza critici vengono valutati in tempo reale. Sono incluse la disabilitazione dell'account, la reimpostazione della password e la modifica della posizione. Per altre informazioni, vedere [valutazione dell'accesso continuo](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Anteprima pubblica: chiedere agli utenti di richiedere un pacchetto di accesso domande aggiuntive per migliorare le decisioni relative all'approvazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Gli amministratori possono ora richiedere che gli utenti che richiedono un pacchetto di accesso rispondano a domande aggiuntive oltre alla giustificazione aziendale nel portale di accesso personale della gestione dei diritti Azure AD. Le risposte degli utenti verranno quindi visualizzate agli approvatori per consentire loro di prendere una decisione di approvazione dell'accesso più accurata. Per ulteriori informazioni, vedere [raccogliere ulteriori informazioni sul richiedente per l'approvazione (anteprima)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Anteprima pubblica: gestione avanzata degli utenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Gestione utenti
 

Il portale Azure AD è stato aggiornato per semplificare la ricerca degli utenti nelle pagine tutti gli utenti e utenti eliminati. Le modifiche nell'anteprima includono: 
- Proprietà utente più visibili, tra cui ID oggetto, stato di sincronizzazione della directory, tipo di creazione ed emittente di identità.
- La ricerca consente ora la ricerca combinata di nomi, messaggi di posta elettronica e ID oggetto.
- Filtro migliorato per tipo di utente (membro, Guest e nessuno), stato di sincronizzazione della directory, tipo di creazione, nome della società e nome di dominio.
- Nuove funzionalità di ordinamento per proprietà quali nome, nome dell'entità utente e data di eliminazione.
- Un nuovo numero totale di utenti che viene aggiornato con qualsiasi ricerca o filtro.

Per ulteriori informazioni, vedere la pagina relativa ai miglioramenti apportati [alla gestione degli utenti (anteprima) in Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nuovo campo note per le applicazioni aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** app aziendali: SSO

È possibile aggiungere note di testo gratuite alle applicazioni aziendali. È possibile aggiungere tutte le informazioni rilevanti che consentiranno di dirigere le applicazioni in applicazioni aziendali. Per altre informazioni, vedere [Guida introduttiva: configurare le proprietà di un'applicazione nel tenant di Azure Active Directory (Azure ad)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel settembre 2020 abbiamo aggiunto le seguenti 34 nuove applicazioni nella raccolta di app con supporto federativo:

[VMware Horizon-Unified Access Gateway](), [PULSe Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [luccichi Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [grammatical](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), Saviynt [, BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup) [mobile Locker](../saas-apps/mobile-locker-tutorial.md), [ZEngine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [FEXA](../saas-apps/fexa-tutorial.md), [firma protetta Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [firma protetta Enterprise Portal installazione di AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [WISTEC online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft-protetto da F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [](../saas-apps/saviynt-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui: https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella raccolta di app Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nuovo ruolo di delega nella gestione dei diritti Azure AD: Access Package Assignment Manager

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Un nuovo ruolo gestione assegnazione pacchetti di accesso è stato aggiunto in Azure AD gestione dei diritti per fornire autorizzazioni granulari per gestire le assegnazioni. È ora possibile delegare le attività a un utente in questo ruolo, che può delegare le assegnazioni di gestione di un pacchetto di accesso a un proprietario dell'azienda. Tuttavia, un gestore di assegnazione dei pacchetti di accesso non può modificare i criteri del pacchetto di accesso o altre proprietà impostate dagli amministratori. 

Con questo nuovo ruolo, si traggono vantaggio dai privilegi minimi necessari per delegare la gestione delle assegnazioni e mantenere il controllo amministrativo su tutte le altre configurazioni dei pacchetti di accesso. Per altre informazioni, vedere [ruoli di gestione dei diritti](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Modifiche al flusso di onboarding di Privileged Identity Management

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
In precedenza, l'onboarding in Privileged Identity Management (PIM) richiedeva il consenso dell'utente e un flusso di onboarding nel pannello di PIM che includeva la registrazione in Azure AD multi-factor authentication. Con la recente integrazione dell'esperienza PIM nel pannello ruoli e amministratori Azure AD, questa esperienza verrà rimossa. Qualsiasi tenant con licenza P2 valida verrà caricato automaticamente in PIM.

Il caricamento in PIM non ha alcun effetto negativo diretto sul tenant. È possibile prevedere le seguenti modifiche:
- Opzioni di assegnazione aggiuntive, ad esempio Active vs. idonee con l'ora di inizio e di fine quando si effettua un'assegnazione nel pannello ruoli e amministratori PIM o Azure AD. 
- Meccanismi di ambito aggiuntivi, come le unità amministrative e i ruoli personalizzati, introdotti direttamente nell'esperienza di assegnazione. 
- Se si è un amministratore globale o un amministratore del ruolo con privilegi, è possibile iniziare a ricevere alcuni messaggi di posta elettronica aggiuntivi come il digest settimanale di PIM. 
- È anche possibile vedere l'entità servizio MS-PIM nel log di controllo relativa all'assegnazione di ruolo. Questa modifica prevista non influirà sul normale flusso di lavoro.

 Per altre informazioni, vedere [iniziare a usare Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Gestione dei diritti di Azure AD: il riquadro Seleziona delle risorse del pacchetto di accesso ora Visualizza per impostazione predefinita le risorse attualmente presenti nel catalogo selezionato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 

Nel flusso di creazione del pacchetto di accesso, nella scheda ruoli risorsa, il comportamento di selezione riquadro è in corso di modifica. Attualmente, il comportamento predefinito prevede la visualizzazione di tutte le risorse di proprietà dell'utente e delle risorse aggiunte al catalogo selezionato. 

Questa esperienza verrà modificata per visualizzare solo le risorse attualmente aggiunte al catalogo per impostazione predefinita, in modo che gli utenti possano selezionare facilmente le risorse dal catalogo. L'aggiornamento aiuterà a individuare le risorse da aggiungere ai pacchetti di accesso e a ridurre il rischio di aggiungere inavvertitamente le risorse di proprietà dell'utente che non fanno parte del catalogo. Per altre informazioni, vedere [creare un nuovo pacchetto di accesso in Azure ad gestione dei diritti](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Agosto 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aggiornamenti ai requisiti del firewall di Azure server Multi-Factor Authentication

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
A partire dall'1 ottobre 2020, i requisiti del firewall del server Azure multi-factor authentication richiedono intervalli IP aggiuntivi.

Se nell'organizzazione sono presenti regole del firewall in uscita, aggiornare le regole in modo che i server multi-factor authentication possano comunicare con tutti gli intervalli di indirizzi IP necessari. Gli intervalli IP sono documentati in [Azure server multi-factor authentication requisiti del firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Modifiche imminenti all'esperienza utente nel punteggio di identità sicure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Funzionalità del **prodotto** Identity Protection: protezione & della sicurezza delle identità

Si sta aggiornando il portale Identity Secure score per allinearsi alle modifiche introdotte nella [nuova versione](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)di Microsoft Secure score. 

La versione di anteprima con le modifiche sarà disponibile all'inizio di settembre. Le modifiche apportate alla versione di anteprima includono:
- "Punteggio sicuro identità" rinominato "Punteggio sicuro per l'identità" per l'allineamento del marchio con Microsoft Secure Score
- Punti normalizzati alla scala standard e segnalati in percentuali anziché punti

In questa versione di anteprima i clienti possono passare dall'esperienza esistente alla nuova esperienza. Questa anteprima durerà fino alla fine del 2020 novembre. Al termine dell'anteprima, i clienti verranno automaticamente indirizzati alla nuova esperienza UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nuove autorizzazioni di accesso guest con restrizioni in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Controllo di accesso   
**Funzionalità del prodotto:** Gestione utenti

Sono state aggiornate le autorizzazioni a livello di directory per gli utenti guest. Queste autorizzazioni consentono agli amministratori di richiedere ulteriori restrizioni e controlli per l'accesso utente Guest esterno. Gli amministratori possono ora aggiungere ulteriori restrizioni per l'accesso degli utenti esterni ai profili e alle informazioni di appartenenza. Con questa funzionalità di anteprima pubblica, i clienti possono gestire l'accesso degli utenti esterni su larga scala offuscando le appartenenze ai gruppi, inclusa la limitazione degli utenti guest nella visualizzazione delle appartenenze ai gruppi in cui si trovano.

Per altre informazioni, vedere autorizzazioni di [accesso Guest limitate](../enterprise-users/users-restrict-guest-permissions.md) e [autorizzazioni predefinite per gli utenti](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilità generale delle query Delta per le entità servizio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- Entità servizio

Ora i client possono tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilità generale delle query Delta per oAuth2PermissionGrant

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo

Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- OAuth2PermissionGrant

I client possono ora tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-agosto 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2020 agosto sono state aggiunte le 25 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Backup365](https://portal.backup365.io/login), [pulpito](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [connettore Enlyft Dynamics 365](http://enlyft.com/), [soluzioni software per l'utilizzo dello spazio Serraview](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), notality, [visibilmente](../saas-apps/visibly-tutorial.md) [,](https://web.uniq.app/) [Zylo](../saas-apps/zylo-tutorial.md), Edmentum-corsi di [valutazione esatti percorso](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), la piattaforma di [controllo business aziendale di GreenLight](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec clearance](https://www.clearance.network/), [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART, amiko](../saas-apps/verasmart-tutorial.md) [, twingate](https://amiko.web.rivero.app/) [,](https://auth.twingate.com/signup) [imbuto leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/) [, Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [,](https://www.wandera.com/) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Foreste di risorse ora disponibili per Azure AD DS 

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services   
**Funzionalità del prodotto:** Azure AD Domain Services
 
La funzionalità delle foreste di risorse in Azure AD Domain Services è ora disponibile a livello generale. È ora possibile abilitare l'autorizzazione senza sincronizzazione dell'hash delle password per usare Azure AD Domain Services, inclusa l'autorizzazione per smart card. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>È ora disponibile il supporto della replica a livello di area per Azure AD domini gestiti di DS

**Tipo:** Nuova funzionalità   
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. I set di repliche possono essere aggiunti a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporta Azure AD Domain Services. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilità generale di Azure AD My Sign-Ins

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Azure AD My Sign-Ins è una nuova funzionalità che consente agli utenti aziendali di esaminare la cronologia di accesso per verificare la presenza di attività insolite. Inoltre, questa funzionalità consente agli utenti finali di segnalare "questo non è stato" o "questo è stato me" nelle attività sospette. Per altre informazioni sull'uso di questa funzionalità, vedere [visualizzare e cercare le attività di accesso recenti dalla pagina My Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Il provisioning utenti basato su SAP SuccessFactors HR per Azure AD è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
È ora possibile integrare SAP SuccessFactors come origine autorevole di identità con Azure AD e automatizzare il ciclo di vita delle identità end-to-end usando eventi HR come nuovi assunzioni e terminazioni per eseguire il provisioning e il deprovisioning degli account in Azure AD. 

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors in Azure AD, vedere l'esercitazione [configurare SAP SuccessFactors per Active Directory il provisioning degli utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Supporto di MS API Graph per la connessione Open ID personalizzato per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
In precedenza, i provider di connessione Open ID personalizzati potevano essere aggiunti o gestiti solo tramite il portale di Azure. Ora i Azure AD B2C clienti possono aggiungerli e gestirli tramite Microsoft Graph versione beta di API. Per informazioni su come configurare questa risorsa con le API, vedere [tipo di risorsa IdentityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Assegnare Azure AD ruoli predefiniti ai gruppi cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud con questa nuova funzionalità. Ad esempio, è possibile assegnare il ruolo di amministratore di SharePoint a Contoso_SharePoint_Admins gruppo. È anche possibile usare PIM per rendere il gruppo un membro idoneo del ruolo, anziché concedere l'accesso permanente. Per informazioni su come configurare questa funzionalità, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Il ruolo predefinito del leader aziendale di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di leader aziendale di Insights possono accedere a un set di dashboard e informazioni dettagliate tramite l' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Questo include l'accesso completo a tutti i dashboard e le funzionalità di esplorazione dei dati e delle informazioni fornite. Tuttavia, gli utenti con questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo di amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Il ruolo predefinito amministratore di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di amministratore di Insights possono accedere al set completo di funzionalità amministrative nell' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Un utente con questo ruolo può leggere le informazioni della directory, monitorare l'integrità dei servizi, i ticket di supporto file e accedere agli aspetti delle impostazioni dell'amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>L'amministratore dell'applicazione e l'amministratore di applicazioni cloud possono gestire le proprietà di estensione delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
In precedenza, solo l'amministratore globale poteva gestire la [proprietà di estensione](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Questa funzionalità è ora abilitata anche per l'amministratore dell'applicazione e l'amministratore di applicazioni cloud.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>4.6.263.0 hotfix MIM 2016 SP2 e connettori 1.1.1301.0

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un [pacchetto hotfix rollup (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup contiene gli aggiornamenti per i componenti Gestione certificati MIM, Gestione sincronizzazione MIM e PAM. Inoltre, i connettori generici MIM Build 1.1.1301.0 include aggiornamenti per il connettore Graph.

---
