---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory; ad esempio le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche future.
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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532310"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Per ricevere notifiche che indicano che occorre visitare di nuovo questa pagina per visualizzare gli aggiornamenti, copiare e incollare questo URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` nel lettore di feed ![icona del lettore di feed RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano elementi precedenti ai sei mesi, è possibile trovarli in Archivio per le novità [di Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2021"></a>Marzo 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Indicazioni su come abilitare il supporto per TLS 1.2 nell'ambiente, in preparazione per la deprecazione di TLS 1.0/1 Azure AD 1.0/1

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Standard

Azure Active Directory i protocolli seguenti nelle aree Azure Active Directory a partire dal 30 giugno 2021:


- TLS 1.0
- TLS 1.1
- Suite di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Gli ambienti interessati includono:

- Cloud commerciale di Azure
- Office 365 GCC e WW

Per altre indicazioni, vedere Abilitare il supporto per TLS 1.2 nell'ambiente per Azure AD deprecazione di [TLS 1.1 e 1.0.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Anteprima pubblica: Azure AD entitlement supporta ora SharePoint Online multi-geografica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione entitlement
 
Per le organizzazioni che usano SharePoint Online multi-geo, è ora possibile includere siti da ambienti multi-geografici specifici ai pacchetti di accesso alla gestione entitlement. [Altre informazioni](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview)

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Anteprima pubblica: ripristinare le app eliminate Registrazioni app

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo
 
I clienti possono ora visualizzare, ripristinare e rimuovere definitivamente le registrazioni delle app eliminate dal portale di Azure. Questo vale solo per le applicazioni associate a una directory, non per le applicazioni di un account Microsoft. [Altre informazioni](../develop/quickstart-restore-app.md)
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Anteprima pubblica: nuova "azione utente" nell'accesso condizionale per la registrazione o l'aggiunta di dispositivi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
 È disponibile una nuova azione utente denominata "Registra o aggiungi dispositivi" in Accesso condizionale. Questa azione utente consente di controllare i criteri di Multi-Factor Authentication (MFA) per la registrazione Azure AD dispositivo. 

Attualmente, questa azione utente consente di abilitare L'autenticazione a più fattori come controllo solo quando gli utenti registrano o si uniscono dispositivi Azure AD. Altri controlli che dipendono o non sono applicabili alla registrazione Azure AD dispositivo sono disabilitati con questa azione utente. [Altre informazioni](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Anteprima pubblica: ottimizzare i gruppi di connettori per usare il servizio cloud Application Proxy più vicino

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Con questa nuova funzionalità, i gruppi di connettori possono essere assegnati al servizio Application Proxy più vicino in cui è ospitata un'applicazione. Ciò può migliorare le prestazioni delle app negli scenari in cui le app sono ospitate in aree diverse dall'area del tenant principale. [Altre informazioni](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview) 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Anteprima pubblica- Identità esterne Self-Service'iscrizione ad AAD usando gli account di One-Time passcode

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

Gli utenti esterni potranno ora usare gli account passcode One-Time di posta elettronica per accedere Azure AD di prima parte e alle app LOB. [Altre informazioni](../external-identities/one-time-passcode.md)

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Anteprima pubblica: disponibilità di AD FS Sign-Ins in Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
AD FS'attività di accesso può ora essere integrata con la creazione Azure AD di attività, offrendo una visualizzazione unificata dell'infrastruttura di identità ibrida. Usando il report di Azure AD Sign-Ins, Log Analytics e cartelle di lavoro di Monitoraggio di Azure, è possibile eseguire analisi approfondite per scenari di accesso AD FS e AAD, ad esempio blocchi dell'account AD FS, tentativi di password non validi e picchi di tentativi di accesso imprevisti.

Per altre informazioni, [vedere AD FS di accesso in Azure AD con Connect Health.](../hybrid/how-to-connect-health-ad-fs-sign-in.md)

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Disponibilità generale: implementazione a fasi nell'autenticazione cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Autenticazione utente
 
L'implementazione a fasi dell'autenticazione cloud è ora disponibile a livello generale. La funzionalità di implementazione a fasi consente di testare in modo selettivo gruppi di utenti con metodi di autenticazione cloud, ad esempio l'autenticazione pass-through (PTA) o la sincronizzazione dell'hash delle password (PHS). Nel frattempo, tutti gli altri utenti nei domini federati continuano a usare i servizi federativi, ad esempio AD FS o qualsiasi altro servizio federativo per autenticare gli utenti. [Altre informazioni](../hybrid/how-to-connect-staged-rollout.md)

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Disponibilità generale: l'attributo Tipo di utente può ora essere aggiornato nel portale di amministrazione di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Esperienza utente e gestione  
**Funzionalità del prodotto:** Gestione utenti
 
I clienti possono ora aggiornare il tipo di utente Azure AD utenti quando aggiornano le informazioni del profilo utente dal portale di amministrazione di Azure. Il tipo di utente può essere aggiornato anche Microsoft Graph utente. Per altre informazioni, vedere Aggiungere o [aggiornare le informazioni sul profilo utente.](active-directory-users-profile-azure-portal.md)
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Disponibilità generale - Set di repliche per Azure Active Directory Domain Services

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
La funzionalità dei set di repliche in Azure AD DS è ora disponibile a livello generale. [Altre informazioni](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets)
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Disponibilità generale: collaborare con i partner usando il passcode One-Time di posta elettronica nel cloud Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le organizzazioni nel cloud Microsoft Azure per enti pubblici ora possono consentire ai propri guest di riscattare gli inviti con il passcode One-Time posta elettronica. Ciò garantisce che tutti gli utenti guest senza account Azure AD, Microsoft o Gmail nel cloud Azure per enti pubblici possano comunque collaborare con i partner richiedendo e immettendo un codice temporaneo per accedere alle risorse condivise. [Altre informazioni](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nuove app federate disponibili nella raccolta Azure AD di applicazioni - Marzo 2021

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A marzo 2021 sono state aggiunte le 37 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Bambuser Live Video Shopping,](https://lcx.bambuser.com/) [DeepDyve Inc,](https://www.deepdyve.com/azure-sso) [Moqups,](../saas-apps/moqups-tutorial.md) [RICOH Spaces Mobile,](https://ricohspaces.app/welcome) [Flipgrid,](https://auth.flipgrid.com/) [hCaptcha Enterprise,](../saas-apps/hcaptcha-enterprise-tutorial.md) [SchoolStream ASA,](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx) [TransPerfect GlobalLink Dashboard,](../saas-apps/transperfect-globallink-dashboard-tutorial.md) [SimplificaCI,](https://app.simplificaci.com.br/) [Thrive LXP,](../saas-apps/thrive-lxp-tutorial.md) [Lexonis TalentScape,](../saas-apps/lexonis-talentscape-tutorial.md) [Exium,](../saas-apps/exium-tutorial.md) [Sapient,](../saas-apps/sapient-tutorial.md) [TrueChoice,](../saas-apps/truechoice-tutorial.md) [RICOH Spaces,](https://ricohspaces.app/welcome) [Saba Cloud,](../saas-apps/learning-at-work-tutorial.md) [Acunetix 360,](../saas-apps/acunetix-360-tutorial.md) [Exceed.ai,](../saas-apps/exceed-ai-tutorial.md) [GitHub Enterprise Managed User,](../saas-apps/github-enterprise-managed-user-tutorial.md) [Enterprise Vault.cloud for Outlook,](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb) [Smartlook,](../saas-apps/smartlook-tutorial.md) [Accenture Academy,](../saas-apps/accenture-academy-tutorial.md) [Onshape,](../saas-apps/onshape-tutorial.md) [Tradeshift,](../saas-apps/tradeshift-tutorial.md) [JuriBlox,](../saas-apps/juriblox-tutorial.md) [SecurityStudio,](../saas-apps/securitystudio-tutorial.md) [ClicData,](https://app.clicdata.com/) [Evergreen,](../saas-apps/evergreen-tutorial.md) [Patchdeck,](https://patchdeck.com/ad_auth/authenticate/) [FAX. PLUS,](../saas-apps/fax.plus-tutorial.md) [ValidSign,](../saas-apps/validsign-tutorial.md) [AWS Single Sign-On,](../saas-apps/aws-single-sign-on-tutorial.md) [Nura Space,](https://dashboard.nuraspace.com/login) [Broadcom DX SaaS,](../saas-apps/broadcom-dx-saas-tutorial.md) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [SendPro Enterprise,](../saas-apps/sendpro-enterprise-tutorial.md) [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni qui: https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Marzo 2021

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise utente gestito](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [Securelogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Introducing MS Graph API for Company Branding

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** MS Graph  
**Funzionalità del prodotto:** B2B/B2C

[Ms API Graph per](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  la personalizzazione aziendale è disponibile per l'esperienza di accesso Azure AD o Microsoft 365 per consentire la gestione dei parametri di personalizzazione a livello di codice.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Disponibilità generale : SSO con autenticazione basata su intestazione con Application Proxy

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Azure AD Application Proxy supporto nativo per l'autenticazione basata su intestazione è ora disponibile a livello generale. Con questa funzionalità, è possibile configurare gli attributi utente necessari come intestazioni HTTP per l'applicazione senza componenti aggiuntivi necessari per la distribuzione. [Altre informazioni](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>L'SMS bidiredato per il server MFA non è più supportato

**Tipo:** Deprecato  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

L'SMS bidirettore per il server MFA è stato originariamente deprecato nel 2018 e non sarà supportato dopo il 24 febbraio 2021. Gli amministratori devono abilitare un altro metodo per gli utenti che usano ancora GLI SMS bidire utente.

Le notifiche tramite posta elettronica e le notifiche sull'integrità del servizio del portale di Azure sono state inviate agli amministratori interessati l'8 dicembre 2020 e il 28 gennaio 2021. Gli avvisi sono stati inviati ai ruoli Proprietario, Com proprietario, Amministratore e Amministratore del servizio in base al ruolo associati alle sottoscrizioni. [Altre informazioni](../authentication/how-to-authentication-two-way-sms-unsupported.md)
 
---
 
## <a name="february-2021"></a>Febbraio 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Per impostazione predefinita, a partire da ottobre 2021 l'autenticazione con passcode una sola volta è stata inviata tramite posta elettronica

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

A partire dal 31 ottobre 2021, Microsoft Azure Active Directory'autenticazione con [passcode](../external-identities/one-time-passcode.md) una sola volta tramite posta elettronica diventerà il metodo predefinito per invitare account e tenant per scenari di collaborazione B2B. Al momento, Microsoft non consentirà più il riscatto degli inviti usando account Azure Active Directory non gestiti. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Le autorizzazioni non richieste ma con consenso non verranno più aggiunte ai token se attivano l'accesso condizionale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Piattaforma
 
Attualmente, alle applicazioni [che usano autorizzazioni](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) dinamiche vengono concesse tutte le autorizzazioni a cui sono autorizzate ad accedere. Sono incluse le applicazioni non richieste e anche se attivano l'accesso condizionale. Ad esempio, ciò può causare il passaggio dell'accesso condizionale assegnato per l'autorizzazione a un'app che richiede solo il consenso per `user.read` `files.read` `files.read` . 

Per ridurre il numero di richieste di accesso condizionale non necessarie, Azure AD modifica il modo in cui gli ambiti non necessari vengono forniti alle applicazioni. Le app attiveranno l'accesso condizionale solo per l'autorizzazione richiesta in modo esplicito. Per altre informazioni, vedere [Novità dell'autenticazione](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Anteprima pubblica: usare un Pass di accesso temporaneo per registrare le credenziali senza password

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Pass di accesso temporaneo è un passcode limitato nel tempo che funge da credenziali sicuro e consente l'onboarding di credenziali senza password e il ripristino quando un utente ha perso o dimenticato il proprio fattore di autenticazione sicuro (ad esempio, la chiave di sicurezza FIDO2 o Microsoft Authenticator) e deve accedere per registrare nuovi metodi di autenticazione avanzata. [Altre informazioni](../authentication/howto-authentication-temporary-access-pass.md)

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Anteprima pubblica: mantieni l'accesso (KMSI) nella prossima generazione di flussi utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

La prossima generazione di flussi utente B2C supporta ora la funzionalità [Keep Me Signed In (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) che consente ai clienti di estendere la durata della sessione per gli utenti delle applicazioni Web e native usando un cookie permanente.  la funzionalità mantiene attiva la sessione anche quando l'utente chiude e riapre il browser e viene revocata quando l'utente si chiude.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Anteprima pubblica- Identità esterne Self-Service iscrizione ad AAD con account MSA

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Gli utenti esterni potranno ora usare gli account Microsoft per accedere Azure AD app di prima parte e LOB. [Altre informazioni](../external-identities/self-service-sign-up-overview.md)

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Anteprima pubblica: reimpostare lo stato di riscatto per un utente guest

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I clienti possono ora reinviare gli utenti guest esterni esistenti per reimpostare lo stato di riscatto, in modo che l'account utente guest rimanga senza che perda l'accesso. [Altre informazioni](../external-identities/reset-redemption-status.md)
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Anteprima pubblica: le API /synchronization (provisioning) supportano ora le autorizzazioni dell'applicazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
I clienti possono ora usare application.readwrite.ownedby come autorizzazione dell'applicazione per chiamare le API di sincronizzazione. Si noti che è supportato solo per il provisioning da Azure AD in applicazioni di terze parti,ad esempio AWS, Data Bricks e così via. Attualmente non è supportato per il provisioning hr (Workday/Successfactors) o per la sincronizzazione cloud (da AD a Azure AD). [Altre informazioni](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Disponibilità generale - Ruolo predefinito di Amministratore criteri di autenticazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con questo ruolo possono configurare i criteri dei metodi di autenticazione, le impostazioni MFA a livello di tenant e i criteri di protezione delle password. Questo ruolo concede l'autorizzazione per gestire le impostazioni di protezione password: configurazioni di blocco intelligente e aggiornamento dell'elenco di password personalizzate escluse. [Altre informazioni](../roles/permissions-reference.md#authentication-policy-administrator)

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Disponibilità generale: le raccolte utenti App personali sono ora disponibili.

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Gli utenti possono ora creare raggruppamenti personalizzati di app nell'icona App personali'icona di avvio delle app. Possono anche riordinare e nascondere le raccolte condivise dall'amministratore. [Altre informazioni](../user-help/my-apps-portal-user-collections.md)

---

### <a name="general-availability---autofill-in-authenticator"></a>Disponibilità generale - Riempimento automatico in Authenticator

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Microsoft Authenticator app  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Microsoft Authenticator offre funzionalità di autenticazione a più fattori (MFA) e gestione degli account e ora il riempimento automatico delle password nei siti e nelle app visitate dagli utenti sui dispositivi mobili (iOS e Android). 

Per usare il riempimento automatico in Authenticator, gli utenti devono aggiungere il proprio account Microsoft personale ad Authenticator e usarlo per sincronizzare le password. Gli account aziendali o dell'istituto di istruzione non possono essere usati per sincronizzare le password in questo momento. [Altre informazioni](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Disponibilità generale- Invitare utenti interni a Collaborazione B2B

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I clienti possono ora invitare utenti guest interni a usare Collaborazione B2B invece di inviare un invito a un account interno esistente. In questo modo i clienti possono mantenere l'ID oggetto, l'UPN, le appartenenze ai gruppi e le assegnazioni di app dell'utente. [Altre informazioni](../external-identities/invite-internal-users.md)

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Disponibilità generale - Ruolo predefinito Amministratore nome di dominio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con questo ruolo possono gestire (leggere, aggiungere, verificare, aggiornare ed eliminare) i nomi di dominio. Possono anche leggere le informazioni della directory su utenti, gruppi e applicazioni, perché questi oggetti hanno dipendenze di dominio. 

Per gli ambienti locali, gli utenti con questo ruolo possono configurare i nomi di dominio per la federazione in modo che gli utenti associati siano sempre autenticati in locale. Questi utenti possono quindi accedere Azure AD servizi basati su certificati con le proprie password locali tramite Single Sign-On. Le impostazioni di federazione devono essere sincronizzate tramite Azure AD Connect, in modo che gli utenti hanno anche le autorizzazioni per gestire Azure AD Connect. [Altre informazioni](../roles/permissions-reference.md#domain-name-administrator)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nuove app federate disponibili nella raccolta Azure AD applicazioni - Febbraio 2021

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di febbraio 2021 sono state aggiunte le 37 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Estensione Loop Messenger,](https://loopworks.com/loop-flow-messenger/) [Silverfort Azure AD Adapter,](http://www.silverfort.com/) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [Nura Space,](https://dashboard.nuraspace.com/login) [Yooz EU,](https://eu1.getyooz.com/?kc_idp_hint=microsoft) [UXPressia,](https://uxpressia.com/users/sign-in) [introDus Pre- and Onboarding Platform,](http://app.introdus.dk/login) [Happybot,](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All) [LeaksID,](https://app.leaksid.com/) [ShiftWizard,](http://www.shiftwizard.com/) [PingFlow SSO,](https://app.pingview.io/) [Swiftlane,](https://admin.swiftlane.com/login) [Quasydoc SSO,](https://www.quasydoc.eu/login) [Account Foreach Gold,](https://businesscentral.dynamics.com/) [SeamlessDesk,](https://www.seamlessdesk.com/login) [Learnsoft LMS & TMS,](http://www.learnsoft.com/) [P-TH+](https://p-th.jp/), [myViewBoard,](https://api.myviewboard.com/auth/microsoft/) [Tartabit IoT Bridge,](https://bridge-us.tartabit.com/) [AKASHI,](../saas-apps/akashi-tutorial.md) [Rewatch,](../saas-apps/rewatch-tutorial.md) [Zuddl,](../saas-apps/zuddl-tutorial.md) [Parkalot - Car park management,](../saas-apps/parkalot-car-park-management-tutorial.md) [HSB ThoughtSpot,](../saas-apps/hsb-thoughtspot-tutorial.md) [IBMid,](../saas-apps/ibmid-tutorial.md) [SharingCloud,](../saas-apps/sharingcloud-tutorial.md) [PoolParty Semantic Suite,](../saas-apps/poolparty-semantic-suite-tutorial.md) [GlobeSmart,](../saas-apps/globesmart-tutorial.md) [Samsung Knox e Business Services,](../saas-apps/samsung-knox-and-business-services-tutorial.md) [Penji,](../saas-apps/penji-tutorial.md) [Kendis- Scaling Agile Platform,](../saas-apps/kendis-scaling-agile-platform-tutorial.md) [Maptician,](../saas-apps/maptician-tutorial.md) [Olfeo SAAS,](../saas-apps/olfeo-saas-tutorial.md) [Sigma Computing,](../saas-apps/sigma-computing-tutorial.md) [CloudKnox Permissions Management Platform,](../saas-apps/cloudknox-permissions-management-platform-tutorial.md) [Klaxoon SAML,](../saas-apps/klaxoon-saml-tutorial.md) [Enablon](../saas-apps/enablon-tutorial.md)

La documentazione di tutte le applicazioni è disponibile anche qui: https://aka.ms/AppsTutorial

Per visualizzare l'applicazione nella raccolta Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Febbraio 2021

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Per altre informazioni, vedere [Automatizzare il provisioning utenti in applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Disponibilità generale: 10 ruoli Azure Active Directory ora rinominati

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
10 Azure AD ruoli predefiniti sono stati rinominati in modo che siano allineati tra interfaccia di amministrazione di Microsoft 365 [,](/microsoft-365/admin/microsoft-365-admin-center-preview) [Azure AD portale](https://portal.azure.com/)e Microsoft Graph [.](https://developer.microsoft.com/graph/) Per altre informazioni sui nuovi ruoli, vedere Autorizzazioni [dei ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabella che mostra i nomi dei ruoli in MS API Graph e portale di Azure e il nome finale proposto tra API, portale di Azure e Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nuova personalizzazione dell'azienda nella registrazione combinata MFA/SSPR

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Esperienza utente e gestione  
**Funzionalità del prodotto:** Esperienze dell'utente finale
 
In passato i logo aziendali non erano usati nelle Azure Active Directory di accesso. La personalizzazione dell'azienda si trova ora nella parte superiore sinistra della registrazione combinata MFA/SSPR. La personalizzazione aziendale è inclusa anche nella Sign-Ins e nella pagina Informazioni di sicurezza. [Altre informazioni](../fundamentals/customize-branding.md)

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Disponibilità generale: il responsabile di secondo livello può essere impostato come responsabile approvazione alternativo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 
Un'opzione aggiuntiva quando si selezionano i responsabili approvazione è ora disponibile in Gestione entitlement. Se si seleziona "Manager come responsabile approvazione" per Primo responsabile approvazione, sarà disponibile un'altra opzione, "Responsabile di secondo livello come responsabile approvazione alternativo", che è possibile scegliere nel campo Responsabile approvazione alternativo. Se si seleziona questa opzione, è necessario aggiungere un responsabile approvazione di fallback a cui inoltrare la richiesta nel caso in cui il sistema non trovi il responsabile di secondo livello. [Altre informazioni](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="authentication-methods-activity-dashboard"></a>Dashboard attività Metodi di autenticazione

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

Il dashboard aggiornato Authentication Methods Activity (Attività metodi di autenticazione) offre agli amministratori una panoramica della registrazione del metodo di autenticazione e dell'attività di utilizzo nel tenant. Il report riepiloga il numero di utenti registrati per ogni metodo e i metodi usati durante l'accesso e la reimpostazione della password. [Altre informazioni](../authentication/howto-authentication-methods-activity.md)
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>La configurazione della durata dei token di aggiornamento e di sessione in Durata token configurabile (CTL) è stata ritirata

**Tipo:** Deprecato  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Autenticazione utente
 
La configurazione della durata dei token di aggiornamento e di sessione in CTL è stata ritirata. Azure Active Directory non rispetta più la configurazione dei token di aggiornamento e di sessione nei criteri esistenti. [Altre informazioni](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)
 
---
 
## <a name="january-2021"></a>Gennaio 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Il token segreto sarà un campo obbligatorio durante la configurazione del provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

In passato, il campo del token segreto poteva essere mantenuto vuoto durante la configurazione del provisioning nell'applicazione personalizzata/BYOA. Questa funzione era destinata esclusivamente all'uso a scopo di test. Si aggiornerà l'interfaccia utente per rendere obbligatorio il campo. 

I clienti possono risolvere questo requisito a scopo di test usando un flag di funzionalità nell'URL del browser. [Altre informazioni](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Anteprima pubblica: personalizzare e configurare i dispositivi condivisi Android per i frontline worker su larga scala

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure AD e i team Endpoint Manager Microsoft si sono combinati per personalizzare, ridimensionare e proteggere i dispositivi di lavoro di prima linea.

Le funzionalità di anteprima seguenti consentono di:
- Effettuare il provisioning di dispositivi condivisi Android su larga scala con Microsoft Endpoint Manager
- Proteggere l'accesso per i turnisti usando l'accesso condizionale basato su dispositivo
- Personalizzare le esperienze di accesso per i turnisti con Managed Home Screen

Per altre informazioni, vedere Personalizzare e configurare dispositivi condivisi per i [frontline worker su larga scala.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Anteprima pubblica: i log di provisioning possono ora essere scaricati come CSV o JSON

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

I clienti possono scaricare i log di provisioning come file CSV o JSON tramite l'interfaccia utente e l'API Graph. Per altre informazioni, vedere [Report di provisioning nel portale Azure Active Directory .](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Anteprima pubblica- Assegnare gruppi cloud a Azure AD ruoli personalizzati e con ambito di unità amministrativa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
I clienti possono assegnare un gruppo cloud a Azure AD ruoli personalizzati o a un ruolo con ambito di unità amministrativa. Per informazioni su come usare questa funzionalità, vedere Usare i gruppi [cloud per gestire le assegnazioni di ruolo in Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Disponibilità generale: Azure AD Connect sincronizzazione cloud (precedentemente nota come provisioning cloud)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Connect sincronizzazione cloud  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Azure AD Connect sincronizzazione cloud è ora disponibile a livello generale per tutti i clienti.

Azure AD Connect cloud sposta nel cloud il carico elevato della logica di trasformazione, riducendo il footprint locale. Sono inoltre disponibili più distribuzioni di agenti leggeri per una maggiore disponibilità della sincronizzazione. [Altre informazioni](https://aka.ms/cloudsyncGA)
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Disponibilità generale - Ruoli predefiniti Amministratore simulazione attacchi e Autore payload attacco

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Sono disponibili due nuovi ruoli in Role-Based di accesso per l'assegnazione agli utenti, Amministratore simulazione attacchi e Autore payload di attacco. 

Gli utenti con il [ruolo Di amministratore simulazione](../roles/permissions-reference.md#attack-simulation-administrator) attacchi hanno accesso per tutte le simulazioni nel tenant e possono:
- creare e gestire tutti gli aspetti della creazione della simulazione di attacchi
- avvio/pianificazione di una simulazione
-  esaminare i risultati della simulazione. 

Gli utenti con il [ruolo autore del payload di](../roles/permissions-reference.md#attack-payload-author) attacco possono creare payload di attacco, ma non avviarli o pianificarli. I payload degli attacchi sono quindi disponibili per tutti gli amministratori del tenant che possono usarli per creare una simulazione.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Disponibilità generale - Ruolo predefinito Lettore report di riepilogo utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo lettore report di riepilogo sull'utilizzo possono accedere ai dati aggregati a livello di tenant e alle informazioni dettagliate associate in Microsoft 365 admin center for Usage and Productivity Score(Punteggio di utilizzo e produttività). Tuttavia, non possono accedere a dettagli o informazioni dettagliate a livello di utente. 

Nell'interfaccia Microsoft 365 per i due report viene differenziata tra i dati aggregati a livello di tenant e i dettagli a livello di utente. Questo ruolo aggiunge un ulteriore livello di protezione ai singoli dati identificabili dall'utente. [Altre informazioni](../roles/permissions-reference.md#usage-summary-reports-reader)

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Disponibilità generale: richiedere una Protezione di app di criteri nell'Azure AD condizionale

**Digitare:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure AD la concessione di accesso condizionale per "Richiedi criteri di protezione app" è ora disponibile a livello di codice a livello di codice. 

I criteri forniscono le funzionalità seguenti:
- Consente l'accesso solo quando si usa un'applicazione per dispositivi mobili che supporta Intune Protezione di app
- Consente l'accesso solo quando un utente ha un criterio di protezione delle app di Intune recapitato all'applicazione per dispositivi mobili

Per altre informazioni su come configurare criteri di accesso condizionale per la protezione delle [app, vedere](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Disponibilità generale - Passcode One-Time posta elettronica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Email OTP consente alle organizzazioni di tutto il mondo di collaborare con chiunque inviando un collegamento o un invito tramite posta elettronica. Gli utenti invitati possono verificare la propria identità con il passcode una sola volta inviato al messaggio di posta elettronica per accedere alle risorse del partner. [Altre informazioni](../external-identities/one-time-passcode.md) 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Gennaio 2021

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

Per altre informazioni, vedere Che cos'è il provisioning utenti di [app SaaS automatizzato in Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nuove app federate disponibili nella raccolta Azure AD application - Gennaio 2021

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A gennaio 2021 sono state aggiunte le 29 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[mySCView,](https://dev.myscview.com/) [Talentech,](https://talentech.com/contact/) [Bipsync,](https://www.bipsync.com/) [OroTimesheet,](https://app.orotimesheet.com/login.php) [Mio,](https://app.m.io/auth/install/microsoft?scopetype=hub) [Sovelto Easy,](https://login.soveltoeasy.fi/) [Supportbench,](https://account.supportbench.net/agent/login/)[Bienvenue Formation,](https://formation.bienvenue.pro/login) [AIDA Healthcare SSO,](https://aidaforparents.com/login/organizations) [International SOS Assistance Products,](../saas-apps/international-sos-assistance-products-tutorial.md) [NAVEX One,](../saas-apps/navex-one-tutorial.md) [LabLog,](../saas-apps/lablog-tutorial.md) [Oktopost SAML,](../saas-apps/oktopost-saml-tutorial.md) [EPHOTO DAM,](../saas-apps/ephoto-dam-tutorial.md) [Notion,](../saas-apps/notion-tutorial.md) [Syndio,Napo](../saas-apps/syndio-tutorial.md) [Enterprise,](../saas-apps/yello-enterprise-tutorial.md) [Time Clock 365 SAML,](../saas-apps/timeclock-365-saml-tutorial.md) [Nalco E-data,](https://www.ecolab.com/) [Vacancy Filler,](https://app.vacancy-filler.co.uk/VFMVC/Account/Login) [Synerise AI Growth Ecosystem,](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [Imperva Data Security,](../saas-apps/imperva-data-security-tutorial.md) [Illusive Networks,](../saas-apps/illusive-networks-tutorial.md) [Proware,](../saas-apps/proware-tutorial.md) [Splan Visitor,](../saas-apps/splan-visitor-tutorial.md) [Aruba User Experience Insight,](../saas-apps/aruba-user-experience-insight-tutorial.md) [Contentsquare SSO,](../saas-apps/contentsquare-sso-tutorial.md) [Perimeter 81,](../saas-apps/perimeter-81-tutorial.md) [Burp Suite edizione Enterprise](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

La documentazione di tutte le applicazioni è disponibile anche qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Anteprima pubblica: il responsabile di secondo livello può essere impostato come responsabile approvazione alternativo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 
Un'opzione aggiuntiva quando si selezionano i responsabili approvazione è ora disponibile in Gestione entitlement. Se si seleziona "Manager come responsabile approvazione" per Primo responsabile approvazione, sarà disponibile un'altra opzione, "Responsabile di secondo livello come responsabile approvazione alternativo", che è possibile scegliere nel campo Responsabile approvazione alternativo. Se si seleziona questa opzione, è necessario aggiungere un responsabile approvazione di fallback a cui inoltrare la richiesta nel caso in cui il sistema non trovi il responsabile di secondo livello. [Scopri di più](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Disponibilità generale: passare a Teams direttamente dal Accesso personale portale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 
È ora possibile avviare Teams direttamente dal Accesso personale portale. 

A tale scopo, accedere a Accesso personale ( , passare a "Pacchetti di accesso", quindi passare alla scheda "Attivo" per visualizzare tutti i pacchetti di accesso a cui si ha già https://myaccess.microsoft.com/) accesso. Quando si espande il pacchetto di accesso selezionato e si passa il mouse su Teams, è possibile avviarlo facendo clic sul pulsante "Apri". [Altre informazioni](../governance/entitlement-management-request-access.md)
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Miglioramento delle richieste & End-User registrazione per gli utenti guest a rischio

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Le richieste di registrazione e End-User per gli utenti guest a rischio sono state aggiornate. Per altre informazioni, [vedere Identity Protection e utenti B2B.](../identity-protection/concept-identity-protection-b2b.md)
 
---
 
## <a name="december-2020"></a>Dicembre 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Anteprima pubblica: Azure AD B2C'iscrizione e l'accesso tramite telefono tramite criteri predefiniti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
I criteri predefiniti per l'iscrizione e l'accesso al telefono B2C consentono agli amministratori IT e agli sviluppatori di organizzazioni di consentire agli utenti finali di accedere e iscriversi usando un numero di telefono nei flussi utente. Per altre informazioni, vedere Configurare [l'iscrizione telefonica e l'accesso per i flussi utente (anteprima).](../../active-directory-b2c/phone-authentication-user-flows.md)

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Disponibilità generale: le impostazioni predefinite di sicurezza sono ora abilitate per tutti i nuovi tenant per impostazione predefinita

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Per proteggere gli account utente, tutti i nuovi tenant creati il 12 o dopo il 12 novembre 2020 avranno le impostazioni predefinite di sicurezza abilitate. Le impostazioni predefinite di sicurezza applicano più criteri, tra cui:
- Richiede a tutti gli utenti e agli amministratori di registrarsi per L'autenticazione a più fattori usando l Microsoft Authenticator app
- È necessario che i ruoli di amministratore critici usino MFA ogni volta che a loro volta esereranno l'accesso. A tutti gli altri utenti verrà richiesto di eseguire l'autenticazione a più fattori quando necessario. 
- L'autenticazione legacy verrà bloccata a livello di tenant. 

Per altre informazioni, vedere [Che cosa sono le impostazioni predefinite per la sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Disponibilità generale: supporto per gruppi con un massimo di 250.000 membri in AADConnect

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Microsoft ha distribuito un nuovo endpoint (API) per Azure AD Connect che consente di migliorare le prestazioni delle operazioni del servizio di sincronizzazione per Azure Active Directory. Quando si usa il nuovo [endpoint V2,](../hybrid/how-to-connect-sync-endpoint-api-v2.md)si verificano notevoli miglioramenti delle prestazioni durante l'esportazione e l'importazione in Azure AD. Questo nuovo endpoint supporta gli scenari seguenti:

- Sincronizzazione di gruppi con un massimo di 250.000 membri
- Miglioramento delle prestazioni per l'esportazione e l'importazione Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Disponibilità generale- Gestione entitlement disponibile per i tenant nel cloud Azure Cina

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 

Le funzionalità di Entitlement Management sono ora disponibili per tutti i tenant nella Azure Cina cloud. Per informazioni, visitare il sito [della documentazione sulla governance delle](https://docs.azure.cn/zh-cn/active-directory/governance/) identità.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Dicembre 2020

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

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nuove app federate disponibili nella raccolta Azure AD application - Dicembre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A dicembre 2020 sono state aggiunte le 18 nuove applicazioni seguenti nella raccolta di app con supporto della federazione:

[AwareGo,](../saas-apps/awarego-tutorial.md) [HowNow SSO,](https://gethownow.com/) [ZyLAB ONE Legal Hold,](https://www.zylab.com/en/product/legal-hold) [Guider,](http://www.guider-ai.com/) [Softcrisis,](https://www.softcrisis.se/sv/) [Pims 365,](http://www.omega365.com/pims) [InformaCast,](../saas-apps/informacast-tutorial.md) [RetrieverMediaDatabase,](../saas-apps/retrievermediadatabase-tutorial.md) [vonage,](../saas-apps/vonage-tutorial.md) [Count Me In - Operations Dashboard,](../saas-apps/count-me-in-operations-dashboard-tutorial.md) [ProProfs Knowledge Base,](../saas-apps/proprofs-knowledge-base-tutorial.md) [RightCrowd Workforce Management,](../saas-apps/rightcrowd-workforce-management-tutorial.md) [JLL TRIRIGA,](../saas-apps/jll-tririga-tutorial.md) [Persianstock,](../saas-apps/shutterstock-tutorial.md) [FortiWeb Web Application Firewall,](../saas-apps/linkedin-talent-solutions-tutorial.md) [LinkedIn Talent Solutions,](../saas-apps/linkedin-talent-solutions-tutorial.md) [Equinix Federation App,](../saas-apps/equinix-federation-app-tutorial.md) [KFAdvance](../saas-apps/kfadvance-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Passare a Teams direttamente dal portale Accesso personale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Funzionalità del prodotto User Access **Management:** Entitlement Management

È ora possibile avviare Teams direttamente Accesso personale portale. A tale scopo, accedere a [Accesso personale](https://myaccess.microsoft.com/), passare a Pacchetti di  accesso **e** quindi passare alla scheda Attiva per visualizzare tutti i pacchetti di accesso a cui si ha già accesso. Quando si espande il pacchetto di accesso e si passa il mouse su Teams, è possibile avviarlo facendo clic sul **pulsante** Apri. 

Per altre informazioni sull'uso del portale Accesso personale, vedere Richiedere l'accesso a un pacchetto di accesso in gestione Azure AD [entitlement.](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Anteprima pubblica: il manager di secondo livello può essere impostato come responsabile approvazione alternativo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement

Un'opzione aggiuntiva è ora disponibile nel processo di approvazione in Entitlement Management. Se si seleziona Manager come responsabile approvazione per primo responsabile approvazione, sarà disponibile un'altra opzione, Responsabile di secondo livello come responsabile approvazione alternativo, disponibile nel campo Responsabile approvazione alternativo. Quando si seleziona questa opzione, è necessario aggiungere un responsabile approvazione di fallback a cui inoltrare la richiesta nel caso in cui il sistema non trovi il responsabile di secondo livello.

Per altre informazioni, vedere Modificare le [impostazioni di approvazione per un pacchetto di](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)accesso in Azure AD entitlement management .

--- 

## <a name="november-2020"></a>Novembre 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory deprecazione di TLS 1.0, TLS 1.1 e 3DES

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte Azure AD applicazioni  
**Funzionalità del prodotto:** Standard

Azure Active Directory i protocolli seguenti nelle aree Azure Active Directory a partire dal 30 giugno 2021:

- TLS 1.0
- TLS 1.1
- Suite di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Gli ambienti interessati sono:
- Cloud commerciale di Azure
- Office 365 GCC e WW

Annuncio correlato Tutte le combinazioni client-server e browser-server devono usare TLS 1.2 e i moderni pacchetti di crittografia per mantenere una connessione sicura a Azure Active Directory per i servizi azure, Office 365 e Microsoft 365. Azure Active Directory Questa modifica è correlata alla deprecazione di [TLS 1.0 & 1.1 e 3DES Cipher Suite in US Gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

Per indicazioni su come rimuovere le dipendenze dei protocolli deprecate, vedere Abilitare il supporto per TLS 1.2 nell'ambiente per Azure AD [TLS 1.1 e 1.0 deprecato.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nuove app federate disponibili nella raccolta Azure AD application - Novembre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A novembre 2020 sono state aggiunte le 52 nuove applicazioni seguenti nella raccolta di app con supporto della federazione:

[Travel & Expense](https://app.expenseonce.com/Account/Login)Management,Logoeloo, [Itslearning File Picker,](https://pmteam.itslearning.com/) [Controllo](../saas-apps/crises-control-tutorial.md)controllo a carico, [CourtAlert,Mail,](https://www.courtalert.com/) [](../saas-apps/tribeloo-tutorial.md) [Edmentum - Study Island,](https://app.studyisland.com/cfw/login/) [Virtual Risk Manager,](../saas-apps/virtual-risk-manager-tutorial.md) [TIMU,](../saas-apps/timu-tutorial.md) [Looker Analytics Platform,](../saas-apps/looker-analytics-platform-tutorial.md) [Talview - Recruit,](https://recruit.talview.com/login)Real Time Translator, [](https://stealthmail.com/) [Klaxoon,](https://access.klaxoon.com/login) [Podbean,](../saas-apps/podbean-tutorial.md) [zcal,](https://zcal.co/signup) [expensemanager,](https://api.expense-manager.com/) [Netsparker Enterprise,](../saas-apps/netsparker-enterprise-tutorial.md) [En-trak Tenant Experience Platform,](https://portal.en-trak.app/) [Appian,](../saas-apps/appian-tutorial.md) [Panorays,](../saas-apps/panorays-tutorial.md) [Builterra,](https://portal.builterra.com/) [EVA Check-in,](https://my.evacheckin.com/organization) [HowNow WebApp SSO,](../saas-apps/hownow-webapp-sso-tutorial.md) [Coupa Risk Assess, Lucid](../saas-apps/coupa-risk-assess-tutorial.md)(Tutti i [prodotti),](../saas-apps/lucid-tutorial.md) [GoBright,](https://portal.brightbooking.eu/) [SailPoint IdentityNow,](../saas-apps/sailpoint-identitynow-tutorial.md)Resource[Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), AWS [ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), Freight [Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), Nature [Research](../saas-apps/nature-research-tutorial.md), Play Digital [Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), Software Relations [CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® di eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

La documentazione di tutte le applicazioni è disponibile anche qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Anteprima pubblica - Ruoli personalizzati per le app aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
 [I ruoli controllo degli accessi in base al ruolo](../roles/custom-available-permissions.md) personalizzati per la gestione delegata delle applicazioni aziendali sono ora disponibili in anteprima pubblica. Queste nuove autorizzazioni si basano sui ruoli personalizzati per la gestione della registrazione delle app, che consente un controllo con granularità fine sull'accesso degli amministratori. Nel corso del tempo, verranno rilasciate autorizzazioni aggiuntive per delegare Azure AD gestione delle risorse.

Alcuni scenari di delega comuni:
- assegnazione di utenti e gruppi che possono accedere alle applicazioni Single Sign-On basate su SAML
- creazione di applicazioni Azure AD Gallery
- aggiornamento e lettura delle configurazioni SAML di base per le applicazioni Single Sign-On basate su SAML
- gestione dei certificati di firma per le applicazioni Single Sign-On basate su SAML
- aggiornamento degli indirizzi di posta elettronica di notifica dei certificati di accesso in scadenza per le applicazioni Single Sign-On basate su SAML
- aggiornamento della firma del token SAML e dell'algoritmo di accesso per le applicazioni Single Sign-On basate su SAML
- creare, eliminare e aggiornare attributi utente e attestazioni per applicazioni Single Sign-On basate su SAML
- possibilità di attivare, disattivare e riavviare i processi di provisioning
- aggiornamenti al mapping degli attributi
- possibilità di leggere le impostazioni di provisioning associate all'oggetto
- possibilità di leggere le impostazioni di provisioning associate all'entità servizio
- possibilità di autorizzare l'accesso alle applicazioni per il provisioning

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Anteprima pubblica: Azure AD Application Proxy in modo nativo supporta l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Azure Active Directory (Azure AD) Application Proxy'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione. È possibile configurare i valori di intestazione richiesti dall'applicazione in Azure AD. I valori dell'intestazione verranno inviati all'applicazione tramite Application Proxy. Per altre informazioni, vedere [Accesso Single Sign-On](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) basato su intestazione per le app locali con App Azure AD Proxy
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilità generale: Azure AD B2C'iscrizione al telefono e accesso tramite criteri personalizzati

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Con l'iscrizione e l'accesso al numero di telefono, sviluppatori e aziende possono consentire ai clienti di iscriversi e accedere usando una password una sola volta inviata al numero di telefono dell'utente tramite SMS. Questa funzionalità consente inoltre al cliente di modificare il numero di telefono se perde l'accesso al telefono. Grazie alla potenza dei criteri personalizzati, gli sviluppatori e le aziende possono comunicare il proprio marchio tramite la personalizzazione della pagina. Informazioni su come configurare [l'iscrizione tramite telefono e l'accesso](../../active-directory-b2c/phone-authentication-user-flows.md)con criteri personalizzati in Azure AD B2C .
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Novembre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Per altre informazioni, vedere [Automatizzare il provisioning utenti in applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Anteprima pubblica - Messaggio di posta Sign-In con ProxyAddresses ora distribuibile tramite implementazione a fasi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Gli amministratori tenant possono ora usare l'implementazione a fasi per distribuire Sign-In posta elettronica con ProxyAddresses a gruppi Azure AD specifici. Ciò può essere utile quando si prova la funzionalità prima di distribuirla nell'intero tenant tramite i criteri di individuazione dell'area di autenticazione principale. Le istruzioni per la distribuzione di Sign-In posta elettronica con ProxyAddresses tramite l'implementazione a fasi sono disponibili nella [documentazione](../authentication/howto-authentication-use-email-signin.md)di .
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Anteprima limitata - Diagnostica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Con la versione di anteprima iniziale della diagnostica di accesso, gli amministratori possono ora esaminare gli account di accesso degli utenti. Gli amministratori possono ricevere informazioni contestuali, specifiche e pertinenti e indicazioni su cosa è successo durante un accesso e su come risolvere i problemi. La diagnostica è disponibile sia nel livello di Azure AD che nei blade Diagnostica e risoluzione dell'accesso condizionale. Gli scenari di diagnostica descritti in questa versione sono l'accesso condizionale, Multi-Factor Authentication e l'accesso riuscito.

Per altre informazioni, vedere [Che cos'è la diagnostica di](../reports-monitoring/overview-sign-in-diagnostics.md)accesso in Azure AD? .
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Miglioramento delle proprietà di accesso non familiari

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

  Proprietà di accesso insolite nuovi rilevamenti sono stati aggiornati. I clienti potrebbero notare rilevamenti di proprietà di accesso non note ad alto rischio. Per altre informazioni, vedere [Che cos'è il rischio?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Aggiornamento dell'anteprima pubblica dell'agente di provisioning cloud ora disponibile (versione: 1.1.281.0)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD Cloud Provisioning  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
L'agente di provisioning cloud è stato rilasciato in anteprima pubblica ed è ora disponibile tramite il portale. Questa versione contiene diversi miglioramenti, tra cui il supporto per GMSA per i domini, che offre una maggiore sicurezza, cicli di sincronizzazione iniziale migliorati e supporto per gruppi di grandi dimensioni. Per altri dettagli, vedere [la cronologia](../app-provisioning/provisioning-agent-release-version-history.md) delle versioni di rilascio. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Endpoint dell'API della chiave di ripristino di BitLocker ora in /informationProtection

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
In precedenza era possibile ripristinare le chiavi BitLocker tramite l'endpoint /bitlocker. Questo endpoint verrà deprecato e i clienti dovrebbero iniziare a utilizzare l'API che ora rientra in /informationProtection. 

Vedere [API di ripristino di BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) per gli aggiornamenti alla documentazione per riflettere queste modifiche.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilità generale del Application Proxy per Servizi Desktop remoto client Web HTML5

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso
 
Azure AD Application Proxy il supporto Servizi Desktop remoto client Web di Servizi Desktop remoto è ora disponibile a disponibilità generale. Il client Web di Servizi Desktop remoto consente agli utenti di accedere all'infrastruttura di Desktop remoto tramite qualsiasi browser con supporto per HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con app o desktop remoti come con un dispositivo locale ovunque ci si trovi. 

Usando le Azure AD Application Proxy, è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto tramite l'applicazione di criteri di pre-autenticazione e accesso condizionale per tutti i tipi di app rich client. Per altre informazioni, vedere [Pubblicare Desktop remoto con Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Il nuovo servizio Gruppo dinamico avanzato è disponibile in anteprima pubblica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 
Il servizio gruppo dinamico migliorato è ora disponibile in anteprima pubblica. I nuovi clienti che creano gruppi dinamici nei tenant usano il nuovo servizio. Il tempo necessario per creare un gruppo dinamico sarà proporzionale alle dimensioni del gruppo che viene creato anziché alle dimensioni del tenant. Questo aggiornamento migliorerà notevolmente le prestazioni per i tenant di grandi dimensioni quando i clienti creano gruppi più piccoli. 

Il nuovo servizio mira anche a completare l'aggiunta e la rimozione di membri a causa di modifiche degli attributi in pochi minuti. Inoltre, gli errori di elaborazione singola non bloccano l'elaborazione del tenant. Per altre informazioni sulla creazione di gruppi dinamici, vedere la [documentazione](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Ottobre 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD agenti ibridi locali influenzati dalle modifiche ai certificati TLS di Azure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma

Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Questo aggiornamento è dovuto al fatto che i certificati della CA correnti non sono conformi a uno dei requisiti di base della CA/browser Forum. Questa modifica inciderà Azure AD gli agenti ibridi installati in locale che dispongono di ambienti con protezione avanzata con un elenco fisso di certificati radice e dovranno essere aggiornati per considerare attendibili le nuove autorità di certificazione.

Questa modifica causa un'interruzione del servizio se non si eservirà immediatamente un'azione. Questi agenti includono [connettori Application Proxy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto a locale, agenti di autenticazione [pass-through](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che consentono agli utenti di accedere alle applicazioni usando le stesse password e agenti di anteprima del [provisioning cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) che eseguono AD per la sincronizzazione Azure AD. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo al download dell'elenco di revoche di certificati (CRL) specifico, sarà necessario consentire gli URL CRL e OCSP seguenti. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP a cui abilitare l'accesso, vedere [Modifiche ai certificati TLS di Azure.](../../security/fundamentals/tls-certificate-changes.md)

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Gli eventi di provisioning verranno rimossi dai log di controllo e pubblicati esclusivamente nei log di provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
L'attività del servizio [di provisioning](../app-provisioning/user-provisioning.md) SCIM viene registrata nei log di controllo e nei log di provisioning. Sono incluse attività come la creazione di un utente in ServiceNow, il gruppo in GSuite o l'importazione di un ruolo da AWS. In futuro questi eventi verranno pubblicati solo nei log di provisioning. Questa modifica viene implementata per evitare eventi duplicati tra i log e costi aggiuntivi sostenuti dai clienti che utilizzano i log in Log Analytics. 

Verrà fornito un aggiornamento al termine di una data. Questa deprecazione non è pianificata per l'anno di calendario 2020. 

> [!NOTE]
> Ciò non influisce sugli eventi nei log di controllo al di fuori degli eventi di sincronizzazione generati dal servizio di provisioning. Eventi come la creazione di un'applicazione, i criteri di accesso condizionale, un utente nella directory e così via continueranno a essere generati nei log di controllo. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD agenti ibridi locali influenzati dalle modifiche ai certificati di Azure Transport Layer Security (TLS)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma
 
Microsoft sta aggiornando i servizi di Azure per l'uso di certificati TLS emessi da un set diverso di autorità di certificazione (CA) radice. Sarà disponibile un aggiornamento a causa dei certificati della CA correnti che non segue uno dei requisiti di base della CA/browser Forum. Questa modifica avrà effetto Azure AD gli agenti ibridi installati in locale con ambienti con protezione avanzata con un elenco fisso di certificati radice. Questi agenti dovranno essere aggiornati per considerare attendibili le nuove autorità di certificazione.

Questa modifica causa un'interruzione del servizio se non si eservirà immediatamente un'azione. Questi agenti includono: 
- [Application Proxy connettori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) per l'accesso remoto all'ambiente locale 
- [Agenti di autenticazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) pass-through che consentono agli utenti di accedere alle applicazioni usando le stesse password
- [Agenti dell'anteprima del provisioning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) cloud che eserne la sincronizzazione Azure AD ACTIVE Directory. 

Se si dispone di un ambiente con regole del firewall impostate per consentire le chiamate in uscita solo al download dell'elenco di revoche di certificati (CRL) specifico, è necessario consentire gli URL CRL e OCSP. Per informazioni dettagliate sulla modifica e sugli URL CRL e OCSP a cui abilitare l'accesso, vedere [Modifiche ai certificati TLS di Azure.](../../security/fundamentals/tls-certificate-changes.md)
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory deprecazione di TLS 1.0, TLS 1.1 e 3DES in US Gov Cloud

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Tutte Azure AD applicazioni  
**Funzionalità del prodotto:** Standard
 
Azure Active Directory deprecazione dei protocolli seguenti a partire dal 31 marzo 2021:
- TLS 1.0
- TLS 1.1
- Suite di crittografia 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Tutte le combinazioni client-server e browser-server devono usare TLS 1.2 e i moderni pacchetti di crittografia per mantenere una connessione sicura a Azure Active Directory per i servizi azure, Office 365 e Microsoft 365.

Gli ambienti interessati sono:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Per indicazioni su come rimuovere le dipendenze dei protocolli deprecate, vedere Abilitare il supporto per TLS 1.2 nell'ambiente per Azure AD [TLS 1.1 e 1.0 deprecato.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Assegnare applicazioni ai ruoli nell'ambito dell'unità amministrativa e dell'oggetto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Questa funzionalità consente di assegnare un'applicazione (SPN) a un ruolo di amministratore nell'ambito dell'unità amministrativa. Per altre informazioni, vedere [Assegnare ruoli con ambito a un'unità amministrativa.](../roles/admin-units-assign-roles.md)

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>È ora possibile disabilitare ed eliminare gli utenti guest quando viene negato l'accesso a una risorsa

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
Disabilitare ed eliminare è un controllo avanzato nelle verifiche Azure AD di accesso per aiutare le organizzazioni a gestire meglio i guest esterni in Gruppi e app. Se i guest vengono negati in una verifica di accesso, **la** disabilitazione e l'eliminazione ne bloccano automaticamente l'accesso per 30 giorni. Dopo 30 giorni, verranno rimossi completamente dal tenant.

Per altre informazioni su questa funzionalità, vedere [Disabilitare ed eliminare identità esterne con](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)Azure AD di accesso.
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>I creatori di Access Review possono aggiungere messaggi personalizzati nei messaggi di posta elettronica ai revisori

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Identity Governance
 
In Azure AD di accesso, gli amministratori che creano le verifiche possono ora scrivere un messaggio personalizzato ai revisori. I revisori visualizzano il messaggio nel messaggio di posta elettronica che li richiede di completare la verifica. Per altre informazioni sull'uso di questa funzionalità, vedere il passaggio 14 della sezione [Creare una o più verifiche di](../governance/create-access-review.md#create-one-or-more-access-reviews) accesso.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Ottobre 2020

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

### <a name="integration-assistant-for-azure-ad-b2c"></a>Assistente per l'integrazione Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza Integration Assistant (anteprima) è ora disponibile per Azure AD B2C Registrazioni app. Questa esperienza consente di configurare l'applicazione per scenari comuni. Altre informazioni sulle [procedure consigliate e le raccomandazioni di Microsoft Identity Platform.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Visualizzare l'ID modello di ruolo nell portale di Azure utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 

È ora possibile visualizzare l'ID modello di ogni Azure AD ruolo nel portale di Azure. Nella Azure AD selezionare  **la descrizione** del ruolo selezionato. 

È consigliabile che i clienti usino gli ID modello di ruolo nel codice e nello script di PowerShell, anziché nel nome visualizzato. L'ID modello di ruolo è supportato per [l'uso in oggetti directoryRoles](/graph/api/resources/directoryrole) [e roleDefinition.](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) Per altre informazioni sugli ID dei modelli di ruolo, [Azure AD ruoli predefiniti.](../roles/permissions-reference.md)

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>I connettori API per Azure AD B2C i flussi utente di iscrizione sono ora in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 

I connettori API sono ora disponibili per l'uso con Azure Active Directory B2C. I connettori API consentono di usare le API Web per personalizzare i flussi utente di iscrizione e integrarli con sistemi cloud esterni. È possibile usare i connettori API per:

- Integrazione con flussi di lavoro di approvazione personalizzati
- Convalidare i dati di input dell'utente
- Sovrascrivere gli attributi utente 
- Eseguire logica di business personalizzata 

 Per altre informazioni, vedere Usare [i connettori API](../../active-directory-b2c/api-connectors-overview.md) per personalizzare ed estendere la documentazione di iscrizione.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Proprietà State per le organizzazioni connesse nella gestione dei diritti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto Gestione directory:** Gestione entitlement
 

 Tutte le organizzazioni connesse avranno ora una proprietà aggiuntiva denominata "State". Lo stato controlla come verrà usata l'organizzazione connessa nei criteri che fanno riferimento a "tutte le organizzazioni connesse configurate". Il valore sarà "configurato", ovvero l'organizzazione è nell'ambito dei criteri che usano la clausola "all" o "proposed" (ovvero l'organizzazione non è nell'ambito).  

Le organizzazioni connesse create manualmente avranno un'impostazione predefinita "configurata". Nel frattempo, quelli creati automaticamente (creati tramite criteri che consentono a qualsiasi utente da Internet di richiedere l'accesso) per impostazione predefinita saranno "proposti".  Tutte le organizzazioni connesse create prima del 9 settembre 2020 verranno impostate su "configurato". Gli amministratori possono aggiornare questa proprietà in base alle esigenze. [Altre informazioni](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory le identità esterne hanno ora impostazioni di sicurezza avanzate Premium per B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di accesso condizionale basato sui rischi e di rilevamento dei rischi di Identity Protection sono ora disponibili in [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Con queste funzionalità di sicurezza avanzate, i clienti possono ora:
- Sfruttare intelligent insights per valutare i rischi con le app B2C e gli account utente finali. I rilevamenti includono viaggi atipici, indirizzi IP anonimi, indirizzi IP collegati al malware e Intelligence per le minacce di Azure AD. Sono disponibili anche report basati su PORTALE e API.
- Risolvere automaticamente i rischi configurando criteri di autenticazione adattiva per gli utenti B2C. Gli sviluppatori e gli amministratori di app possono ridurre i rischi in tempo reale richiedendo l'autenticazione a più fattori (MFA) o bloccando l'accesso a seconda del livello di rischio utente rilevato, con controlli aggiuntivi disponibili in base a posizione, gruppo e app.
- Integrazione con Azure AD B2C flussi utente e criteri personalizzati. Le condizioni possono essere attivate da flussi utente predefiniti in Azure AD B2C o possono essere incorporate nei criteri personalizzati B2C. Come per altri aspetti del flusso utente B2C, è possibile personalizzare la messaggistica dell'esperienza utente finale. La personalizzazione è in base alle alternative di voce, marchio e mitigazione dell'organizzazione.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nuove app federate disponibili nella raccolta Azure AD applicazioni - Ottobre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A ottobre 2020 sono state aggiunte le 27 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee - Productivity Superapp,](https://app.yellowmessenger.com/user/login) [ABBYY FlexiCapture Cloud,](../saas-apps/abbyy-flexicapture-cloud-tutorial.md) [EAComposer,](../saas-apps/eacomposer-tutorial.md) [Genesys Cloud Integration for Azure,](https://apps.mypurecloud.com/msteams-integration/) [Zone Technologies Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI,](https://app.zokri.com/) [CheckProof](../saas-apps/checkproof-tutorial.md) [, Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke,](http://atspoke.com/login) [Appointment Reminder,](https://app.appointmentreminder.co.nz/account/login) [Cloud.Market,](https://cloud.market/) [TravelPerk,](../saas-apps/travelperk-tutorial.md) [Greetly,](https://app.greetly.com/) [OrgVitality SSO,](../saas-apps/orgvitality-sso-tutorial.md) [Web Cargo Air,](../saas-apps/web-cargo-air-tutorial.md) [Loop Flow CRM,](../saas-apps/loop-flow-crm-tutorial.md) [Starmind,](../saas-apps/starmind-tutorial.md) [Workstem,](https://hrm.workstem.com/login) [Retail Zipline,](../saas-apps/retail-zipline-tutorial.md) [Hoxhunt,](../saas-apps/hoxhunt-tutorial.md) [MEVISIO,](../saas-apps/mevisio-tutorial.md) [Samsara,](../saas-apps/samsara-tutorial.md) [Nimbus,](../saas-apps/nimbus-tutorial.md) [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

La documentazione di tutte le applicazioni è disponibile anche qui https://aka.ms/AppsTutorial

Per visualizzare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>È ora possibile trasmettere i log di provisioning a Log Analytics

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 

Pubblicare i log di provisioning in Log Analytics per:
- Archiviare i log di provisioning per più di 30 giorni
- Definire avvisi e notifiche personalizzati
- Creare dashboard per visualizzare i log
- Eseguire query complesse per analizzare i log 

Per informazioni su come usare la funzionalità, vedere Informazioni sull'integrazione del provisioning con Monitoraggio di Azure [log.](../app-provisioning/application-provisioning-log-analytics.md)
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>I log di provisioning possono ora essere visualizzati dai proprietari dell'applicazione

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È ora possibile consentire ai proprietari delle applicazioni di monitorare l'attività del servizio di provisioning e risolvere i problemi senza fornire loro un ruolo con privilegi o rendendo l'IT un collo di bottiglia. [Altre informazioni](../reports-monitoring/concept-provisioning-logs.md)
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Ridenominazione di 10 Azure Active Directory ruoli

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure  
**Funzionalità del prodotto:** Controllo di accesso
 
Alcuni Azure Active Directory (AD) predefiniti hanno nomi diversi da quelli visualizzati in interfaccia di amministrazione di Microsoft 365, nel portale Azure AD e Microsoft Graph. Questa incoerenza può causare problemi nei processi automatizzati. Con questo aggiornamento vengono rinominati 10 nomi di ruolo per renderli coerenti. Nella tabella seguente sono riportati i nuovi nomi dei ruoli:

![Tabella che mostra i nomi dei ruoli in MS API Graph e nel portale di Azure e il nuovo nome del ruolo proposto nell'interfaccia di amministrazione di M365, nell'portale di Azure e nell'API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C supporto per il flusso di codice di autenticazione per GLI SPA con MSAL JS 2.x

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
MSAL.js versione 2.x include ora il supporto per il flusso del codice di autorizzazione per le app Web a pagina singola. Azure AD B2C ora supporterà l'uso del tipo di app SPA nel portale di Azure e l'uso del flusso del codice di autorizzazione MSAL.js con PKCE per le app a pagina singola. In questo modo, gli amministratori del servizio Azure AD B2C mantenere l'accesso SSO con browser più nuovi e rispettano le raccomandazioni più nuove sul protocollo di autenticazione. Introduzione all'esercitazione [Registrare un'applicazione](../../active-directory-b2c/tutorial-register-spa.md) a pagina singola in Azure Active Directory B2C.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aggiornamenti per ricordare l'impostazione di Multi-Factor Authentication (MFA) in un dispositivo attendibile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Di recente è stata aggiornata la funzionalità remember [Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) in un dispositivo attendibile per estendere l'autenticazione per un massimo di 365 giorni. Azure Active Directory (Azure AD) Premium, può anche usare il criterio Accesso condizionale [-](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) Frequenza di accesso che offre maggiore flessibilità per le impostazioni di riautenticazione.

Per un'esperienza utente ottimale, è consigliabile usare la frequenza di accesso condizionale per estendere la durata delle sessioni in dispositivi attendibili, posizioni o sessioni a basso rischio come alternativa all'impostazione memorizza MFA in un dispositivo attendibile. Per iniziare, esaminare le linee guida [più recenti per ottimizzare l'esperienza di riautenticazione.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

---
