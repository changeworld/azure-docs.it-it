---
title: Archivio per le novità in Azure Active Directory | Microsoft Docs
description: Le novità delle note sulla versione nella sezione Panoramica di questo insieme di contenuti includono 6 mesi di attività. Dopo 6 mesi, gli elementi vengono rimossi dall'articolo principale e inseriti in questo articolo di archivio.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0500c8535c6230bad85c97c23787a405edfbfee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588208"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivio per le novità in Azure Active Directory

L'articolo principale delle [note sulla versione Novità di Active Directory](whats-new.md) contiene aggiornamenti relativi agli ultimi sei mesi, mentre in questo articolo sono contenute tutte le informazioni meno recenti.

Le note sulla versione Novità di Active Directory forniscono informazioni su:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

---

## <a name="september-2020"></a>Settembre 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Settembre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Consapevolezza della sicurezza webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Aggiornamento dell'anteprima pubblica di Provisioning cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD funzionalità del prodotto Cloud **Provisioning:** Gestione del ciclo di vita delle identità
 
Azure AD Connect'aggiornamento dell'anteprima pubblica di Cloud Provisioning include due miglioramenti principali sviluppati dai commenti e suggerimenti dei clienti: 

- Esperienza di mapping degli attributi tramite portale di Azure

    Con questa funzionalità, gli amministratori IT possono eseguire il mapping degli attributi utente, gruppo o contatto da AD a Azure AD usando vari tipi di mapping attualmente presenti. Il mapping degli attributi è una funzionalità usata per standardizzare i valori degli attributi che fluino da Active Directory a Azure Active Directory. È possibile determinare se eseguire direttamente il mapping del valore dell'attributo così come si trova da Ad Azure AD o usare espressioni per trasformare i valori degli attributi durante il provisioning degli utenti. [Scopri di più](../cloud-sync/how-to-attribute-mapping.md)

- Esperienza utente di provisioning o test su richiesta

    Dopo aver creato la configurazione, è possibile verificare se la trasformazione utente funziona come previsto prima di applicarla a tutti gli utenti nell'ambito. Con il provisioning su richiesta, gli amministratori IT possono immettere il nome distinto (DN) di un utente di Active Directory e verificare se vengono sincronizzati come previsto. Il provisioning su richiesta offre un ottimo modo per garantire che i mapping degli attributi precedentemente eserciti funzionino come previsto. [Altre informazioni](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Ripristino di BitLocker con controllo in Azure AD - Anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
Quando gli amministratori IT o gli utenti finali leggono le chiavi di ripristino di BitLocker a cui hanno accesso, Azure Active Directory genera ora un log di controllo che acquisisce chi ha eseguito l'accesso alla chiave di ripristino. Lo stesso controllo fornisce i dettagli del dispositivo a cui è stata associata la chiave BitLocker.

Gli utenti finali [possono accedere alle proprie chiavi di ripristino tramite Account personale.](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) Gli amministratori IT possono accedere alle chiavi di ripristino tramite l'API delle chiavi di [ripristino di BitLocker in versione beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) o tramite il portale Azure AD. Per altre informazioni, vedere [Visualizzare o copiare chiavi BitLocker nel portale Azure AD .](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)

---

### <a name="teams-devices-administrator-built-in-role"></a>Ruolo predefinito Amministratore dispositivi di Teams

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Rbac  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con [il ruolo Amministratore dispositivi di Teams](../roles/permissions-reference.md#teams-devices-administrator) possono gestire i dispositivi certificati da [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) dall'interfaccia di amministrazione di Teams. 

Questo ruolo consente all'utente di visualizzare tutti i dispositivi a colpo d'occhio, con la possibilità di cercare e filtrare i dispositivi. L'utente può anche controllare i dettagli di ogni dispositivo, inclusi l'account connesso e la make e il modello del dispositivo. L'utente può modificare le impostazioni nel dispositivo e aggiornare le versioni del software. Questo ruolo non concede le autorizzazioni per controllare l'attività di Teams e chiamare la qualità del dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Funzionalità di query avanzate per gli oggetti directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** MS Graph  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Tutte le nuove funzionalità di query introdotte per gli oggetti directory nelle API Azure AD sono ora disponibili nell'endpoint v1.0 e pronte per la produzione. Gli sviluppatori possono contare, cercare, filtrare e ordinare gli oggetti directory e i collegamenti correlati usando gli operatori OData standard.

Per altre informazioni, vedere la documentazione [qui](https://aka.ms/BlogPostMezzoGA)ed è anche possibile inviare commenti e suggerimenti con [questo breve sondaggio.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Anteprima pubblica: valutazione dell'accesso continuo per i tenant che hanno configurato i criteri di accesso condizionale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
La valutazione dell'accesso continuo è ora disponibile in anteprima pubblica per Azure AD tenant con criteri di accesso condizionale. Con CAE, i criteri e gli eventi di sicurezza critici vengono valutati in tempo reale. Ciò include la disabilitazione dell'account, la reimpostazione della password e la modifica della posizione. Per altre informazioni, vedere [Valutazione dell'accesso continuo.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Anteprima pubblica: chiedere agli utenti che richiedono un pacchetto di accesso altre domande per migliorare le decisioni di approvazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 
Gli amministratori possono ora richiedere che gli utenti che richiedono un pacchetto di accesso rispondano ad altre domande oltre Accesso personale alla semplice giustificazione aziendale nel portale Azure AD gestione entitlement. Le risposte degli utenti verranno quindi visualizzate ai responsabili approvazione per consentire loro di prendere una decisione di approvazione dell'accesso più accurata. Per altre informazioni, vedere [Raccogliere informazioni aggiuntive sul richiedente per l'approvazione (anteprima).](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)
 
---

### <a name="public-preview-enhanced-user-management"></a>Anteprima pubblica: Gestione utenti avanzata

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Gestione utenti
 

Il Azure AD è stato aggiornato per semplificare l'individuazione degli utenti nelle pagine Tutti gli utenti e Utenti eliminati. Le modifiche nell'anteprima includono: 
- Proprietà utente più visibili, tra cui l'ID oggetto, lo stato di sincronizzazione della directory, il tipo di creazione e l'autorità emittente dell'identità.
- La ricerca consente ora la ricerca combinata di nomi, messaggi di posta elettronica e ID oggetto.
- Filtro avanzato in base al tipo di utente (membro, guest e nessuno), stato di sincronizzazione della directory, tipo di creazione, nome della società e nome di dominio.
- Nuove funzionalità di ordinamento per proprietà come il nome, il nome dell'entità utente e la data di eliminazione.
- Un nuovo numero totale di utenti che viene aggiornato con eventuali ricerche o filtri.

Per altre informazioni, vedere Miglioramenti della [gestione utenti (anteprima) in Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nuovo campo delle note per le applicazioni aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto app aziendali:** SSO

È possibile aggiungere note di testo libero alle applicazioni aziendali. È possibile aggiungere qualsiasi informazione pertinente che possa essere utile per la gestione delle applicazioni in Applicazioni aziendali. Per altre informazioni, vedere Avvio rapido: Configurare le proprietà per un'applicazione nel tenant Azure Active Directory [(Azure AD).](../manage-apps/add-application-portal-configure.md) 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nuove app federate disponibili nella raccolta Azure AD applicazioni - Settembre 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di settembre 2020 sono state aggiunte le 34 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[VMware Horizon - Unified Access Gateway,]() [Pulse Secure PCS,](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md) [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [KpileCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender Fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite,](../saas-apps/rsa-archer-suite-tutorial.md) [TeamzSkill,](../saas-apps/teamzskill-tutorial.md) [raumfürraum,](../saas-apps/raumfurraum-tutorial.md) [Saviynt,](../saas-apps/saviynt-tutorial.md) [BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup) [Mobile Locker,](../saas-apps/mobile-locker-tutorial.md) [Zengine,](../saas-apps/zengine-tutorial.md) [CloudCADI,](https://app.cloudcadi.com/login) [Sim analytics,](https://simfonianalytics.com/accounts/microsoft/login/) [Priva Identity & Access Management,](https://my.priva.com/) [Nitro Pro,](https://www.gonitro.com/nps/product-details/downloads) [Eventfinity,](../saas-apps/eventfinity-tutorial.md) [Fexa,](../saas-apps/fexa-tutorial.md)Secured [Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), Secured Signing [Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec Online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

La documentazione di tutte le applicazioni è disponibile anche qui: https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella Azure AD di app, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nuovo ruolo di delega nella Azure AD entitlement: Gestione assegnazioni pacchetti di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 
È stato aggiunto un nuovo ruolo Gestione assegnazioni pacchetti di accesso Azure AD gestione entitlement per fornire autorizzazioni granulari per gestire le assegnazioni. È ora possibile delegare le attività a un utente con questo ruolo, che può delegare la gestione delle assegnazioni di un pacchetto di accesso a un proprietario dell'azienda. Tuttavia, un gestore di assegnazione pacchetti di accesso non può modificare i criteri del pacchetto di accesso o altre proprietà impostate dagli amministratori. 

Con questo nuovo ruolo si traggono vantaggio dai privilegi minimi necessari per delegare la gestione delle assegnazioni e mantenere il controllo amministrativo su tutte le altre configurazioni del pacchetto di accesso. Per altre informazioni, vedere [Ruoli di gestione entitlement](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Modifiche al flusso Privileged Identity Management di onboarding di Privileged Identity Management

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
In precedenza, l'onboarding in Privileged Identity Management (PIM) richiedeva il consenso dell'utente e un flusso di onboarding nel pannello di PIM che includeva la registrazione in Azure AD MFA. Con la recente integrazione dell'esperienza PIM nel pannello Azure AD ruoli e amministratori, questa esperienza viene rimosso. Qualsiasi tenant con licenza P2 valida verrà automaticamente onboarded in PIM.

L'onboarding in PIM non ha alcun effetto negativo diretto sul tenant. È possibile prevedere le modifiche seguenti:
- Opzioni di assegnazione aggiuntive, ad esempio attiva o idonea con ora di inizio e fine quando si effettua un'assegnazione nel pannello PIM o Azure AD ruoli e amministratori. 
- Meccanismi di ambito aggiuntivi, ad esempio unità amministrative e ruoli personalizzati, introdotti direttamente nell'esperienza di assegnazione. 
- Se si è un amministratore globale o un amministratore di ruoli con privilegi, è possibile iniziare a ricevere alcuni messaggi di posta elettronica aggiuntivi, ad esempio il digest settimanale PIM. 
- È anche possibile visualizzare l'entità servizio ms-pim nel log di controllo correlato all'assegnazione di ruolo. Questa modifica prevista non dovrebbe influire sul normale flusso di lavoro.

 Per altre informazioni, vedere [Iniziare a usare Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD Entitlement Management: il riquadro Seleziona delle risorse del pacchetto di accesso ora mostra ora per impostazione predefinita le risorse attualmente presenti nel catalogo selezionato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi utente  
**Funzionalità del prodotto:** Gestione entitlement
 

Nel flusso di creazione del pacchetto di accesso, nella scheda Ruoli risorsa, il comportamento del riquadro Seleziona sta cambiando. Attualmente, il comportamento predefinito è visualizzare tutte le risorse di proprietà dell'utente e le risorse aggiunte al catalogo selezionato. 

Questa esperienza verrà modificata per visualizzare solo le risorse attualmente aggiunte nel catalogo per impostazione predefinita, in modo che gli utenti possano selezionare facilmente le risorse dal catalogo. L'aggiornamento consente di individuare le risorse da aggiungere ai pacchetti di accesso e ridurre il rischio di aggiungere inavvertitamente risorse di proprietà dell'utente che non fanno parte del catalogo. Per altre informazioni, vedere [Creare un nuovo pacchetto di accesso in Azure AD entitlement.](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>Agosto 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aggiornamenti ai requisiti del firewall server Multi-Factor Authentication Azure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
A partire dal 1° ottobre 2020, server Azure MFA requisiti del firewall richiederanno intervalli IP aggiuntivi.

Se nell'organizzazione sono presenti regole del firewall in uscita, aggiornare le regole in modo che i server MFA possano comunicare con tutti gli intervalli IP necessari. Gli intervalli IP sono documentati in [Requisiti server Multi-Factor Authentication firewall di Azure.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Modifiche imminenti all'esperienza utente in Identity Secure Score

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Identity Security & Protection

È in corso l'aggiornamento del portale di Identity Secure Score per allinearsi alle modifiche introdotte nella nuova [versione di](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)Microsoft Secure Score. 

La versione di anteprima con le modifiche sarà disponibile all'inizio di settembre. Le modifiche nella versione di anteprima includono:
- "Identity Secure Score" rinominato in "Secure Score for Identity" per l'allineamento del marchio con Microsoft Secure Score
- Punti normalizzati alla scala standard e riportati in percentuali anziché in punti

In questa anteprima i clienti possono passare dall'esperienza esistente alla nuova esperienza. Questa anteprima durerà fino alla fine di novembre 2020. Dopo l'anteprima, i clienti verranno automaticamente indirizzati alla nuova esperienza utente.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nuove autorizzazioni di accesso guest con restrizioni in Azure AD - Anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Controllo di accesso   
**Funzionalità del prodotto:** Gestione utenti

Sono state aggiornate le autorizzazioni a livello di directory per gli utenti guest. Queste autorizzazioni consentono agli amministratori di richiedere restrizioni e controlli aggiuntivi sull'accesso degli utenti guest esterni. Gli amministratori possono ora aggiungere altre restrizioni per l'accesso degli utenti guest esterni alle informazioni sul profilo e sull'appartenenza di utenti e gruppi. Con questa funzionalità di anteprima pubblica, i clienti possono gestire l'accesso degli utenti esterni su larga scala offuscando le appartenenze ai gruppi, inclusa la limitazione degli utenti guest di visualizzare le appartenenze dei gruppi in cui si presentano.

Per altre informazioni, vedere [Autorizzazioni di accesso guest limitate e](../enterprise-users/users-restrict-guest-permissions.md) Autorizzazioni predefinite degli [utenti](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilità generale delle query differenziali per le entità servizio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** MS Graph  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Microsoft Graph Delta Query supporta ora il tipo di risorsa nella versione 1.0:
- Entità servizio

Ora i client possono tenere traccia delle modifiche apportate a tali risorse in modo efficiente e forniscono la soluzione migliore per sincronizzare le modifiche a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere Usare una query differenziale per tenere traccia delle [modifiche Microsoft Graph dati.](/graph/delta-query-overview)
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilità generale delle query differenziali per oAuth2PermissionGrant

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** MS Graph  
**Funzionalità del prodotto:** esperienza di sviluppo

Microsoft Graph Delta Query supporta ora il tipo di risorsa nella versione 1.0:
- OAuth2PermissionGrant

I client possono ora tenere traccia delle modifiche apportate a tali risorse in modo efficiente e forniscono la soluzione migliore per sincronizzare le modifiche a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere Usare una query differenziale per tenere traccia delle [modifiche Microsoft Graph dati.](/graph/delta-query-overview)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nuove app federate disponibili nella raccolta Azure AD di applicazioni - Agosto 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nell'agosto 2020 sono state aggiunte le 25 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365 Connector](http://enlyft.com/), [Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq](https://web.uniq.app/), [Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel,](../saas-apps/wirewheel-tutorial.md) [Zix Compliance and Capture,](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common) [Greenlight Enterprise Business Controls Platform,](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [Genetec Clearance,](https://www.clearance.network/) [iSAMS,](../saas-apps/isams-tutorial.md) [VeraSMART,](../saas-apps/verasmart-tutorial.md) [Amiko,](https://amiko.web.rivero.app/) [Twingate, Funnel](https://auth.twingate.com/signup) [Leasing,](https://nestiolistings.com/sso/oidc/azure/authorize/) [Scalefusion,](https://scalefusion.com/users/sign_in/) [Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Connect,](https://app.vivun.com/dashboard/calendar/connect) [FortiGate SSL VPN,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Wandera End User](https://www.wandera.com/)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per visualizzare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Foreste di risorse ora disponibili per l'Azure AD DS 

**Digitare:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services   
**Funzionalità del prodotto:** Azure AD Domain Services
 
La funzionalità delle foreste di risorse in Azure AD Domain Services è ora disponibile a livello generale. È ora possibile abilitare l'autorizzazione senza sincronizzazione dell'hash delle password per Azure AD Domain Services, inclusa l'autorizzazione con smart card. Per altre informazioni, vedere Concetti e funzionalità dei set di repliche [per Azure Active Directory Domain Services (anteprima).](../../active-directory-domain-services/concepts-replica-sets.md)
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>È ora disponibile il supporto della replica Azure AD DS per i domini gestiti

**Tipo:** Nuova funzionalità   
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. I set di repliche possono essere aggiunti a qualsiasi rete virtuale con peer in qualsiasi area di Azure che supporta Azure AD Domain Services. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline. Per altre informazioni, vedere Concetti e funzionalità dei set di repliche [per Azure Active Directory Domain Services (anteprima).](../../active-directory-domain-services/concepts-replica-sets.md)

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilità generale di Azure AD my Sign-Ins

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Azure AD my Sign-Ins è una nuova funzionalità che consente agli utenti aziendali di esaminare la cronologia di accesso per verificare la presenza di attività insolite. Questa funzionalità consente inoltre agli utenti finali di segnalare le attività sospette. Per altre informazioni sull'uso di questa funzionalità, vedere Visualizzare ed eseguire ricerche nell'attività di accesso recente dalla pagina My [Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Sap SuccessFactors HR ha guidato il provisioning degli utenti Azure AD è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
È ora possibile integrare SAP SuccessFactors come origine di identità autorevole con Azure AD e automatizzare il ciclo di vita delle identità end-to-end usando eventi hr come nuove assunzioni e terminazioni per guidare il provisioning e il deproproduzione degli account Azure AD. 

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors Azure AD, vedere l'esercitazione [Configurare SAP SuccessFactors per](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)il provisioning utenti di Active Directory.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Supporto personalizzato di Open ID Connect MS API Graph per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
In precedenza, i provider Open ID Connect personalizzati potevano essere aggiunti o gestiti solo tramite il portale di Azure. Ora i Azure AD B2C clienti possono aggiungerli e gestirli tramite Microsoft Graph api beta. Per informazioni su come configurare questa risorsa con le API, vedere [tipo di risorsa identityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Assegnare Azure AD ruoli predefiniti ai gruppi cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD ruoli  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud con questa nuova funzionalità. Ad esempio, è possibile assegnare il ruolo Amministratore di SharePoint Contoso_SharePoint_Admins gruppo. È anche possibile usare PIM per rendere il gruppo un membro idoneo del ruolo, anziché concedere l'accesso permanente. Per informazioni su come configurare questa funzionalità, vedere Usare i gruppi cloud per gestire le assegnazioni di ruolo in Azure Active Directory [(anteprima).](../roles/groups-concept.md)
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Ruolo predefinito di Insights Business Leader ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD ruoli  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo Di business leader di Insights possono accedere a un set di dashboard e informazioni dettagliate tramite [l'applicazione M365 Insights.](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) Questo include l'accesso completo a tutti i dashboard e le informazioni dettagliate presentate e la funzionalità di esplorazione dei dati. Tuttavia, gli utenti in questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo amministratore di Insights. Per altre informazioni su questo ruolo, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>È ora disponibile il ruolo predefinito Amministratore di Insights

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD ruoli  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo di amministratore di Insights possono accedere al set completo di funzionalità amministrative [nell'applicazione M365 Insights.](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics) Un utente con questo ruolo può leggere le informazioni della directory, monitorare l'integrità del servizio, i ticket di supporto file e accedere agli aspetti delle impostazioni dell'amministratore di Insights. Per altre informazioni su questo ruolo, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>L'amministratore dell'applicazione e l'amministratore dell'applicazione cloud possono gestire le proprietà delle estensioni delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD ruoli  
**Funzionalità del prodotto:** Controllo di accesso
 
In precedenza, solo l'amministratore globale poteva gestire la [proprietà di estensione](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true). Questa funzionalità è ora disponibile anche per l'amministratore dell'applicazione e l'amministratore di applicazioni cloud.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MiM 2016 SP2 hotfix 4.6.263.0 e connettori 1.1.1301.0

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un pacchetto hotfix [rollup (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup contiene gli aggiornamenti per i Gestione certificati MIM, Gestione sincronizzazione MIM e PAM. Inoltre, i connettori generici MIM build 1.1.1301.0 includono aggiornamenti per il connettore Graph.

---

## <a name="july-2020"></a>Luglio 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Come amministratore IT, si vogliono usare come destinazione le app client con l'accesso condizionale

**Tipo:** Modifica pianificata   
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Con la versione disponibile a livello globale della condizione delle app client nell'accesso condizionale, i nuovi criteri verranno ora applicati per impostazione predefinita a tutte le applicazioni client. Sono inclusi i client di autenticazione legacy. I criteri esistenti rimarranno invariati, ma l'interruttore Configura *Sì/No* verrà rimosso dai criteri esistenti per visualizzare facilmente le app client applicate dai criteri. 

Quando si creano nuovi criteri, assicurarsi di escludere gli utenti e gli account del servizio che usano ancora l'autenticazione legacy. In caso contrario, verranno bloccati. [Altre informazioni](../conditional-access/concept-conditional-access-conditions.md)
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Prossime correzioni di conformità SCIM

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il Azure AD di provisioning usa lo standard SCIM per l'integrazione con le applicazioni. L'implementazione dello standard SCIM è in evoluzione e ci si aspetta di apportare modifiche al comportamento in base al modo in cui si eseguono operazioni PATCH e di impostare la proprietà "active" su una risorsa. [Altre informazioni](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>L'impostazione del proprietario del gruppo nel portale di amministrazione di Azure verrà modificata

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Le impostazioni del proprietario nella pagina delle impostazioni generali gruppi possono essere configurate per limitare i privilegi di assegnazione del proprietario a un gruppo limitato di utenti nel portale di amministrazione di Azure e Pannello di accesso. Presto sarà possibile assegnare il privilegio di proprietario del gruppo non solo in questi due portali dell'esperienza utente, ma anche applicare i criteri sul back-end per fornire un comportamento coerente tra endpoint, ad esempio PowerShell e Microsoft Graph. 

Si inizierà a disabilitare l'impostazione corrente per i clienti che non la usano e nei prossimi mesi verrà offerta un'opzione per l'ambito degli utenti per il privilegio di proprietario del gruppo. Per indicazioni sull'aggiornamento delle impostazioni del gruppo, vedere Modificare le informazioni sul gruppo [usando Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory registration service sta terminando il supporto per TLS 1.0 e 1.1

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) 1.2 e i server e i client di aggiornamento comunicheranno presto con Registrazione dispositivo Azure Active Directory Service. Il supporto per TLS 1.0 e 1.1 per la comunicazione Registrazione dispositivo Azure AD servizio verrà ritirato:
- Il 31 agosto 2020, in tutti i cloud sovrani (GCC High, DoD e così via)
- Il 30 ottobre 2020, in tutti i cloud commerciali

[Altre informazioni](../devices/reference-device-registration-tls-1-2.md) su TLS 1.2 per il servizio Azure AD registrazione.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello for Business di accesso visibili nei log Azure AD di accesso

**Tipo:** Correzione  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Windows Hello for Business consente agli utenti finali di accedere ai computer Windows con un movimento, ad esempio un PIN o biometrico. Azure AD amministratori possono voler distinguere gli Windows Hello for Business da altri account di accesso di Windows come parte del percorso di un'organizzazione verso l'autenticazione senza password. 

Gli amministratori possono ora verificare se un autenticazione di Windows usato Windows Hello for Business controllando la scheda Dettagli autenticazione per un evento di accesso di Windows nel pannello Azure AD Sign-Ins della portale di Azure. Windows Hello for Business le autenticazioni includono "WindowsHelloForBusiness" nel campo Metodo di autenticazione. Per altre informazioni sull'interpretazione Sign-In log, vedere la [documentazione relativa ai log di accesso](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correzioni per il comportamento di eliminazione dei gruppi e i miglioramenti delle prestazioni

**Tipo:** Correzione  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
In precedenza, quando un gruppo è passato da "in-scope" a "out-of-scope" e un amministratore ha fatto clic sul riavvio prima del completamento della modifica, l'oggetto gruppo non è stato eliminato. A questo punto l'oggetto gruppo verrà eliminato dall'applicazione di destinazione quando esce dall'ambito (disabilitato, eliminato, non assegnato o non ha superato il filtro di ambito). [Altre informazioni](../app-provisioning/how-provisioning-works.md#incremental-cycles)
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Anteprima pubblica: gli amministratori possono ora aggiungere contenuto personalizzato nel messaggio di posta elettronica ai revisori durante la creazione di una verifica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Identity Governance
 
Quando viene creata una nuova verifica di accesso, il revisore riceve un messaggio di posta elettronica che richiede di completare la verifica di accesso. Molti clienti hanno richiesto la possibilità di aggiungere contenuto personalizzato al messaggio di posta elettronica, ad esempio informazioni di contatto o altro contenuto di supporto aggiuntivo per guidare il revisore. 

Ora disponibili in anteprima pubblica, gli amministratori possono specificare contenuto personalizzato nel messaggio di posta elettronica inviato ai revisori aggiungendo contenuto nella sezione "avanzate" di Azure AD di accesso. Per indicazioni sulla creazione di verifiche di accesso, vedere Creare una verifica di accesso di gruppi e applicazioni in Azure AD [di accesso.](../governance/create-access-review.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Flusso del codice di autorizzazione per le app a pagina singola disponibili

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo
 
A causa delle moderne restrizioni dei cookie di terze parti del browser, ad esempio Safari ITP, gli amministratori di servizi devono usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere l'accesso SSO e MSAL.js v 2.x supporterà ora il flusso del codice di autorizzazione. 

Sono disponibili aggiornamenti corrispondenti al portale di Azure in modo che sia possibile aggiornare l'applicazione a pagina pagina come tipo "spa" e usare il flusso del codice di autenticazione. Per altre indicazioni, vedere Accedere agli utenti e ottenere un token di accesso [in un'applicazione a](../develop/quickstart-v2-javascript-auth-code.md) pagina pagina JavaScript usando il flusso del codice di autenticazione.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD Application Proxy ora supporta il client Servizi Desktop remoto Web

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Azure AD Application Proxy ora supporta il Servizi Desktop remoto Web di Servizi Desktop remoto. Il client Web di Servizi Desktop remoto consente agli utenti di accedere Desktop remoto infrastruttura tramite qualsiasi browser con supporto HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con app o desktop remoti come farebbe con un dispositivo locale da qualsiasi posizione. Usando Azure AD Application Proxy è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto tramite l'applicazione di criteri di pre-autenticazione e accesso condizionale per tutti i tipi di app rich client. Per indicazioni, vedere [Pubblicare Desktop remoto con Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Flussi utente Azure AD B2C di nuova generazione in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza semplificata del flusso utente offre la parità delle funzionalità con le funzionalità di anteprima ed è la sede di tutte le nuove funzionalità. Gli utenti potranno abilitare nuove funzionalità all'interno dello stesso flusso utente, riducendo la necessità di creare più versioni con ogni nuova versione delle funzionalità. Infine, la nuova esperienza utente semplice semplifica la selezione e la creazione dei flussi utente. Provare subito creando [un flusso utente.](../../active-directory-b2c/tutorial-create-user-flows.md) 

Per altre informazioni sui flussi degli utenti, vedere [Versioni del flusso utente in Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nuove app federate disponibili nella raccolta Azure AD di applicazioni - Luglio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di luglio 2020 sono state aggiunte le 55 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Clap Your Hands,](http://www.rmit.com.ar/) [Appreiz,](https://microsoftteams.appreiz.com/) [Inextor Vault,](https://inexto.com/inexto-suite/inextor) [Beekast,](https://my.beekast.com/) [Templafy OpenID Connect,](https://app.templafy.com/) [PeterConnects](https://msteams.peterconnects.com/)ricettore, [AlohaCloud,](https://appfusions.alohacloud.com/auth) [Control Tower,](https://bpm.tnxcorp.com/sso/microsoft) [Cocoom,CLOUD](https://start.cocoom.com/) [Construction Cloud,](https://sso.coinsconstructioncloud.com/#login/) [Medxnote MT,](https://task.teamsmain.medx.im/authorization) [Reflekt,](https://reflekt.konsolute.com/login) [Rever,](https://app.reverscore.net/access) [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders,](https://app.greminders.com/o365-oauth) [Titanfile,](../saas-apps/titanfile-tutorial.md) [Wootric,](../saas-apps/wootric-tutorial.md) [SolarWinds Orion,](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Egress](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right-Hand Cybersecurity ADI,](https://right-hand.ai/) [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tell BotMe](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector,](https://www.strata.io/identity-fabric/) [Smartschool (School Management System),](https://smartschoolz.com/login) [Zepto - Smart timekeeping,](https://user.zepto-ai.com/signin) [Studi.ly,](https://studi.ly/) [Trackplan,](http://www.trackplanfm.com/) [Skedda,](../saas-apps/skedda-tutorial.md) [WhosOnLocation,](../saas-apps/whos-on-location-tutorial.md) [Coggle,](../saas-apps/coggle-tutorial.md) [Kemp LoadMaster,](https://kemptechnologies.com/cloud-load-balancer/) [BrowserStack Single Sign-On](../saas-apps/browserstack-single-sign-on-tutorial.md)

La documentazione di tutte le applicazioni è disponibile anche qui https://aka.ms/AppsTutorial

Per inserire l'applicazione nella raccolta Azure AD, leggere i dettagli qui https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visualizzare le assegnazioni di ruolo in tutti gli ambiti e la possibilità di scaricarle in un file CSV

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD ruoli  
**Funzionalità del prodotto:** Controllo di accesso
 
È ora possibile visualizzare le assegnazioni di ruolo in tutti gli ambiti per un ruolo nella scheda "Ruoli e amministratori" nel portale Azure AD ruolo. È anche possibile scaricare tali assegnazioni di ruolo per ogni ruolo in un file CSV. Per indicazioni sulla visualizzazione e l'aggiunta di assegnazioni di ruolo, vedere Visualizzare e [assegnare](../roles/manage-roles-portal.md)ruoli di amministratore in Azure Active Directory .
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Deprecazione di Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Tipo:** Deprecato  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure Multi-Factor Authentication Software Development (Azure MFA SDK) ha raggiunto la fine del ciclo di vita il 14 novembre 2018, come annunciato per la prima volta a novembre 2017. Microsoft arresterà il servizio SDK a partire dal 30 settembre 2020. Tutte le chiamate effettuate all'SDK avranno esito negativo.

Se l'organizzazione usa Azure MFA SDK, è necessario eseguire la migrazione entro il 30 settembre 2020:
- Azure MFA SDK per MIM: se si usa l'SDK con MIM, è necessario eseguire la migrazione a server Azure MFA e attivare Privileged Access Management (PAM) seguendo queste [istruzioni.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Azure MFA SDK per app personalizzate: valutare la possibilità di integrare l'app Azure AD e usare l'accesso condizionale per applicare l'autenticazione a più fattori. Per iniziare, vedere questa [pagina.](../manage-apps/plan-an-application-integration.md) 

---

## <a name="june-2020"></a>Giugno 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condizione di rischio utente nei criteri di accesso condizionale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Il supporto dei rischi utente Azure AD criteri di accesso condizionale consente di creare più criteri basati sul rischio utente. Diversi livelli minimi di rischio utente possono essere richiesti per utenti e app diversi. In base al rischio utente, è possibile creare criteri per bloccare l'accesso, richiedere l'autenticazione a più fattori, la modifica sicura della password o il reindirizzamento a Microsoft Cloud App Security per applicare i criteri di sessione, ad esempio il controllo aggiuntivo.

La condizione di rischio utente Azure AD Premium P2 perché usa Azure Identity Protection, che è un'offerta P2. Per altre informazioni sull'accesso condizionale, vedere la documentazione [Azure AD sull'accesso condizionale.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO supporta ora le app che richiedono l'impostazione di SPNameQualifier quando richiesto

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Alcune applicazioni SAML richiedono che SPNameQualifier sia restituito nel soggetto dell'asserzione quando richiesto. Ora Azure AD risponde correttamente quando viene richiesto spNameQualifier nei criteri NameID della richiesta. Questa operazione funziona anche per l'accesso avviato da SP e verrà seguito l'accesso avviato da IdP.  Per altre informazioni sul protocollo SAML in Azure Active Directory, vedere Single Sign-On SAML protocol ( [Protocollo SAML single-Sign-On).](../develop/single-sign-on-saml-protocol.md)

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD Collaborazione B2B supporta l'invito di utenti dell'account del servizio microsoft e Google Azure per enti pubblici tenant

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

Azure per enti pubblici tenant che usano le funzionalità di Collaborazione B2B possono ora invitare gli utenti con un account Microsoft o Google. Per scoprire se il tenant può usare queste funzionalità, seguire le istruzioni riportate in Come è possibile determinare se collaborazione [B2B](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) è disponibile nel tenant di Azure US Government?

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>L'oggetto utente in MS Graph v1 include ora le proprietà externalUserState ed externalUserStateChangedDateTime

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

Le proprietà externalUserState ed externalUserStateChangedDateTime possono essere usate per trovare i guest B2B invitati che non hanno ancora accettato gli inviti, nonché per creare automazione, ad esempio eliminando gli utenti che non hanno accettato gli inviti dopo un certo numero di giorni. Queste proprietà sono ora disponibili in MS Graph v1. Per indicazioni sull'uso di queste proprietà, vedere [Tipo di risorsa utente](/graph/api/resources/user).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gestire le sessioni di autenticazione in Azure AD'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le funzionalità di gestione delle sessioni di autenticazione consentono di configurare la frequenza con cui gli utenti devono fornire le credenziali di accesso e se devono fornire le credenziali dopo la chiusura e la riapertura dei browser per offrire maggiore sicurezza e flessibilità nell'ambiente.
 
Inoltre, la gestione delle sessioni di autenticazione si applicava solo a First Factor Authentication Azure AD aggiunti, Azure AD ibrido aggiunti e Azure AD dispositivi registrati. La gestione delle sessioni di autenticazione verrà ora applicata anche all'autenticazione a più fattori. Per altre informazioni, vedere Configurare [la gestione delle sessioni di autenticazione con l'accesso condizionale.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nuove app federate disponibili nella raccolta Azure AD applicazioni - Giugno 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A giugno 2020 sono state aggiunte le 29 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Shopify Plus,](../saas-apps/shopify-plus-tutorial.md) [Ekarda,](../saas-apps/ekarda-tutorial.md) [MailGates,](../saas-apps/mailgates-tutorial.md) [BullseyeTDP,](../saas-apps/bullseyetdp-tutorial.md) [Raketa,](../saas-apps/raketa-tutorial.md) [Segment,](../saas-apps/segment-tutorial.md) [Ai Auditor,](https://www.mindbridge.ai/products/ai-auditor/) [Pobuca Connect,](https://app.pobu.ca/) [Proto.io,](../saas-apps/proto.io-tutorial.md) [Gatekeeper,](https://www.gatekeeperhq.com/) [Hub Planner,](../saas-apps/hub-planner-tutorial.md) [Ansira-Partner Go-to-Market Toolbox,](https://ansira.com/technology/channel-engagement) [IBM Digital Business Automation on Cloud,](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md) [Kisi Physical Security,](../saas-apps/kisi-physical-security-tutorial.md) [ViewpointOne,](https://team.viewpoint.com/) [IntelligenceBank,](../saas-apps/intelligencebank-tutorial.md) [pymetrics,](../saas-apps/pymetrics-tutorial.md) [Zero,](https://www.teamzero.com/) [InStation,](https://instation.invillia.com/) [edX for Business SAML 2.0 Integration,](../saas-apps/edx-for-business-saml-integration-tutorial.md) [MOOC Office 365,](https://mooc.office365-training.com/en/) [SmartKargo,](../saas-apps/smartkargo-tutorial.md) [PKIsigning platform,](https://platform.pkisigning.nl/) [SiteIntel,](../saas-apps/siteintel-tutorial.md) [Field iD,](../saas-apps/field-id-tutorial.md) [Curricula SAML,](../saas-apps/curricula-saml-tutorial.md) [Perforce Helix Core - Helix Authentication Service,](../saas-apps/perforce-helix-core-tutorial.md) [MyCompliance Cloud,](https://cloud.metacompliance.com/) [Smallstep SSH](https://smallstep.com/sso-ssh/)  

È anche possibile trovare la documentazione di tutte le applicazioni da https://aka.ms/AppsTutorial qui. Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>I connettori API per l'iscrizione self-service di identità esterne sono ora in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I connettori API delle identità esterne consentono di sfruttare le API Web per integrare l'iscrizione self-service con sistemi cloud esterni. Ciò significa che è ora possibile richiamare le API Web come passaggi specifici in un flusso di iscrizione per attivare flussi di lavoro personalizzati basati sul cloud. Ad esempio, è possibile usare i connettori API per:

- Eseguire l'integrazione con flussi di lavoro di approvazione personalizzati.
- Eseguire la verifica dell'identità
- Convalidare i dati di input dell'utente
- Sovrascrivere gli attributi utente
- Eseguire logica di business personalizzata

Per altre informazioni su tutte le esperienze possibili con i connettori API, vedere Usare i connettori API per personalizzare ed estendere l'iscrizione [self-service](../external-identities/api-connectors-overview.md)o Personalizzare l'iscrizione [self-service](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)di identità esterne con integrazioni api Web.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Effettuare il provisioning su richiesta e consentire agli utenti di accedere alle app in pochi secondi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il Azure AD provisioning del servizio attualmente funziona in modo ciclico. Il servizio viene eseguito ogni 40 minuti. La [funzionalità di provisioning su](https://aka.ms/provisionondemand) richiesta consente di selezionare un utente ed eseguirne il provisioning in pochi secondi. Questa funzionalità consente di risolvere rapidamente i problemi di provisioning, senza dover eseguire un riavvio per forzare il riavvio del ciclo di provisioning. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nuova autorizzazione per l'uso Azure AD entitlement in Graph

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione entitlement
 
Una nuova autorizzazione delegata EntitlementManagement.Read.All è ora disponibile per l'uso con l'entitlement API Gestione in Microsoft Graph beta. Per altre informazioni sulle API disponibili, vedere Working with the Azure AD entitlement management API (Uso [dell'API di gestione entitlement).](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)

---

### <a name="identity-protection-apis-available-in-v10"></a>API di Identity Protection disponibili nella versione 1.0

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le API riskyUsers e riskDetections Microsoft Graph sono ora disponibili a livello generale. Ora che sono disponibili nell'endpoint 1.0, è possibile usarli nell'ambiente di produzione. Per altre informazioni, vedere la documentazione [Microsoft Graph](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Le etichette di riservatezza per applicare i criteri Microsoft 365 gruppi sono ora disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 

È ora possibile creare etichette di riservatezza e usare le impostazioni delle etichette per applicare i criteri Microsoft 365 gruppi, inclusi i criteri di privacy (pubblico o privato) e di accesso degli utenti esterni. È possibile creare un'etichetta con l'informativa sulla privacy come Privata e criteri di accesso utente esterni per non consentire l'aggiunta di utenti guest. Quando un utente applica questa etichetta a un gruppo, il gruppo sarà privato e non sarà consentito aggiungere utenti guest al gruppo. 

Le etichette di riservatezza sono importanti per proteggere i dati business critical e consentono di gestire i gruppi su larga scala, in modo conforme e sicuro. Per indicazioni sull'uso delle etichette di riservatezza, vedere Assegnare etichette di riservatezza Microsoft 365 [gruppi in Azure Active Directory (anteprima).](../enterprise-users/groups-assign-sensitivity-labels.md)
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aggiornamenti al supporto per Microsoft Identity Manager per Azure AD Premium clienti

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
supporto di Azure è ora disponibile per i Azure AD di integrazione di Microsoft Identity Manager 2016, fino alla fine del supporto esteso per Microsoft Identity Manager 2016. Per altre informazioni, [vedere Support update for Azure AD Premium customers using Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>L'uso delle condizioni di appartenenza ai gruppi nella configurazione delle attestazioni SSO è aumentato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
In precedenza, il numero di gruppi che era possibile usare quando si modificano le attestazioni in modo condizionale in base all'appartenenza ai gruppi all'interno di una singola configurazione dell'applicazione era limitato a 10. L'uso delle condizioni di appartenenza ai gruppi nella configurazione delle attestazioni SSO è ora aumentato a un massimo di 50 gruppi. Per altre informazioni su come configurare le attestazioni, vedere Configurazione delle attestazioni [SSO delle applicazioni aziendali](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Abilitazione della formattazione di base nel componente Testo pagina di accesso nella personalizzazione aziendale.

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La funzionalità Di personalizzazione aziendale nell'esperienza di accesso Azure AD/Microsoft 365 è stata aggiornata per consentire al cliente di aggiungere collegamenti ipertestuali e formattazione semplice, tra cui il grassetto, la sottolineatura e il corsivo. Per indicazioni sull'uso di questa funzionalità, vedere Aggiungere la personalizzazione alla pagina di Azure Active Directory [di accesso dell'organizzazione.](./customize-branding.md)

---

### <a name="provisioning-performance-improvements"></a>Miglioramenti delle prestazioni del provisioning

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning è stato aggiornato per ridurre il tempo necessario per il [completamento di un ciclo incrementale.](../app-provisioning/how-provisioning-works.md#incremental-cycles) Ciò significa che il provisioning di utenti e gruppi nelle applicazioni sarà più veloce di quanto non lo fosse in precedenza. Tutti i nuovi processi di provisioning creati dopo il 10/06/2020 trarranno automaticamente vantaggio dai miglioramenti delle prestazioni. Tutte le applicazioni configurate per il provisioning prima del 10/06/2020 dovranno essere riavviate una volta dopo il 10/06/2020 per sfruttare i miglioramenti delle prestazioni. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Annuncio della deprecazione di ADAL e MS Graph Parity

**Tipo:** Deprecato  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ora che Microsoft Authentication Libraries (MSAL) è disponibile, non verranno più aggiunti nuove funzionalità alle librerie di autenticazione di Azure Active Directory (ADAL) e le patch di sicurezza termineranno il 30 giugno 2022. Per altre informazioni su come eseguire la migrazione a MSAL, vedere Eseguire la migrazione di [applicazioni a Microsoft Authentication Library (MSAL).](../develop/msal-migration.md)

Inoltre, è stato completato il lavoro per rendere disponibili tutte le Azure AD Graph tramite MICROSOFT Graph. Pertanto, Azure AD api Graph riceveranno solo correzioni di bug e correzioni di sicurezza fino al 30 giugno 2022. Per altre informazioni, vedere [Aggiornare le applicazioni per l'uso di Microsoft Authentication Library e dell Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>Maggio 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Ritiro delle proprietà nelle API signIns, riskyUsers e riskDetections

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Attualmente, i tipi enumerati vengono usati per rappresentare la proprietà riskType sia nell'API riskDetections che in riskyUserHistoryItem (in anteprima). I tipi enumerati vengono usati anche per la proprietà riskEventTypes nell'API signIns. In futuro queste proprietà verranno rappresentate come stringhe. 

I clienti devono passare alla proprietà riskEventType nell'API beta riskDetections e riskyUserHistoryItem e alla proprietà riskEventTypes_v2 nell'API beta signIns entro il 9 settembre 2020. A tale data verranno ritirate le proprietà riskType e riskEventTypes correnti. Per altre informazioni, vedere Modifiche alle proprietà degli eventi di rischio e [API di Identity Protection in Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Deprecazione della proprietà riskEventTypes nell'API signIns v1.0 Microsoft Graph

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

I tipi enumerati passano ai tipi stringa quando rappresentano le proprietà degli eventi di rischio Microsoft Graph settembre 2020. Oltre a influire sulle API di anteprima, questa modifica inciderà anche sull'API signIns in produzione.

È stata introdotta una nuova riskEventsTypes_v2 (stringa) all'API signIns v1.0. La proprietà riskEventTypes (enum) corrente verrà ritirata l'11 giugno 2022 in base ai criteri di deprecazione Microsoft Graph criteri di deprecazione. I clienti devono passare alla proprietà riskEventTypes_v2 nell'API signIns v1.0 entro l'11 giugno 2022. Per altre informazioni, vedere Deprecazione della proprietà [riskEventTypes nell'API signIns v1.0 Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Modifiche imminenti alle notifiche di posta elettronica MFA

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Vengono apportate le modifiche seguenti alle notifiche di posta elettronica per l'autenticazione a più fattori cloud:

Le notifiche tramite posta elettronica verranno inviate dall'indirizzo seguente: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . È in corso l'aggiornamento del contenuto dei messaggi di posta elettronica di avviso di frode per indicare meglio i passaggi necessari per sbloccare gli usi.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nuova iscrizione self-service per gli utenti nei domini federati che non possono accedere a Microsoft Teams perché non sono sincronizzati con Azure Active Directory.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 

Attualmente, gli utenti che si trova in domini federati in Azure AD, ma che non sono sincronizzati nel tenant, non possono accedere a Teams. A partire dalla fine di giugno, questa nuova funzionalità consentirà di eseguire questa operazione estendendo la funzionalità di iscrizione verificata tramite posta elettronica esistente. In questo modo gli utenti che possono accedere a un IdP federato, ma che non hanno ancora un oggetto utente in ID Azure, potranno creare automaticamente un oggetto utente ed essere autenticati per Teams. L'oggetto utente verrà contrassegnato come "iscrizione self-service". Si tratta di un'estensione della funzionalità esistente per eseguire l'iscrizione self-sign-up verificata tramite posta elettronica che gli utenti nei domini gestiti possono eseguire e possono essere controllati usando lo stesso flag. Questa modifica verrà completata durante i due mesi seguenti. Per gli aggiornamenti della documentazione, [vedere qui](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correzione imminente: è in corso l'aggiornamento del documento di individuazione OIDC per Azure per enti pubblici cloud per fare riferimento agli endpoint Graph corretti.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire da giugno, il documento di individuazione OIDC [Microsoft Identity Platform](../develop/v2-protocols-oidc.md) e il protocollo OpenID Connect nell'endpoint cloud Azure per enti pubblici (login.microsoftonline.us) inizieranno [a](../develop/authentication-national-cloud.md) restituire l'endpoint del [grafo cloud](/graph/deployments) nazionale corretto ( o ) in base al https://graph.microsoft.us tenant https://dod-graph.microsoft.us) fornito.  Attualmente fornisce il campo "graph.microsoft.com" dell'endpoint Graph msgraph_host non corretto.  

Questa correzione di bug verrà implementazione graduale in circa 2 mesi.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure per enti pubblici gli utenti non saranno più in grado di accedere login.microsoftonline.com

**Digitare:** Pianificare la modifica  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
Il 1° giugno 2018 l'autorità Azure Active Directory (Azure AD) per Azure per enti pubblici è cambiata https://login-us.microsoftonline.com da a https://login.microsoftonline.us . Se si è proprietari di un'applicazione all'interno di un tenant Azure per enti pubblici, è necessario aggiornare l'applicazione per consentire agli utenti di accedere all'endpoint .us.

A partire dal 5 maggio, Azure AD verrà avviata l'applicazione della modifica dell'endpoint, bloccando gli utenti Azure per enti pubblici dall'accesso alle app ospitate nei tenant di Azure per enti pubblici usando l'endpoint pubblico (microsoftonline.com). Le app influenzate inizieranno a visualizzare un errore AADSTS900439 - USGClientNotSupportedOnPublicEndpoint. 

Sarà prevista un'implementazione graduale di questa modifica con l'applicazione prevista per tutte le app di giugno 2020. Per altri dettagli, vedere il [post Azure per enti pubblici blog](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>La richiesta di disconnessione singola SAML ora invia NameID nel formato corretto

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Quando un utente fa clic sulla disconnessione (ad esempio, nel portale MyApps), Azure AD invia un messaggio di disconnessione singola SAML a ogni app attiva nella sessione utente e con un URL di disconnessione configurato. Questi messaggi contengono un NameID in un formato persistente.

Se il token di accesso SAML originale ha usato un formato diverso per NameID (ad esempio, posta elettronica/UPN), l'app SAML non può correlare il NameID nel messaggio di disconnessione a una sessione esistente (poiché i NameID usati in entrambi i messaggi sono diversi), causando l'eliminazione del messaggio di disconnessione da parte dell'app SAML e dell'utente per rimanere connessi. Questa correzione rende il messaggio di disconnessione coerente con il NameID configurato per l'applicazione.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Il ruolo Amministratore identità ibride è ora disponibile con il provisioning cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Cloud Provisioning  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Gli amministratori IT possono iniziare a usare il nuovo ruolo "Amministratore ibrido" come ruolo con privilegi minimi per la configurazione Azure AD Connect provisioning cloud. Con questo nuovo ruolo non è più necessario usare il ruolo Amministratore globale per configurare il provisioning cloud. [Altre informazioni](../roles/delegate-by-task.md#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nuove app federate disponibili nella raccolta Azure AD di applicazioni - Maggio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel maggio 2020 sono state aggiunte le 36 nuove applicazioni seguenti nella raccolta di app con il supporto della federazione:

[Moula,](https://moula.com.au/pay/merchants) [Surveypal,](https://www.surveypal.com/app) [Kbot365,](https://www.konverso.ai/virtual-assistant-digital-workplace/) [TackleBox,](http://www.tacklebox.app/) [Powell Teams,](https://powell-software.com/en/powell-teams-en/) [Talentsoft Assistant,](https://msteams.talent-soft.com/) [ASC Recording Insights,](https://teams.asc-recording.app/product) [GO1,](https://www.go1.com/) [B-Engaged,](https://b-engaged.se/) [Competella Contact Center Workgroup,](http://www.competella.com/) [Asite,](http://www.asite.com/) [ImageSoft Identity,](https://identity.imagesoftinc.com/) [My IBISWorld,](https://identity.imagesoftinc.com/) [insuite,](../saas-apps/insuite-tutorial.md) [Change Process Management,](../saas-apps/change-process-management-tutorial.md) [Cyara CX Assurance Platform,](../saas-apps/cyara-cx-assurance-platform-tutorial.md) [Smart Global Governance,](../saas-apps/smart-global-governance-tutorial.md) [Prezi,](../saas-apps/prezi-tutorial.md) [Mapbox,](../saas-apps/mapbox-tutorial.md) [Datava Enterprise Service Platform,](../saas-apps/datava-enterprise-service-platform-tutorial.md) [Whimsical,](../saas-apps/whimsical-tutorial.md) [Trelica,](../saas-apps/trelica-tutorial.md) [EasySSO for Confluence,](../saas-apps/easysso-for-confluence-tutorial.md) [EasySSO for BitBucket,](../saas-apps/easysso-for-bitbucket-tutorial.md) [EasySSO for Bamboo,](../saas-apps/easysso-for-bamboo-tutorial.md) [Torii,](../saas-apps/torii-tutorial.md) [Axiad Cloud,](../saas-apps/axiad-cloud-tutorial.md) [Humanage,](../saas-apps/humanage-tutorial.md) [ColorTokens ZTNA,](../saas-apps/colortokens-ztna-tutorial.md) [CCH Tagetik,](../saas-apps/cch-tagetik-tutorial.md) [ShareVault,](../saas-apps/sharevault-tutorial.md) [Vyond,](../saas-apps/vyond-tutorial.md) [TextExpander,](../saas-apps/textexpander-tutorial.md) [Anyone Home CRM,](../saas-apps/anyone-home-crm-tutorial.md) [askSpoke,](../saas-apps/askspoke-tutorial.md) [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da https://aka.ms/AppsTutorial qui.

Per elencare l'applicazione nella raccolta Azure AD app, leggere i dettagli https://aka.ms/AzureADAppRequest qui.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità solo report per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

[La modalità solo report per Azure AD'accesso condizionale](../conditional-access/concept-conditional-access-report-only.md) consente di valutare il risultato di un criterio senza l'applicazione dei controlli di accesso. È possibile testare i criteri solo report all'interno dell'organizzazione e comprenderne l'impatto prima di abilitarli, rendendo la distribuzione più sicura e più semplice. Negli ultimi mesi è stata osservata una forte adozione della modalità solo report: oltre 26 milioni di utenti sono già nell'ambito di un criterio solo report. Con l'annuncio di oggi, Azure AD nuovi criteri di accesso condizionale verranno creati in modalità solo report per impostazione predefinita. Ciò significa che è possibile monitorare l'impatto dei criteri dal momento in cui vengono creati. Inoltre, per gli utenti che usano le API Microsoft Graph, è possibile gestire anche i criteri solo [report](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) a livello di codice. 

---

### <a name="self-service-sign-up-for-guest-users"></a>Iscrizione self-service per utenti guest

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Con identità esterne in Azure AD, è possibile consentire a utenti esterni all'organizzazione di accedere alle app e alle risorse consentendo loro di accedere usando l'identità preferita. Durante la condivisione di un'applicazione con utenti esterni, è possibile sapere sempre in anticipo chi dovrà accedere all'applicazione. Con [l'iscrizione self-service](../external-identities/self-service-sign-up-overview.md)è possibile consentire agli utenti guest di iscriversi e ottenere un account guest per le app line-of-business. Il flusso di iscrizione può essere creato e personalizzato per supportare Azure AD e le identità di social network. È anche possibile raccogliere informazioni aggiuntive sull'utente durante l'iscrizione.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>La cartella di lavoro Informazioni dettagliate e report per l'accesso condizionale è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [cartella di lavoro Informazioni dettagliate e](../conditional-access/howto-conditional-access-insights-reporting.md) report offre agli amministratori una visualizzazione di riepilogo Azure AD'accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio la funzione di ogni criterio e monitorare eventuali modifiche in tempo reale. La cartella di lavoro consente di trasmettere i dati archiviati in Monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Informazioni dettagliate e report all'interno del menu Azure AD accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello Dei dettagli dei criteri per l'accesso condizionale è disponibile in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello [dei dettagli del nuovo criterio](../conditional-access/troubleshoot-conditional-access.md) visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede Accesso condizionale o Solo report dei dettagli di accesso.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Le nuove funzionalità di query per gli oggetti directory Microsoft Graph sono in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del prodotto MS **Graph: Esperienza** per sviluppatori

Sono state introdotte nuove funzionalità per le API Microsoft Graph Directory Objects, abilitando le operazioni Conteggio, Ricerca, Filtro e Ordinamento. In questo modo gli sviluppatori potranno eseguire rapidamente query sugli oggetti directory senza soluzioni alternative, ad esempio l'ordinamento e il filtro in memoria. Per altre informazioni, vedere questo [post di blog.](https://aka.ms/CountFilterMSGraphAAD)

È attualmente disponibile l'anteprima pubblica e si cercano commenti e suggerimenti. Inviare i commenti con questo [breve sondaggio.](https://aka.ms/MsGraphAADSurveyDocs)

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurare l'accesso Single Sign-On basato su SAML usando Microsoft Graph API (Beta)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
È ora disponibile il supporto per la creazione e la configurazione di un'applicazione dalla Azure AD Gallery usando le API MS Graph in Beta. Se è necessario configurare l'accesso Single Sign-On basato su SAML per più istanze di un'applicazione, risparmiare tempo usando le API Microsoft Graph per automatizzare la configurazione dell'accesso [Single Sign-On](/graph/application-saml-sso-configure-api)basato su SAML.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Maggio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

* [8X8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Saml Token Encryption è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
[La crittografia dei token SAML](../manage-apps/howto-saml-token-encryption.md) consente di configurare le applicazioni per ricevere asserzioni SAML crittografate. La funzionalità è ora disponibile a livello generale in tutti i cloud.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Le attestazioni del nome del gruppo nei token dell'applicazione sono disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Le attestazioni di gruppo rilasciate in un token possono ora essere limitate solo ai gruppi assegnati all'applicazione.  Ciò è particolarmente importante quando gli utenti sono membri di un numero elevato di gruppi e c'è il rischio di superare i limiti delle dimensioni dei token. Con questa nuova funzionalità, la possibilità di aggiungere nomi di [gruppo ai token](../hybrid/how-to-connect-fed-group-claims.md) è disponibile a livello generale.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback supporta ora l'impostazione degli attributi dei numeri di telefono dell'ufficio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
L'app di provisioning writeback di Workday è stata migliorata per supportare ora il writeback degli attributi del numero di telefono aziendale e del numero di cellulare. Oltre alla posta elettronica e al nome utente, è ora possibile configurare l'app di provisioning Workday Writeback per il flusso dei valori dei numeri di telefono da Azure AD a Workday. Per altre informazioni su come configurare il writeback dei numeri di telefono, vedere l'esercitazione sull'app [Workday Writeback.](../saas-apps/workday-writeback-tutorial.md) 

---

### <a name="publisher-verification-preview"></a>Verifica editore (anteprima)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo
 
La verifica dell'autore (anteprima) consente agli amministratori e agli utenti finali di controllare l'autenticità degli sviluppatori di applicazioni che si integrano con Microsoft Identity Platform. Per informazioni dettagliate, vedere Verifica [dell'editore (anteprima).](../develop/publisher-verification-overview.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Flusso del codice di autorizzazione per le app a pagina singola

**Digitare:** Funzionalità modificata **Categoria di servizio:** Autenticazione **Funzionalità del prodotto: Esperienza** dello sviluppatore

A causa delle moderne restrizioni relative ai cookie di terze parti del browser, ad esempio [Safari ITP,](../develop/reference-third-party-cookies-spas.md)le applicazioni a pagina pagina devono usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere l'accesso SSO. MSAL.js v 2.x supporterà ora il flusso del codice di autorizzazione. In questo caso, come aggiornamenti corrispondenti al portale di Azure in modo da poter aggiornare l'applicazione a pagina pagina come tipo "spa" e usare il flusso del codice di autenticazione. Per indicazioni, vedere Guida introduttiva: Accesso degli utenti e ottenere un token di accesso in un'applicazione a pagina pagina [JavaScript usando il flusso del codice di autenticazione.](../develop/quickstart-v2-javascript-auth-code.md)

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Il filtro migliorato per i dispositivi è disponibile in anteprima pubblica

**Digitare:** Funzionalità modificata   
**Categoria di servizio:** Gestione dei dispositivi **Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
In precedenza, gli unici filtri che era possibile usare erano "Abilitato" e "Data attività". A questo punto, è [possibile filtrare l'elenco](../devices/device-management-azure-portal.md#device-list-filtering-preview)dei dispositivi in base a più proprietà, tra cui tipo di sistema operativo, tipo di join, conformità e altro ancora. Queste aggiunte dovrebbero semplificare l'individuazione di un dispositivo specifico.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>La nuova Registrazioni app per Azure AD B2C è ora disponibile a livello generale

**Digitare:** Funzionalità modificata   
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
La nuova Registrazioni app per Azure AD B2C è ora disponibile a livello generale. 

In precedenza, era necessario gestire le applicazioni rivolte agli utenti B2C separatamente dalle altre app usando l'esperienza "Applicazioni" legacy. Ciò significava diverse esperienze di creazione di app in diverse posizioni in Azure.

La nuova esperienza mostra tutte le registrazioni di app B2C e Azure AD le registrazioni delle app in un'unica posizione e offre un modo coerente per gestirle. Sia che sia necessario gestire un'app rivolta ai clienti o un'app che abbia accesso a Microsoft Graph per gestire le risorse Azure AD B2C Azure AD B2C a livello di codice, è necessario apprendere solo un modo per eseguire le operazioni.

È possibile raggiungere la nuova esperienza esplorando il servizio Azure AD B2C e selezionando il Registrazioni app. L'esperienza è accessibile anche dal Azure Active Directory servizio.

L Azure AD B2C Registrazioni app'esperienza è basata sull'esperienza di registrazione [app](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) generale per Azure AD tenant, ma è personalizzata per Azure AD B2C. L'esperienza "Applicazioni" legacy verrà deprecata in futuro.

Per altre informazioni, vedere [The New app registration experience for Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>Aprile 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>L'esperienza combinata di registrazione delle informazioni di sicurezza è ora disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'esperienza di registrazione combinata per Multi-Factor Authentication (MFA) e Self-Service reimpostazione della password (SSPR) è ora disponibile a livello generale. Questa nuova esperienza di registrazione consente agli utenti di registrarsi per MFA e SSPR in un unico processo passo dopo passo. Quando si distribuisce la nuova esperienza per l'organizzazione, gli utenti possono registrarsi in meno tempo e con meno problemi. Vedere il post di blog [qui](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Valutazione continua dell'accesso

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Valutazione continua dell'accesso è una nuova funzionalità di sicurezza che consente di applicare near real-time criteri alle relying party che utilizzano token di accesso Azure AD quando si verificano eventi in Azure AD (ad esempio l'eliminazione di account utente). Questa funzionalità verrà prima implementazione per i client Teams e Outlook. Per altre informazioni, leggere il [blog e](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) la [documentazione.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Accesso tramite SMS: i firstline worker possono accedere alle Azure AD supportate con il numero di telefono e senza password

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Autenticazione utente

Office sta lanciando una serie di app aziendali per dispositivi mobili che si adattano alle organizzazioni non tradizionali e ai dipendenti di organizzazioni di grandi dimensioni che non usano la posta elettronica come metodo di comunicazione principale. Queste app sono indirizzate a dipendenti in prima linea, dipendenti senza ufficio, agenti sul campo o dipendenti al dettaglio che potrebbero non ottenere un indirizzo di posta elettronica dal datore di lavoro, avere accesso a un computer o al personale IT. Questo progetto consente a questi dipendenti di accedere alle applicazioni aziendali immettendo un numero di telefono e arrotondando un codice. Per altri dettagli, vedere la documentazione [dell'amministratore e la](../authentication/howto-authentication-sms-signin.md) documentazione [dell'utente finale.](../user-help/sms-sign-in-explainer.md)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Invitare utenti interni a usare Collaborazione B2B

**Tipo:** Nuova funzionalità

**Categoria di servizio:** B2B

**Funzionalità del prodotto:**

È in corso l'espansione della funzionalità di invito B2B per consentire agli account interni esistenti di essere invitati a usare le credenziali di Collaborazione B2B in futuro. Questa operazione viene eseguita passando l'oggetto utente all'API Di invito oltre ai parametri tipici, ad esempio l'indirizzo di posta elettronica invitato. L'ID oggetto, l'UPN, l'appartenenza al gruppo, l'assegnazione di app e così via dell'utente rimangono invariati, ma in futuro useranno B2B per l'autenticazione con le credenziali del tenant principale anziché con le credenziali interne usate prima dell'invito. Per informazioni dettagliate, vedere [la documentazione](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità solo report per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

[La modalità solo report per Azure AD'accesso condizionale](../conditional-access/concept-conditional-access-report-only.md) consente di valutare il risultato di un criterio senza l'applicazione dei controlli di accesso. È possibile testare i criteri solo report all'interno dell'organizzazione e comprenderne l'impatto prima di abilitarli, rendendo la distribuzione più sicura e più semplice. Negli ultimi mesi si è visto un'adozione avanzata della modalità solo report, con oltre 26 milioni di utenti già nell'ambito di criteri solo report. Con questo annuncio, per impostazione Azure AD nuovi criteri di accesso condizionale verranno creati in modalità solo report. Ciò significa che è possibile monitorare l'impatto dei criteri dal momento in cui vengono creati. Per gli utenti che usano le API Ms Graph, è anche possibile gestire i criteri solo report a [livello di codice.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>La cartella di lavoro Informazioni dettagliate sull'accesso condizionale e creazione di report è disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La cartella di lavoro [Informazioni dettagliate sull'accesso](../conditional-access/howto-conditional-access-insights-reporting.md) condizionale e creazione di report offre agli amministratori una visualizzazione Azure AD'accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio le attività di ogni criterio e monitorare eventuali modifiche in tempo reale. La cartella di lavoro consente di trasmettere i dati archiviati in Monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Informazioni dettagliate e report nel menu Azure AD Accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello Dettagli criteri per l'accesso condizionale è in anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello [dei dettagli del nuovo criterio](../conditional-access/troubleshoot-conditional-access.md) visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede **Accesso** condizionale o **Solo** report dei dettagli di accesso.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nuove app federate disponibili nella raccolta App Azure AD - Aprile 2020

**Tipo:** Nuova funzionalità

**Categoria di servizio:** App aziendali

**Funzionalità del prodotto:** Integrazione con app di terze parti

Ad aprile 2020 sono state aggiunte queste 31 nuove app con supporto della federazione alla raccolta di app: 

[SincroPool Apps,](https://www.sincropool.com/) [SmartDB,](https://hibiki.dreamarts.co.jp/smartdb/trial/) [Float,](../saas-apps/float-tutorial.md) [LMS365,](https://lms.365.systems/) [IWT Procurement Suite,](../saas-apps/iwt-procurement-suite-tutorial.md) [Lunni,](https://lunni.fi/) [EasySSO for Jira,](../saas-apps/easysso-for-jira-tutorial.md) [Virtual Training Academy,](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft) [Meraki Dashboard,](../saas-apps/meraki-dashboard-tutorial.md) [Microsoft 365 Mover,](https://app.mover.io/login) [Speaker Engage,](https://speakerengage.com/login.php) [Honestly,](../saas-apps/honestly-tutorial.md) [Ally,](../saas-apps/ally-tutorial.md) [DutyFlow,](https://app.dutyflow.nl/) [AlertMedia,](../saas-apps/alertmedia-tutorial.md) [gr8 People,](../saas-apps/gr8-people-tutorial.md) [Pendo,](../saas-apps/pendo-tutorial.md) [HighGround,](../saas-apps/highground-tutorial.md) [Harmony,](../saas-apps/harmony-tutorial.md) [Timetabling Solutions,](../saas-apps/timetabling-solutions-tutorial.md) [SynchroNet CLICK,](../saas-apps/synchronet-click-tutorial.md)empower [,](https://www.made-in-office.com/en/) [Fortes Change Cloud,](../saas-apps/fortes-change-cloud-tutorial.md) [Litmus,](../saas-apps/litmus-tutorial.md) [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](../saas-apps/frontify-tutorial.md) [MongoDB Cloud,](../saas-apps/mongodb-cloud-tutorial.md) [TickitLMS Learn,](../saas-apps/tickitlms-learn-tutorial.md) [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [Nitro Productivity Suite,](../saas-apps/nitro-productivity-suite-tutorial.md) [Trend Micro Web Security (TMWS)](../saas-apps/trend-micro-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph di query delta per oAuth2PermissionGrant disponibile per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** MS Graph

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per oAuth2PermissionGrant è disponibile per l'anteprima pubblica. È ora possibile tenere traccia delle modifiche senza dover eseguire continuamente il polling Microsoft Graph. [Altre informazioni.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph supporto delle query delta per il contatto aziendale disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** MS Graph

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per i contatti dell'organizzazione è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire il codice esistente che esegue continuamente il polling di orgContact data by delta query per migliorare significativamente le prestazioni. [Altre informazioni.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph di query delta per l'applicazione disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** MS Graph

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per le applicazioni è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire qualsiasi codice esistente che esegue continuamente il polling dei dati dell'applicazione tramite query delta per migliorare significativamente le prestazioni. [Altre informazioni.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph di query differenziali per le unità amministrative disponibili per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** MS Graph

**Funzionalità del prodotto:** La query Delta dell'esperienza per sviluppatori per le unità amministrative è disponibile per l'anteprima pubblica. È ora possibile tenere traccia delle modifiche senza dover eseguire il polling continuo Microsoft Graph. [Altre informazioni.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gestire i numeri di telefono di autenticazione e altro ancora nelle Microsoft Graph beta

**Tipo:** Nuova funzionalità

**Categoria di servizio:** MS Graph

**Funzionalità del prodotto:** esperienza di sviluppo

Queste API sono uno strumento chiave per la gestione dei metodi di autenticazione degli utenti. È ora possibile pre-registrare e gestire a livello di codice gli autenticatori usati per MFA e la reimpostazione della password self-service. Si tratta di una delle funzionalità più richieste negli Azure AD MFA, SSPR e Microsoft Graph spazi. Le nuove API rilasciate in questa ondata offrono la possibilità di:

- Leggere, aggiungere, aggiornare e rimuovere i telefoni di autenticazione di un utente
- Reimpostare la password di un utente
- Attivare e disattivare l'accesso SMS

Per altre informazioni, vedere panoramica [dell'API Azure AD metodi di autenticazione.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

---

### <a name="administrative-units-public-preview"></a>Anteprima pubblica delle unità amministrative

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Azure AD ruoli

**Funzionalità del prodotto:** Controllo di accesso

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento definito dell'organizzazione. È possibile usare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello dettagliato. Ad esempio, un amministratore di account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze agli utenti solo nella propria unità amministrativa.

Usando le unità amministrative, un amministratore centrale può:

- Creare un'unità amministrativa per la gestione decentralizzata delle risorse
- Assegnare un ruolo con autorizzazioni amministrative solo Azure AD utenti in un'unità amministrativa
- Popolare le unità amministrative con utenti e gruppi in base alle esigenze

Per altre informazioni, vedere [Gestione delle unità amministrative in Azure Active Directory (anteprima)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ruoli predefiniti per l'amministratore della stampante e il tecnico della stampante

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Azure AD ruoli

**Funzionalità del prodotto:** Controllo di accesso

**Amministratore stampanti:** gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni delle stampanti nella soluzione Microsoft Stampa universale, incluse le impostazioni di Stampa universale Connector. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori stampante possono anche accedere ai report di stampa. 

**Tecnico stampanti:** gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione Microsoft Stampa universale. Possono anche leggere tutte le informazioni sul connettore. Le attività principali che un tecnico della stampante non può eseguire sono l'impostazione delle autorizzazioni utente per le stampanti e la condivisione delle stampanti. [Altre informazioni.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ruolo predefinito amministratore identità ibrida

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Azure AD ruoli

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono abilitare, configurare e gestire i servizi e le impostazioni relative all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD a uno dei tre metodi di autenticazione supportati&#8212;Sincronizzazione dell'hash delle password, Autenticazione pass-through (PTA) o Federazione (AD FS o provider federativo di terze parti)&#8212;e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura locale include gli agenti di provisioning e PTA. Questo ruolo concede la possibilità di abilitare l'accesso Single Sign-On facile (S-SSO) per consentire l'autenticazione semplice nei dispositivi non Windows 10 o nei computer non Windows Server 2016. Inoltre, questo ruolo concede la possibilità di visualizzare i log di accesso e di accedere all'integrità e all'analisi a scopo di monitoraggio e risoluzione dei problemi. [Altre informazioni.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ruolo predefinito Amministratore di rete

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Azure AD ruoli

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono esaminare le raccomandazioni di Microsoft sull'architettura perimetrale di rete basate sui dati di telemetria di rete provenienti dalle posizioni degli utenti. Le prestazioni di rete Microsoft 365 si basano su un'attenta architettura perimetrale di rete dei clienti aziendali, che in genere è specifica della posizione dell'utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali posizioni per ottenere misurazioni di telemetria e raccomandazioni di progettazione migliori. [Altre informazioni.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Attività in blocco e download nell'esperienza Azure AD portale di amministrazione

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Gestione utenti

**Funzionalità del prodotto:** Directory

È ora possibile eseguire attività in blocco su utenti e gruppi in Azure AD caricando un file CSV nell'esperienza Azure AD portale di amministrazione. È possibile creare utenti, eliminare utenti e invitare utenti guest. È anche possibile aggiungere e rimuovere membri da un gruppo.

È anche possibile scaricare elenchi di risorse Azure AD dall'esperienza Azure AD portale di amministrazione. È possibile scaricare l'elenco di utenti nella directory, l'elenco dei gruppi nella directory e i membri di un gruppo specifico.

Per altre informazioni, vedere quanto segue:

- [Creare utenti](../enterprise-users/users-bulk-add.md) o [invitare utenti guest](../external-identities/tutorial-bulk-invite.md)
- [Eliminare gli utenti](../enterprise-users/users-bulk-delete.md) [o ripristinare gli utenti eliminati](../enterprise-users/users-bulk-restore.md)
- [Scaricare l'elenco di utenti](../enterprise-users/users-bulk-download.md) [o l'elenco di download dei gruppi](../enterprise-users/groups-bulk-download.md)
- [Aggiungere (importare) membri](../enterprise-users/groups-bulk-import-members.md) [o rimuovere membri](../enterprise-users/groups-bulk-remove-members.md) o Scaricare [l'elenco di membri](../enterprise-users/groups-bulk-download-members.md) per un gruppo

---

### <a name="my-staff-delegated-user-management"></a>My Staff gestione utenti delegata

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Gestione utenti

**Funzionalità del prodotto:**

My Staff consente a Firstline Managers, ad esempio un responsabile del negozio, di garantire che i membri del personale siano in grado di accedere ai propri account Azure AD personali. Invece di affidarsi a un supporto tecnico centrale, le organizzazioni possono delegare attività comuni, ad esempio la reimpostazione delle password o la modifica dei numeri di telefono, a firstline manager. Con My Staff, un utente che non può accedere al proprio account può ottenere nuovamente l'accesso con un paio di clic, senza alcun supporto tecnico o personale IT. Per altre informazioni, vedere Gestire gli utenti con My Staff [(anteprima)](../roles/my-staff-configure.md) e Delegare la gestione degli utenti [My Staff (anteprima).](../user-help/my-staff-team-manager.md)

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Un'esperienza utente finale aggiornata nelle verifiche di accesso

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Verifiche di accesso

**Funzionalità del prodotto:** Governance delle identità

È stata aggiornata l'esperienza del revisore per Azure AD di accesso nel portale App personali web. Alla fine di aprile, i revisori che hanno eseguito l'accesso all'esperienza del revisore delle verifiche di accesso di Azure AD vedranno un banner che consentirà loro di provare l'esperienza aggiornata in Accesso personale. Si noti che l'esperienza delle verifiche di accesso aggiornata offre le stesse funzionalità dell'esperienza corrente, ma con un'interfaccia utente migliorata oltre alle nuove funzionalità per consentire agli utenti di essere produttivi. [Per altre informazioni sull'esperienza aggiornata, vedere](../governance/perform-access-review.md). Questa anteprima pubblica durerà fino alla fine di luglio 2020. Alla fine di luglio, i revisori che non hanno acconsentito esplicitamente all'esperienza di anteprima verranno indirizzati automaticamente Accesso personale eseguire le verifiche di accesso. Se si vuole che i revisori eservitino il passaggio permanente all'esperienza di anteprima in Accesso personale ora, effettuare [una richiesta qui.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Le app di provisioning e writeback degli utenti in ingresso workday ora supportano le versioni più recenti dell'API dei servizi Web workday

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** 

In base ai commenti e suggerimenti dei clienti, sono state aggiornate le app di provisioning e writeback degli utenti in ingresso workday nella raccolta di app aziendali per supportare le versioni più recenti dell'API dei servizi Web Workday (WWS). Con questa modifica, i clienti possono specificare la versione dell'API WWS che vogliono usare nella stringa di connessione. In questo modo i clienti possono recuperare più attributi hr disponibili nelle versioni di Workday. L'app workday writeback usa ora il servizio Web Change_Work_Contact_Info Workday consigliato per superare le limitazioni di Maintain_Contact_Info.

Se non viene specificata alcuna versione nella stringa di connessione, per impostazione predefinita, le app di provisioning in ingresso workday continueranno a usare WWS v21.1 [](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) Per passare alle API Workday più recenti per il provisioning utenti in ingresso, i clienti devono aggiornare la stringa di connessione come documentato nell'esercitazione e aggiornare anche i file XPATH usati per gli attributi di Workday, come documentato nella guida di riferimento agli attributi di [Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Per usare la nuova API per il writeback, non sono necessarie modifiche nell'app di provisioning writeback workday. Sul lato Workday verificare che l'account utente del sistema di integrazione Workday (ISU) abbia le autorizzazioni per richiamare il processo aziendale di Change_Work_Contact, come documentato nella sezione Configurare le autorizzazioni dei criteri di sicurezza dei processi [aziendali.](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) 

La guida all'esercitazione [è stata aggiornata per](../saas-apps/workday-inbound-tutorial.md) riflettere il supporto della nuova versione dell'API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gli utenti con ruolo di accesso predefinito sono ora nell'ambito per il provisioning

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

In una storia, gli utenti con il ruolo di accesso predefinito non sono più in ambito per il provisioning. È stato ricevuto un feedback che indica che i clienti vogliono che gli utenti con questo ruolo siano nell'ambito del provisioning. A partire dal 16 aprile 2020, tutte le nuove configurazioni di provisioning consentono il provisioning degli utenti con il ruolo di accesso predefinito. Gradualmente si modificherà il comportamento delle configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. [Altre informazioni.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Aggiornamento dell'interfaccia utente del provisioning

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning è stata aggiornata per creare una visualizzazione di gestione più mirata. Quando si passa al pannello di provisioning per un'applicazione aziendale già configurata, sarà possibile monitorare facilmente lo stato di avanzamento del provisioning e gestire azioni come l'avvio, l'arresto e il riavvio del provisioning. [Altre informazioni.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>La convalida dinamica delle regole del gruppo è ora disponibile per l'anteprima pubblica

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Gestione gruppi

**Funzionalità del prodotto:** Collaborazione

Azure Active Directory (Azure AD) offre ora i mezzi per convalidare le regole di gruppo dinamico. Nella scheda **Convalida regole** è possibile convalidare la regola dinamica rispetto ai membri del gruppo di esempio per verificare che la regola funzioni come previsto. Quando si creano o si aggiornano le regole del gruppo dinamico, gli amministratori vogliono sapere se un utente o un dispositivo sarà membro del gruppo. Ciò consente di valutare se un utente o un dispositivo soddisfa i criteri della regola e aiuta a risolvere i problemi quando non è prevista l'appartenenza.

Per altre informazioni, vedere [Convalidare una regola di appartenenza a un gruppo dinamico (anteprima).](../enterprise-users/groups-dynamic-rule-validation.md)

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Punteggio di sicurezza dell'identità - Impostazioni predefinite della sicurezza e aggiornamenti delle azioni di miglioramento dell'autenticazione a più fattori

**Tipo:** Funzionalità modificata

**Categoria di servizio:** N/D

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

**Supporto delle impostazioni predefinite di sicurezza per Azure AD di miglioramento:** Microsoft Secure Score aggiornerà le azioni di miglioramento per supportare le impostazioni predefinite di sicurezza [in Azure AD](./concept-fundamentals-security-defaults.md), che semplificano la protezione dell'organizzazione con impostazioni di sicurezza preconfigurato per attacchi comuni. Ciò influirà sulle azioni di miglioramento seguenti:

- Assicurarsi che tutti gli utenti possano completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedere l'autenticazione a più fattori per i ruoli amministrativi
- Abilitare i criteri per bloccare l'autenticazione legacy
 
**Aggiornamenti delle azioni di miglioramento MFA:** Per riflettere la necessità per le aziende di garantire la massima sicurezza durante l'applicazione di criteri che funzionano con la propria azienda, Microsoft Secure Score ha rimosso tre azioni di miglioramento incentrato sull'autenticazione a più fattori e ne ha aggiunte due.

Rimozione delle azioni di miglioramento:

- Registrare tutti gli utenti per l'autenticazione a più fattori
- Richiedere l'autenticazione a più fattori per tutti gli utenti
- Richiedere l'autenticazione a più Azure AD ruoli con privilegi

Aggiunta di azioni di miglioramento:

- Assicurarsi che tutti gli utenti possano completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedere l'autenticazione a più fattori per i ruoli amministrativi

Queste nuove azioni di miglioramento richiedono la registrazione degli utenti o degli amministratori per l'autenticazione a più fattori (MFA) nella directory e la definizione del set di criteri più adatto alle esigenze dell'organizzazione. L'obiettivo principale è avere flessibilità assicurando al tempo stesso che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica delle identità basate sul rischio. Questo può assumere la forma di avere più criteri che applicano decisioni con ambito o di impostare le impostazioni predefinite per la sicurezza (a partire dal 16 marzo) che consentono a Microsoft di decidere quando richiedere agli utenti l'autenticazione a più fattori. [Altre informazioni sulle novità di Microsoft Secure Score.](/microsoft-365/security/mtp/microsoft-secure-score#whats-new)

---

## <a name="march-2020"></a>Marzo 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Account di Azure Active Directory non gestiti nell'aggiornamento B2B per marzo 2021

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
A partire dal **31 marzo 2021,** Microsoft non supporterà più il riscatto degli inviti creando account e tenant Azure Active Directory (Azure AD) non gestiti per gli scenari di collaborazione B2B. Per prepararsi a questo, si consiglia di acconsentire esplicitamente all'autenticazione con passcode una sola volta tramite [posta elettronica.](../external-identities/one-time-passcode.md)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gli utenti con il ruolo di accesso predefinito saranno nell'ambito per il provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
In una cronologia, gli utenti con il ruolo di accesso predefinito non sono più in ambito per il provisioning. È stato ricevuto un feedback che indica che i clienti vogliono che gli utenti con questo ruolo siano nell'ambito del provisioning. Si sta lavorando alla distribuzione di una modifica in modo che tutte le nuove configurazioni di provisioning consentano il provisioning degli utenti con il ruolo di accesso predefinito. Gradualmente, si modificherà il comportamento per le configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. Non è necessaria alcuna azione da parte del cliente. Dopo aver apportato questa [](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) modifica, verrà pubblicato un aggiornamento nella documentazione.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD collaborazione B2B sarà disponibile in Microsoft Azure gestiti da 21 tenantVianet (Azure China (21Vianet))

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di collaborazione B2B Azure AD saranno rese disponibili in Microsoft Azure gestito da 21 tenantvianet (Azure China (21Vianet)), consentendo agli utenti di un tenant Azure China (21Vianet) di collaborare senza problemi con gli utenti di altri tenant Azure China (21Vianet). [Altre informazioni sulla collaborazione Azure AD B2B.](/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD Collaborazione B2B riprogettazione dell'indirizzo di posta elettronica di invito

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I messaggi [di](../external-identities/invitation-email-elements.md) posta elettronica inviati dal servizio di invito Azure AD collaborazione B2B per invitare gli utenti alla directory verranno riprogettati per rendere più chiare le informazioni sull'invito e i passaggi successivi dell'utente.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Le modifiche ai criteri HomeRealmDiscovery verranno visualizzate nei log di controllo

**Tipo:** Correzione  
**Categoria di servizio:** revisione  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È stato risolto un bug per cui le [modifiche ai criteri HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) non venivano incluse nei log di controllo. Sarà ora possibile vedere quando e come è stato modificato il criterio e da chi. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nuove app federate disponibili nella raccolta App Azure AD - Marzo 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A marzo 2020 sono state aggiunte queste 51 nuove app con supporto della federazione alla raccolta di app: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md), [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), Wisdom [By Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Cloud Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://www.bizzcontact.app/), [Elqano SSO,](../saas-apps/elqano-sso-tutorial.md) [MarketSignShare,](http://www.signshare.com/) [CrossKnowledge Learning Suite,](../saas-apps/crossknowledge-learning-suite-tutorial.md) [Netvision Compas,](../saas-apps/netvision-compas-tutorial.md) [FCM HUB,](../saas-apps/fcm-hub-tutorial.md) [TRAIN A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](../saas-apps/golinks-tutorial.md) [Datadog,](../saas-apps/datadog-tutorial.md) [Zscaler B2B User Portal,](../saas-apps/zscaler-b2b-user-portal-tutorial.md) [LIFT](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams,](https://www.devfinition.com/) [Aster,](https://demo.asterapp.io/login) [Skills Workflow,](../saas-apps/skills-workflow-tutorial.md) [Node Insight,](https://admin.nodeinsight.com/AADLogin.aspx) [IP Platform,](../saas-apps/ip-platform-tutorial.md) [InVision,](../saas-apps/invision-tutorial.md) [Pipedrive,](../saas-apps/pipedrive-tutorial.md) [Showcase Workshop,](https://app.showcaseworkshop.com/) [Greenlight Integration Platform,](../saas-apps/greenlight-integration-platform-tutorial.md) [Greenlight Compliant Access Management,](../saas-apps/greenlight-compliant-access-management-tutorial.md) [Grok Learning,](../saas-apps/grok-learning-tutorial.md) [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](../saas-apps/khoros-care-tutorial.md) [AskYourTeam,](../saas-apps/askyourteam-tutorial.md) [TruNarrative,](../saas-apps/trunarrative-tutorial.md) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Bizagi Studio per Digital Process Automation,](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md) [insuiteX,](https://www.insuite.jp/) [sybo,](https://www.systexsoftware.com.tw/) [Britive,](../saas-apps/britive-tutorial.md) [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days,](../saas-apps/e-days-tutorial.md) [Kollective SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](../saas-apps/korn-ferry-360-tutorial.md) [Campus Campus,](../saas-apps/campus-cafe-tutorial.md) [Catchpoint,](../saas-apps/catchpoint-tutorial.md) [Code42](../saas-apps/code42-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD Collaborazione B2B disponibili nei tenant Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le Azure AD di Collaborazione B2B sono ora disponibili tra Azure per enti pubblici tenant.  Per scoprire se il tenant è in grado di usare queste funzionalità, seguire le istruzioni in Come è possibile determinare se collaborazione [B2B](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)è disponibile nel tenant di Azure US Government? .

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Monitoraggio di Azure'integrazione dei log di Azure è ora disponibile in Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Monitoraggio di Azure'integrazione con Azure AD log è ora disponibile in Azure per enti pubblici. È possibile instradare Azure AD log (log di controllo e di accesso) a un account di archiviazione, a Un hub eventi e a Log Analytics. Consultare la documentazione dettagliata [e i](../reports-monitoring/concept-activity-logs-azure-monitor.md) piani di distribuzione per la creazione di [report](../reports-monitoring/plan-monitoring-and-reporting.md) e il monitoraggio per Azure AD scenari.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aggiornamento di Identity Protection in Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo molto contenti di condividere che è stata [](../identity-protection/overview-identity-protection.md)ora aggiornata l'esperienza Azure AD Identity Protection nel portale Microsoft Azure per enti pubblici   [.](https://portal.azure.us/) Per altre informazioni, vedere il post di [blog dell'annuncio](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Ripristino di emergenza: scaricare e archiviare la configurazione di provisioning

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il Azure AD di provisioning offre un set di funzionalità di configurazione avanzate. I clienti devono essere in grado di salvare la configurazione in modo che possano fare riferimento a tale configurazione in un secondo momento o eseguire il rollback a una versione valida nota. È stata aggiunta la possibilità di scaricare la configurazione di provisioning come file JSON e caricarla quando necessario. [Altre informazioni](../app-provisioning/export-import-provisioning-configuration.md)

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>La reimpostazione della password self-service richiede ora due gate per gli amministratori in Microsoft Azure gestiti da 21Vianet (Azure China (21Vianet)) 

**Tipo:** Funzionalità modificata  
**Categoria di servizio: Self-Service** reimpostazione della password  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
In precedenza Microsoft Azure gestito da 21Vianet (Azure China (21Vianet)), gli amministratori che usano la reimpostazione della password self-service (SSPR) per reimpostare le proprie password avevano bisogno di un solo "gate" (sfida) per dimostrare la propria identità. Nei cloud pubblici e in altri cloud nazionali, gli amministratori in genere devono usare due gate per dimostrare la propria identità quando si usa la password della password di accesso in modalità SSPR. Tuttavia, poiché non sono stati supportati SMS o chiamate telefoniche Azure China (21Vianet), gli amministratori hanno consentito la reimpostazione della password con un solo gate.

Si sta creando la parità di funzionalità SSPR tra Azure China (21Vianet) e il cloud pubblico. In futuro, gli amministratori devono usare due gate quando usano la password della password di sicurezza. Saranno supportati SMS, chiamate telefoniche e codici e notifiche dell'app Authenticator. [Altre informazioni](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences)

---

### <a name="password-length-is-limited-to-256-characters"></a>La lunghezza della password è limitata a 256 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Per garantire l'affidabilità del Azure AD, le password utente sono ora limitate a 256 caratteri. Agli utenti con password più lunghe verrà chiesto di modificare la password al successivo accesso, contattando l'amministratore o usando la funzionalità di reimpostazione della password self-service.

Questa modifica è stata abilitata il 13 marzo 2020 alle 10:00 PST (18:00 UTC) e l'errore è AADSTS 50052, InvalidPasswordExceedsMaxLength. Per altri [dettagli, vedere l'avviso](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) sulle modifiche di rilievo.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD log di accesso sono ora disponibili per tutti i tenant gratuiti tramite il portale di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
A partire da questo momento, i clienti che hanno tenant gratuiti possono accedere Azure AD [log](../reports-monitoring/concept-sign-ins.md) di accesso dal portale di Azure per un massimo di 7 giorni. In precedenza, i log di accesso erano disponibili solo per i clienti con Azure Active Directory Premium licenze. Con questa modifica, tutti i tenant possono accedere a questi log tramite il portale.

> [!NOTE]
> I clienti devono comunque avere una licenza Premium (Azure Active Directory Premium P1 o P2) per accedere ai log di accesso tramite l'API Microsoft Graph e Monitoraggio di Azure.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Deprecazione dell'opzione Gruppi a livello di directory da Impostazioni generali gruppi portale di Azure

**Tipo:** Deprecato  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Per offrire ai clienti un modo più flessibile per creare gruppi a livello di directory che meglio soddisfano le proprie esigenze, è stata sostituita l'opzione Gruppi a livello di **directory** dalle impostazioni Generali gruppi nel portale di Azure con un collegamento alla documentazione sui gruppi  >   [dinamici](../enterprise-users/groups-dynamic-membership.md). La documentazione è stata migliorata per includere altre istruzioni in modo che gli amministratori possano creare gruppi di tutti gli utenti che includono o escludono gli utenti guest.

---

## <a name="february-2020"></a>Febbraio 2020

### <a name="upcoming-changes-to-custom-controls"></a>Modifiche imminenti ai controlli personalizzati

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Si prevede di sostituire l'anteprima corrente dei controlli personalizzati con un approccio che consenta alle funzionalità di autenticazione fornite dai partner di funzionare senza problemi con l'esperienza dell'amministratore Azure Active Directory e dell'utente finale. Attualmente, le soluzioni MFA dei partner devono affrontare le limitazioni seguenti: funzionano solo dopo l'immissione di una password; non fungono da autenticazione a più fattori per l'autenticazione dettagliata in altri scenari chiave; e non si integrano con le funzioni di gestione delle credenziali dell'utente finale o dell'amministratore. La nuova implementazione consentirà ai fattori di autenticazione forniti dai partner di funzionare insieme ai fattori predefiniti per gli scenari chiave, tra cui registrazione, utilizzo, attestazioni MFA, autenticazione avanzata, creazione di report e registrazione. 

I controlli personalizzati continueranno a essere supportati in anteprima insieme alla nuova progettazione fino a raggiungere la disponibilità generale. A questo punto, i clienti saranno in grado di eseguire la migrazione alla nuova progettazione. A causa delle limitazioni dell'approccio corrente, i nuovi provider non saranno disponibili fino a quando non sarà disponibile la nuova progettazione. Stiamo lavorando a stretto contatto con clienti e provider e comunicheremo la sequenza temporale man appena ci si avvicina. [Altre informazioni](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Punteggio di sicurezza dell'identità - Aggiornamenti delle azioni di miglioramento dell'autenticazione a più fattori

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Per riflettere la necessità per le aziende di garantire la sicurezza più avanzata durante l'applicazione di criteri che funzionano con la propria azienda, Microsoft Secure Score rimuove tre azioni di miglioramento incentrato sull'autenticazione a più fattori (MFA) e ne aggiunge due.

Le azioni di miglioramento seguenti verranno rimosse:

- Registrare tutti gli utenti per MFA
- Richiedere l'autenticazione a più fattori per tutti gli utenti
- Richiedere l'autenticazione a più fattori Azure AD ruoli con privilegi elevati

Verranno aggiunte le azioni di miglioramento seguenti:

- Assicurarsi che tutti gli utenti possano completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedi autenticazione a più fattori per i ruoli amministrativi

Queste nuove azioni di miglioramento richiederanno la registrazione degli utenti o degli amministratori per MFA nella directory e la definizione del set di criteri più adatto alle esigenze dell'organizzazione. L'obiettivo principale è avere flessibilità assicurando al tempo stesso che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica delle identità basate sul rischio. Questa operazione può assumere la forma di impostazione delle impostazioni predefinite di sicurezza che consentono a Microsoft di decidere quando richiedere agli utenti l'autenticazione a più fattori o di avere più criteri che applicano decisioni con ambito. Come parte di questi aggiornamenti delle azioni di miglioramento, i criteri di protezione di base non verranno più inclusi nei calcoli di assegnazione dei punteggi. [Altre informazioni sulle novità in Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
È stato ricevuto un feedback che Azure AD Domain Services i clienti vogliono una maggiore flessibilità nella selezione dei livelli di prestazioni per le istanze. A partire dal 1° febbraio 2020 è stato fatto il passaggio da un modello dinamico (in cui Azure AD determina le prestazioni e il piano tariffario in base al numero di oggetti) a un modello di selezione automatica. Ora i clienti possono scegliere un livello di prestazioni corrispondente al proprio ambiente. Questa modifica consente anche di abilitare nuovi scenari come foreste di risorse e funzionalità Premium come i backup giornalieri. Il numero di oggetti è ora illimitato per tutti gli SKU, ma continueremo a offrire suggerimenti per il conteggio degli oggetti per ogni livello.

**Non è necessaria alcuna azione immediata da parte del cliente.** Per i clienti esistenti, il livello dinamico in uso il 1° febbraio 2020 determina il nuovo livello predefinito. Il risultato di questa modifica non comporta alcun impatto sui prezzi o sulle prestazioni. In futuro, i Azure AD DS clienti dovranno valutare i requisiti di prestazioni quando cambiano le dimensioni della directory e le caratteristiche del carico di lavoro. Il passaggio da un livello di servizio all'altro continuerà a essere un'operazione senza tempi di inattività e i clienti non verranno più spostati automaticamente in nuovi livelli in base all'aumento della directory. Inoltre, non verranno aumentati i prezzi e i nuovi prezzi saranno allineati al modello di fatturazione corrente. Per altre informazioni, vedere la documentazione [Azure AD DS SKU](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) e la pagina [Azure AD Domain Services prezzi.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nuove app federate disponibili nella raccolta App Azure AD - Febbraio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
A febbraio 2020 sono state aggiunte queste 31 nuove app con supporto della federazione alla raccolta di app: 

[IamIP Patent Platform,](../saas-apps/iamip-patent-platform-tutorial.md) [Experience Cloud,](../saas-apps/experience-cloud-tutorial.md) [NS1 SSO For Azure,](../saas-apps/ns1-sso-azure-tutorial.md) [Barracuda Email Security Service,](https://ess.barracudanetworks.com/sso/azure) [ABa Reporting,](https://myaba.co.uk/client-access/signin/auth/msad) [In Case of Crisis - Online Portal,](../saas-apps/in-case-of-crisis-online-portal-tutorial.md) [BIC Cloud Design,](../saas-apps/bic-cloud-design-tutorial.md) [Beekeeper Azure AD Data Connector,](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada Command](../saas-apps/verkada-command-tutorial.md), [Splashtop,](../saas-apps/splashtop-tutorial.md) [Syxsense,](../saas-apps/syxsense-tutorial.md) [EAB Navigate,](../saas-apps/eab-navigate-tutorial.md) [New Relic (versione limitata),](../saas-apps/new-relic-limited-release-tutorial.md) [Thulium,](https://admin.thulium.com/login/instance) [Ticket Manager,](../saas-apps/ticketmanager-tutorial.md)Template [Chooser per Teams,](https://links.officeatwork.com/templatechooser-download-teams) [Beesy,](https://www.beesy.me/index.php/site/login) [Health Support System,](../saas-apps/health-support-system-tutorial.md) [WAKE,](https://app.mural.co/signup) [Hive,](../saas-apps/hive-tutorial.md) [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](../saas-apps/firmex-vdr-tutorial.md) [ThingLink per](https://www.thinglink.com/)docenti e istituti di formazione, [Coda,](../saas-apps/coda-tutorial.md) [NearpodApp,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO,](../saas-apps/wedo-tutorial.md) [InvitePeople,](https://invitepeople.com/login) [Reprints Desk - Article Galaxy,](../saas-apps/reprints-desk-article-galaxy-tutorial.md) [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Febbraio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD per le chiavi di sicurezza FIDO2 in ambienti ibridi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
È stata annunciata l'anteprima pubblica del supporto Azure AD per le chiavi di sicurezza FIDO2 negli ambienti ibridi. Gli utenti possono ora usare le chiavi di sicurezza FIDO2 per accedere ai dispositivi Azure AD ibrido aggiunti Windows 10 e ottenere un accesso trasparente alle risorse locali e cloud. Il supporto per gli ambienti ibridi è stata la funzionalità più richiesta dai clienti senza password da quando è stata inizialmente avviata l'anteprima pubblica per il supporto FIDO2 nei Azure AD aggiunti. L'autenticazione senza password che usa tecnologie avanzate come la biometria e la crittografia a chiave pubblica/privata offre praticità e facilità d'uso pur essendo sicura. Con questa anteprima pubblica è ora possibile usare l'autenticazione moderna come le chiavi di sicurezza FIDO2 per accedere alle risorse di Active Directory tradizionali. Per altre informazioni, vedere [SSO to on-premises resources (Accesso SSO alle risorse locali).](../authentication/howto-authentication-passwordless-security-key-on-premises.md) 

Per iniziare, vedere [Abilitare le chiavi di sicurezza FIDO2](../authentication/howto-authentication-passwordless-security-key.md) per il tenant per istruzioni dettagliate. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>La nuova esperienza Account personale è ora disponibile a livello generale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Profilo/account personale  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Il mio account, il negozio unico per tutte le esigenze di gestione degli account degli utenti finali, è ora disponibile a livello generale. Gli utenti finali possono accedere a questo nuovo sito tramite URL o nell'intestazione della nuova App personali esperienza. Per altre informazioni su tutte le funzionalità self-service offerte dalla nuova esperienza, vedere [Panoramica del portale account personale](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aggiornamento dell'URL del sito dell'account personale a myaccount.microsoft.com

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Profilo/account personale  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
La nuova esperienza utente finale Account personale aggiornerà l'URL `https://myaccount.microsoft.com` in nel mese successivo. Per altre informazioni sull'esperienza e su tutte le funzionalità self-service dell'account offerte agli utenti finali, vedere la guida del [portale Account personale](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Gennaio 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Il nuovo portale App personali è ora disponibile a livello generale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Aggiornare l'organizzazione al nuovo portale App personali disponibile a livello generale. Per altre informazioni sul nuovo portale e sulle raccolte, vedere [Creare raccolte nel portale App personali .](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Le aree di lavoro Azure AD sono state rinominate in raccolte

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App personali   
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Le aree di lavoro, i filtri che gli amministratori possono configurare per organizzare le app degli utenti, verranno ora definite raccolte. Per altre informazioni su come configurarle, vedere [Creare raccolte nel portale App personali .](../manage-apps/access-panel-collections.md)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C l'iscrizione e l'accesso tramite criteri personalizzati (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Con l'iscrizione e l'accesso al numero di telefono, sviluppatori e aziende possono consentire ai clienti di iscriversi e accedere usando una password una sola volta inviata al numero di telefono dell'utente tramite SMS. Questa funzionalità consente inoltre al cliente di modificare il numero di telefono in caso di perdita dell'accesso al telefono. Grazie alla potenza dei criteri personalizzati e dell'iscrizione e dell'accesso tramite telefono, gli sviluppatori e le aziende possono comunicare il proprio marchio tramite la personalizzazione della pagina. Informazioni su come configurare [l'iscrizione e l'accesso](../../active-directory-b2c/phone-authentication-user-flows.md)tramite telefono con criteri personalizzati in Azure AD B2C .
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nuovi connettori di provisioning nella raccolta Azure AD applicazioni - Gennaio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nuove app federate disponibili nella raccolta App Azure AD - Gennaio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel gennaio 2020 sono state aggiunte queste 33 nuove app con supporto della federazione alla raccolta di app: 

[JOSA,](../saas-apps/josa-tutorial.md) [Fastly Edge Cloud,](../saas-apps/fastly-edge-cloud-tutorial.md) [Terraform Enterprise,](../saas-apps/terraform-enterprise-tutorial.md) [Spintr SSO,](../saas-apps/spintr-sso-tutorial.md) [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick,](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access) [Upshotly,](../saas-apps/upshotly-tutorial.md) [LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions,](../saas-apps/tripactions-tutorial.md) [SmartWork](https://www.intumit.com/teams-smartwork/), [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, e JDE,](../saas-apps/ssogen-tutorial.md) [Hosted MyCirqa SSO,](../saas-apps/hosted-mycirqa-sso-tutorial.md) [Yuhu Property Management Platform,](../saas-apps/yuhu-property-management-platform-tutorial.md) [LumApps,](https://sites.lumapps.com/login) [Upwork Enterprise,](../saas-apps/upwork-enterprise-tutorial.md) [Talentsoft,](../saas-apps/talentsoft-tutorial.md) [SmartDB per Microsoft Teams,](http://teams.smartdb.jp/login/) [PressPage,](../saas-apps/presspage-tutorial.md) [ContractSafe Saml2 SSO,](../saas-apps/contractsafe-saml2-sso-tutorial.md) [Maxient Conduct Manager Software,](../saas-apps/maxient-conduct-manager-software-tutorial.md) [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/)Squelch Cloud Office365 Connector, [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS,](../saas-apps/printerlogic-saas-tutorial.md) [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Assistente virtuale](https://akari.io/akari-virtual-assistant/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Due nuovi rilevamenti di Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Sono stati aggiunti due nuovi tipi di rilevamento collegato all'accesso a Identity Protection: Regole sospette di manipolazione della Posta in arrivo e Spostamento fisico impossibile. Questi rilevamenti offline vengono individuati da Microsoft Cloud App Security (MCAS) e influenzano l'utente e il rischio di accesso in Identity Protection. Per altre informazioni su questi rilevamenti, vedere i [tipi di rischio di accesso.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Modifica di rilievo: i frammenti URI non verranno trasportati tramite il reindirizzamento dell'account di accesso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire dall'8 febbraio 2020, quando viene inviata una richiesta a login.microsoftonline.com per accedere a un utente, il servizio aggiungerà un frammento vuoto alla richiesta.  In questo modo si impedisce a una classe di attacchi di reindirizzamento di assicurarsi che il browser esuli eventuali frammenti esistenti nella richiesta. Nessuna applicazione deve avere una dipendenza da questo comportamento. Per altre informazioni, vedere [Modifiche di rilievo](../develop/reference-breaking-changes.md#february-2020) nella documentazione di Microsoft Identity Platform.

---

## <a name="december-2019"></a>Dicembre 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrare il provisioning di SAP SuccessFactors Azure AD e in ACTIVE locale (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È ora possibile integrare SAP SuccessFactors come origine di identità autorevole in Azure AD. Questa integrazione consente di automatizzare il ciclo di vita delle identità end-to-end, incluso l'uso di eventi basati sulle risorse umane, ad esempio nuove assunzioni o terminazioni, per controllare il provisioning Azure AD account.

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors Azure AD, vedere l'esercitazione Configurare il provisioning automatico di [SAP SuccessFactors.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Supporto per messaggi di posta elettronica personalizzati in Azure AD B2C (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile usare Azure AD B2C per creare messaggi di posta elettronica personalizzati quando gli utenti si indosserno per usare le app. Usando DisplayControls (attualmente in anteprima) e un provider di posta elettronica di terze parti ( ad esempio [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)o un'API REST personalizzata), è possibile usare il proprio modello di posta **elettronica,** l'indirizzo mittente e il testo dell'oggetto, nonché supportare la localizzazione e le impostazioni personalizzate delle password una-volta (OTP).

Per altre informazioni, vedere [Verifica della posta elettronica personalizzata in Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Sostituzione dei criteri di base con le impostazioni predefinite per la sicurezza

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Nell'ambito di un modello di protezione per impostazione predefinita per l'autenticazione, vengono rimosso i criteri di protezione di base esistenti da tutti i tenant. Questa rimozione è destinata al completamento alla fine di febbraio. La sostituzione di questi criteri di protezione di base è l'impostazione predefinita per la sicurezza. Se si usano criteri di protezione di base, è necessario pianificare il passaggio ai nuovi criteri predefiniti per la sicurezza o all'accesso condizionale. Se questi criteri non sono stati usati, non è necessario eseguire alcuna azione.

Per altre informazioni sulle nuove impostazioni predefinite per la sicurezza, vedere [Che cosa sono le impostazioni predefinite per la sicurezza?](./concept-fundamentals-security-defaults.md) Per altre informazioni sui criteri di accesso condizionale, vedere [Criteri di accesso condizionale comuni.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>Novembre 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Supporto per l'attributo SameSite e Chrome 80

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Come parte di un modello di protezione per impostazione predefinita per i cookie, il browser Chrome 80 sta modificando il modo in cui tratta i cookie senza `SameSite` l'attributo . Tutti i cookie che non specificano l'attributo verranno trattati come se fosse impostato su , il che comporterà il blocco da parte di Chrome di determinati scenari di condivisione di cookie tra domini da cui l'app può `SameSite` `SameSite=Lax` dipendere. Per mantenere il comportamento precedente di Chrome, è possibile usare l'attributo e aggiungere un attributo aggiuntivo, in modo che i cookie tra siti siano accessibili solo `SameSite=None` `Secure` tramite connessioni HTTPS. Chrome è pianificato per completare questa modifica entro il 4 febbraio 2020.

È consigliabile che tutti gli sviluppatori testano le app usando queste linee guida:

- Impostare il valore predefinito per **l'impostazione Usa cookie protetti** su **Sì.**

- Impostare il valore predefinito per **l'attributo SameSite** **su None**.

- Aggiungere un attributo `SameSite` aggiuntivo di **Secure.**

Per altre informazioni, vedere [Upcoming SameSite Cookie Changes in ASP.NET and ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) and Potential disruption to customer websites and Microsoft products and services in Chrome versione [79](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)e successive.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nuovo hotfix per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Correzione  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un pacchetto di hotfix cumulativo (build 4.6.34.0) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup risolve i problemi e aggiunge i miglioramenti descritti nella sezione "Problemi risolti e miglioramenti aggiunti in questo aggiornamento".

Per altre informazioni e per scaricare il pacchetto hotfix, vedere [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Aggiornamento](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)cumulativo è disponibile .

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nuovo AD FS attività dell'app per facilitare la migrazione delle app Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Usare il nuovo report Active Directory Federation Services (AD FS) dell'app, nel portale di Azure, per identificare le app di cui è possibile eseguire la migrazione a Azure AD. Il report valuta tutte le app AD FS compatibilità con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione delle singole app per la migrazione.

Per altre informazioni, vedere [Usare il report attività AD FS'applicazione per eseguire la migrazione delle applicazioni Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nuovo flusso di lavoro per richiedere il consenso dell'amministratore (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Controllo di accesso

Il nuovo flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo per concedere l'accesso alle app che richiedono l'approvazione dell'amministratore. Se un utente prova ad accedere a un'app, ma non è in grado di fornire il consenso, può ora inviare una richiesta di approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica e inserita in una coda accessibile dal portale di Azure a tutti gli amministratori designati come revisori. Dopo che un revisore ha seguito una richiesta in sospeso, gli utenti richiedenti vengono informati dell'azione.

Per altre informazioni, vedere [Configurare il flusso di lavoro di consenso dell'amministratore (anteprima).](../manage-apps/configure-admin-consent-workflow.md)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nuova App Azure AD di configurazione del token di registrazione per la gestione delle attestazioni facoltative (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo

Il nuovo **pannello App Azure AD di configurazione token** di registrazione delle app nel portale di Azure ora mostra agli sviluppatori di app un elenco dinamico di attestazioni facoltative per le proprie app. Questa nuova esperienza consente di semplificare le migrazioni Azure AD app e di ridurre al minimo gli errori di configurazione delle attestazioni facoltative.

Per altre informazioni, vedere [Fornire attestazioni facoltative all'app Azure AD.](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nuovo flusso di lavoro di approvazione in due fasi nella Azure AD Entitlement Management (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione entitlement

È stato introdotto un nuovo flusso di lavoro di approvazione in due fasi che consente di richiedere a due responsabili approvazione di approvare la richiesta di un utente a un pacchetto di accesso. Ad esempio, è possibile impostarlo in modo che il responsabile dell'utente richiedente deve prima approvare e quindi è anche possibile richiedere l'approvazione di un proprietario della risorsa. Se uno dei responsabili approvazione non approva, l'accesso non viene concesso.

Per altre informazioni, vedere [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso in Azure AD entitlement.](../governance/entitlement-management-access-package-request-policy.md)

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aggiornamenti alla pagina App personali e alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono all'esperienza App personali aggiornata. Questa nuova esperienza include anche la nuova funzionalità aree di lavoro, che semplifica la ricerca e l'organizzazione delle app da parte degli utenti.

Per altre informazioni sulla nuova esperienza App personali e sulla creazione di aree di lavoro, vedere Creare aree di lavoro [App personali portale.](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Supporto di GOOGLE Social ID per Azure AD Collaborazione B2B (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** Autenticazione utente

Il nuovo supporto per l'uso di ID social google (account Gmail) in Azure AD semplifica la collaborazione per utenti e partner. Non è più necessario che i partner creino e gestiranno un nuovo account specifico di Microsoft. Microsoft Teams supporta ora completamente gli utenti di Google in tutti i client e tra gli endpoint di autenticazione comuni e correlati al tenant.

Per altre informazioni, vedere [Aggiungere Google come provider di identità per gli utenti guest B2B.](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge per dispositivi mobili per l'accesso condizionale e l'accesso Single Sign-On (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Azure AD per Microsoft Edge in iOS e Android supporta ora Azure AD single Sign-On e l'accesso condizionale:

- **Microsoft Edge Single Sign-On (SSO):** L'accesso Single Sign-On è ora disponibile nei client nativi (ad esempio Microsoft Outlook e Microsoft Edge) per tutte Azure AD app connesse.

- **Microsoft Edge'accesso condizionale:** Tramite i criteri di accesso condizionale basati sull'applicazione, gli utenti devono usare Microsoft Intune browser protetti, ad esempio Microsoft Edge.

Per altre informazioni sull'accesso condizionale e sull'accesso Single Sign-On con Microsoft Edge, vedere il post di blog Microsoft Edge [Mobile Support for Conditional Access and Single Sign-On Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) (Supporto di Microsoft Edge Mobile per l'accesso condizionale e l'accesso Single Sign-On ora disponibile a livello generale). Per altre informazioni su come configurare le app client usando l'accesso condizionale basato su [app](../conditional-access/app-based-conditional-access.md) o l'accesso condizionale basato su [dispositivo,](../conditional-access/require-managed-devices.md)vedere Gestire l'accesso Web tramite un [browser](/intune/apps/app-configuration-managed-browser)protetto da criteri di Microsoft Intune .

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD entitlement management (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione entitlement

Azure AD entitlement management è una nuova funzionalità di governance delle identità, che consente alle organizzazioni di gestire il ciclo di vita delle identità e degli accessi su larga scala. Questa nuova funzionalità consente di automatizzare i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le revisioni e la scadenza tra gruppi, app e siti di SharePoint Online.

Con Azure AD entitlement management, è possibile gestire in modo più efficiente l'accesso sia per i dipendenti che per gli utenti esterni all'organizzazione che necessitano dell'accesso a tali risorse.

Per altre informazioni, vedere [Informazioni sulla gestione Azure AD entitlement?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizza il provisioning degli account utente per queste app SaaS supportate di recente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti  

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

[SAP Cloud Platform Identity Authentication Service,](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md) [RingCentral,](../saas-apps/ringcentral-provisioning-tutorial.md) [SpaceIQ,](../saas-apps/spaceiq-provisioning-tutorial.md) [Miro,](../saas-apps/miro-provisioning-tutorial.md) [Cloudgate,](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md) [Infor CloudSuite,](../saas-apps/infor-cloudsuite-provisioning-tutorial.md) [OfficeSpace Software,](../saas-apps/officespace-software-provisioning-tutorial.md) [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nuove app federate disponibili nella raccolta App Azure AD - Novembre 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di novembre 2019 sono state aggiunte queste 21 nuove app con supporto della federazione alla raccolta di app:

[Airtable](../saas-apps/airtable-tutorial.md), [Hootsuite](../saas-apps/hootsuite-tutorial.md), [Blue Access for Members (BAM),](../saas-apps/blue-access-for-members-tutorial.md) [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), ResLife Portal , [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/) [](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Raccolta di applicazioni Azure AD e migliorata

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

La raccolta di applicazioni Azure AD è stata aggiornata per semplificare la ricerca di app pre-integrate che supportano il provisioning, il OpenID Connect e SAML nel tenant Azure Active Directory.

Per altre informazioni, vedere [Aggiungere un'applicazione al tenant Azure Active Directory .](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Aumento del limite di lunghezza della definizione del ruolo app da 120 a 240 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

I clienti hanno detto che il limite di lunghezza per il valore di definizione del ruolo app in alcune app e servizi è troppo breve a 120 caratteri. In risposta, la lunghezza massima della definizione del valore del ruolo è stata aumentata a 240 caratteri.

Per altre informazioni sull'uso di definizioni di ruolo specifiche dell'applicazione, vedere Aggiungere ruoli [dell'app](../develop/howto-add-app-roles-in-azure-ad-apps.md)nell'applicazione e riceverli nel token .

---

## <a name="october-2019"></a>Ottobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>API identityRiskEvent per i rilevamenti di rischi di Azure AD Identity Protection deprecata

**Tipo:** Modifica prevista **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

In risposta al feedback degli sviluppatori, i sottoscrittori di Azure AD Premium P2 possono ora eseguire query complesse sui dati di rilevamento di rischi di Azure AD Identity Protection usando la nuova API riskDetection per Microsoft Graph. La versione beta dell'API [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) esistente non restituirà più dati dal **10 gennaio 2020**. Se l'organizzazione usa l'API identityRiskEvent, è consigliabile passare alla nuova API di riskDetection.

Per altre informazioni sulla nuova API riskDetection, vedere la [documentazione di riferimento sull'API di rilevamento di rischi](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Supporto di Application Proxy per l'attributo SameSite e Chrome 80

**Tipo:** Modifica prevista **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

È prevista una modifica alla modalità di gestione dell'attributo **SameSite** tramite i cookie di Application Proxy due settimane prima che venga rilasciato il browser Chrome 80. Con il rilascio di Chrome 80, i cookie che non specificano l'attributo **SameSite** verranno gestiti come se fossero impostati su `SameSite=Lax`.

Per evitare potenziali conseguenze negative dovute a questa modifica, i cookie di accesso e di sessione di Application Proxy verranno aggiornati nel modo seguente:

- Impostando il valore predefinito di **Usa cookie protetti** su **Sì**.

- Impostando il valore predefinito dell'attributo **SameSite** su **Nessuno**.

    >[!NOTE]
    > I cookie di accesso di Application Proxy sono sempre stati trasmessi esclusivamente su canali protetti. Queste modifiche si applicano solo ai cookie di sessione.

Per altre informazioni sulle impostazioni dei cookie di Application Proxy, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Registrazioni app (legacy) e la gestione delle app nel portale di registrazione delle applicazioni (apps.dev.microsoft.com) non sono più disponibili

**Tipo:** Modifica prevista **Categoria di servizio:** N/D **Funzionalità del prodotto:** esperienza di sviluppo

Gli utenti con account Azure AD non possono più registrare o gestire le applicazioni usando il portale di registrazione delle applicazioni (apps.dev.microsoft.com) né registrare e gestire le applicazioni nell'esperienza Registrazioni app (legacy) nel portale di Azure.

Per altre informazioni sulla nuova esperienza Registrazioni app, vedere la [guida al training di Registrazioni app nel portale di Azure](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gli utenti non devono più ripetere la registrazione durante la migrazione da MFA per utente a MFA basata sull'accesso condizionale

**Tipo:** Fisso **Categoria di servizio:** MFA **Funzionalità del prodotto:** Protezione e sicurezza delle identità

È stato risolto un problema noto per cui gli utenti dovevano ripetere la registrazione se venivano disabilitati per Multi-Factor Authentication (MFA) per utente e quindi abilitati per MFA tramite un criterio di accesso condizionale.

Per richiedere agli utenti di ripetere la registrazione, è possibile selezionare l'opzione **Required re-register MFA** (Nuova registrazione MFA obbligatoria) tra i metodi di autenticazione dell'utente nel portale di Azure AD. Per altre informazioni sulla migrazione degli utenti da MFA per utente a MFA basata sull'accesso condizionale, vedere [Convertire gli utenti da MFA per singolo utente a MFA basata sull'accesso condizionale](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nuove funzionalità per trasformare e inviare attestazioni nel token SAML

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Sono state aggiunte altre funzionalità che consentono di personalizzare e inviare attestazioni nel token SAML. Queste nuove funzionalità includono:

- Funzioni aggiuntive per la trasformazione delle attestazioni, che consentono di modificare il valore inviato nell'attestazione.

- Possibilità di applicare più trasformazioni a una singola attestazione.

- Possibilità di specificare l'origine di un'attestazione in base al tipo di utente e al gruppo a cui l'utente appartiene.

Per informazioni dettagliate su queste nuove funzionalità, incluso come usarle, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nuova pagina Accessi personali per gli utenti finali in Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Monitoraggio e creazione report

È stata aggiunta una nuova pagina **Accessi personali** (https://mysignins.microsoft.com) per consentire agli utenti dell'organizzazione di visualizzare la cronologia degli accessi recenti e verificare la presenza di eventuali attività insolite. Questa nuova pagina consente agli utenti di verificare:

- Se qualcuno sta provando a indovinare la password.

- Se un malintenzionato è riuscito ad accedere all'account e da che posizione.

- A quali app l'utente malintenzionato ha provato ad accedere.

Per altre informazioni, vedere il blog [Users can now check their sign-in history for unusual activity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) (Gli utenti ora possono verificare la presenza di attività insolite nella cronologia degli accessi).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrazione di Azure AD Domain Services (Azure AD DS) dalle reti virtuali classiche a quelle di Azure Resource Manager

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

È disponibile un'interessante novità per i clienti che hanno sempre usato le reti virtuali classiche. È ora possibile eseguire una migrazione occasionale da una rete virtuale classica a una rete virtuale di Resource Manager esistente. Dopo il passaggio alla rete virtuale di Resource Manager, sarà possibile sfruttare funzionalità aggiuntive e aggiornate, ad esempio i criteri granulari per le password, le notifiche tramite posta elettronica e i log di controllo.

Per altre informazioni, vedere [Anteprima - Eseguire la migrazione di Azure AD Domain Services dal modello di rete virtuale classica a Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aggiornamenti al layout del contratto della pagina in Azure AD B2C

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

Sono state introdotte alcune nuove modifiche alla versione 1.2.0 del contratto della pagina per Azure AD B2C. In questa versione aggiornata è ora possibile controllare l'ordine di caricamento per gli elementi, che può anche essere utile per arrestare lo sfarfallio che si verifica quando viene caricato il foglio di stile (CSS).

Per un elenco completo delle modifiche apportate al contratto della pagina, vedere il [log delle modifiche delle versioni](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Eseguire l'aggiornamento alla pagina App personali insieme alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** Controllo di accesso

È ora possibile personalizzare la visualizzazione e l'accesso degli utenti dell'organizzazione alla nuova esperienza App personali, incluso l'uso della nuova funzionalità delle aree di lavoro per semplificare la ricerca delle app. La nuova funzionalità delle aree di lavoro funge da filtro per le app a cui gli utenti dell'organizzazione hanno già accesso.

Per altre informazioni sull'implementazione della nuova esperienza App personali e sulla creazione di aree di lavoro, vedere [Creare aree di lavoro nel portale App personali (anteprima)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Supporto per il modello di fatturazione basato sugli utenti attivi mensili (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

Azure AD B2C supporta ora la fatturazione degli utenti attivi mensili. La fatturazione degli utenti attivi mensili si basa sul numero di utenti univoci con attività di autenticazione durante un mese di calendario. I clienti esistenti possono passare a questo nuovo metodo di fatturazione in qualsiasi momento.

A partire dal 1° novembre 2019, le fatture per tutti i nuovi clienti verranno automaticamente emesse usando questo metodo. Questo metodo di fatturazione consente ai clienti di sfruttare i vantaggi in termini di costi e la possibilità di pianificare in anticipo.

Per altre informazioni, vedere [Eseguire l'aggiornamento al modello di fatturazione degli utenti attivi mensili](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD - Ottobre 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di ottobre 2019 sono state aggiunte 35 nuove app con il supporto della federazione alla raccolta di app:

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant), [OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Voce di menu Sicurezza consolidata nel portale di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile accedere a tutte le funzionalità di sicurezza di Azure AD disponibili dalla nuova voce di menu **Sicurezza** e dalla barra **Cerca** nel portale di Azure. Inoltre la nuova pagina di destinazione di **Sicurezza**, denominata **Security - Getting started** (Sicurezza - Introduzione), contiene collegamenti alla documentazione pubblica, indicazioni sulla sicurezza e guide alla distribuzione.

Il nuovo menu **Sicurezza** include:

- Accesso condizionale
- Identity Protection
- Centro sicurezza
- Identity Secure Score
- Metodi di autenticazione
- MFA
- Report sui rischi: Utenti a rischio, Accessi a rischio e Rilevamento dei rischi
- E altro ancora

Per altre informazioni, vedere [Sicurezza - Introduzione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Criteri di scadenza dei gruppi di Office 365 migliorati con il rinnovo automatico

**Tipo:** Funzionalità modificata **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

I criteri di scadenza dei gruppi di Office 365 sono stati migliorati con il rinnovo automatico dei gruppi usati attivamente dai relativi membri. I gruppi verranno rinnovati automaticamente in base all'attività dell'utente in tutte le app di Office 365, inclusi Outlook, SharePoint e Teams.

Quest miglioramento consente di ridurre le notifiche di scadenza dei gruppi e di garantire che i gruppi attivi continuino a essere disponibili. Se si ha già un criterio di scadenza attivo per i gruppi di Office 365, non è necessario eseguire alcuna operazione per attivare questa nuova funzionalità.

Per altre informazioni, vedere [Configurare i criteri di scadenza per i gruppi di Office 365](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Esperienza di creazione di Azure AD Domain Services (Azure AD DS) aggiornata

**Tipo:** Funzionalità modificata **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Azure AD Domain Services (Azure AD DS) è stato aggiornato per includere un'esperienza di creazione nuova e migliorata, che consente di creare un dominio gestito in soli tre clic. Inoltre è ora possibile caricare e distribuire Azure AD DS da un modello.

Per altre informazioni, vedere [Esercitazione: Creare e configurare un'istanza di Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Settembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Modifica prevista: Pacchetti di contenuti di Power BI deprecati

**Tipo:** Modifica prevista **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

A partire dal 1° ottobre 2019, saranno deprecati tutti i pacchetti di contenuto di Power BI, incluso il pacchetto di contenuto di Power BI per Azure AD. In alternativa a questo pacchetto di contenuto, è possibile usare Cartelle di lavoro di Azure AD per ottenere informazioni approfondite sui servizi correlati ad Azure AD. Saranno presto disponibili altre cartelle di lavoro, incluse le cartelle di lavoro sui criteri di accesso condizionale in modalità solo report, informazioni dettagliate basate sul consenso delle app e altro ancora.

Per altre informazioni sulle cartelle di lavoro, vedere [Come usare le cartelle di lavoro di Monitoraggio di Azure per i report Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Per altre informazioni sui pacchetti di contenuto deprecati, vedere il post di blog [Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) (Annuncio della disponibilità generale delle app modello di Power BI).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>L'opzione Profilo personale verrà rinominata e integrata con la pagina dell'account Microsoft Office

**Tipo:** Modifica prevista **Categoria di servizio:** Profilo personale/Account **Funzionalità del prodotto:** Collaborazione

A partire da ottobre, l'esperienza Profilo personale diventerà Account personale. Nell'ambito di tale modifica, ogni attuale occorrenza di **Profilo personale** diventerà **Account personale**. Oltre alla modifica del nome e ad alcuni miglioramenti alla progettazione, l'esperienza aggiornata offrirà un'integrazione aggiuntiva con la pagina dell'account Microsoft Office. In particolare, sarà possibile accedere alle installazioni e alle sottoscrizioni di Office dalla pagina **Panoramica account**, oltre che alle preferenze di contatto correlate a Office dalla pagina **Privacy**.

Per altre informazioni sull'esperienza Profilo personale (anteprima), vedere [Panoramica del portale Profilo personale (anteprima)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gestire in blocco i gruppi e i membri con i file CSV nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Microsoft è lieta di annunciare la disponibilità dell'anteprima pubblica delle esperienze di gestione dei gruppi in blocco nel portale di Azure AD. È ora possibile usare un file CSV e il portale di Azure AD per gestire i gruppi e gli elenchi di membri, inclusi:

- Aggiunta o rimozione di membri da un gruppo.

- Download dell'elenco di gruppi dalla directory.

- Download dell'elenco dei membri per un gruppo specifico.

Per altre informazioni, vedere [Aggiungere membri in blocco](../enterprise-users/groups-bulk-import-members.md), [Rimuovere membri in blocco](../enterprise-users/groups-bulk-remove-members.md), [Scaricare l'elenco dei membri in blocco](../enterprise-users/groups-bulk-download-members.md) e [Scaricare l'elenco dei gruppi in blocco](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Il consenso dinamico è ora supportato tramite un nuovo endpoint di consenso amministratore

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

È stato creato un nuovo endpoint di consenso amministratore per supportare il consenso dinamico, utile per le app che vogliono usare il modello di consenso dinamico in Microsoft Identity Platform.

Per altre informazioni su come usare questo nuovo endpoint, vedere [Uso dell'endpoint di consenso dell'amministratore](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD - Settembre 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di settembre 2019 sono state aggiunte 29 nuove app con il supporto della federazione alla raccolta di app:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office&trade; - Single sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/`, [ARC Facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Nuovo Ruolo con autorizzazioni di lettura globali di Azure AD

**Digitare:** Nuova funzionalità **Categoria di servizio: ruoli** Azure AD Funzionalità del **prodotto: Controllo** di accesso

A partire dal 24 settembre 2019, verrà avviata l'implementazione del nuovo ruolo di Azure Active Directory (AD) con autorizzazioni di lettura globali. Questa implementazione verrà avviata con i clienti di produzione e del cloud globale e verrà completata in tutto il mondo nel mese di ottobre.

Il ruolo con autorizzazioni di lettura globali è la controparte di sola lettura dell'amministratore globale. Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra servizi Microsoft 365, ma non possono eseguire azioni di gestione. Il ruolo con autorizzazioni di lettura globali è stato creato per ridurre il numero di amministratori globali nell'organizzazione. Poiché gli account amministratore globale sono potenti e vulnerabili agli attacchi, è consigliabile avere al massimo cinque amministratori globali. È consigliabile usare il ruolo con autorizzazioni di lettura globali per la pianificazione, i controlli o le indagini. È anche consigliabile usare il ruolo con autorizzazioni di lettura globali insieme ad altri ruoli di amministratore limitati, come l'amministratore di Exchange, per semplificare il lavoro senza richiedere il ruolo di amministratore globale.

Il ruolo con autorizzazioni di lettura globali funziona con la nuova interfaccia di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione di Teams, il Centro sicurezza, il Centro conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione di Gestione dispositivi.

>[!NOTE]
> All'inizio dell'anteprima pubblica, il ruolo con autorizzazioni di lettura globali non funzionerà con: SharePoint, Privileged Access Management, Customer Lockbox, etichette di riservatezza, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management e Teams App Catalog.

Per altre informazioni, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accesso all'istanza locale di Server di report dall'app Power BI per dispositivi mobili con Azure Active Directory Application Proxy

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

La nuova integrazione tra l'app Power BI per dispositivi mobili e Azure AD Application Proxy consente di accedere in modo sicuro all'app Power BI per dispositivi mobili e di visualizzare i report dell'organizzazione ospitati nel server di report di Power BI locale.

Per informazioni sull'app Power BI per dispositivi mobili, incluso da dove scaricare l'app, vedere il [sito di Power BI](https://powerbi.microsoft.com/mobile/). Per altre informazioni su come configurare l'app Power BI per dispositivi mobili con Azure AD Application Proxy, vedere [Abilitare l'accesso remoto a Power BI per dispositivi mobili con Azure AD Application Proxy](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>È disponibile la nuova versione del modulo AzureADPreview di PowerShell

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Directory

Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per definire e assegnare ruoli personalizzati in Azure AD, tra cui:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nuova versione di Azure AD Connect

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Directory

È stata rilasciata una versione aggiornata di Azure AD Connect per i clienti con aggiornamento automatico. Questa nuova versione include diverse nuove funzionalità, miglioramenti e correzioni di bug. Per altre informazioni su questa nuova versione, vedere [Azure AD Connect: Cronologia delle versioni](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>È ora disponibile il server Azure Multi-Factor Authentication (MFA), versione 8.0.2

**Tipo:** Fisso **Categoria di servizio:** MFA **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Se si è già clienti e il server MFA è stato attivato prima del 1° luglio 2019, è ora possibile scaricare la versione più recente del server MFA (versione 8.0.2). In questa nuova versione:

- È stato risolto un problema e ora, quando la sincronizzazione di Azure AD imposta come abilitato un utente disabilitato, viene inviato un messaggio di posta elettronica all'utente.

- È stato risolto un problema per consentire ai clienti di eseguire l'aggiornamento, continuando a usare la funzionalità Tag.

- È stato aggiunto il prefisso internazionale per il Kosovo (+383).

- È stata aggiunta la registrazione di controllo bypass monouso a MultiFactorAuthSvc.log.

- Sono state migliorate le prestazioni per Web Service SDK.

- Sono stati corretti bug secondari.

A partire dal 1° luglio 2019, Microsoft non offre più il server MFA per le nuove distribuzioni. I nuovi clienti che richiedono l'autenticazione a più fattori devono usare l'autenticazione Azure AD a più fattori basata sul cloud. Per altre informazioni, vedere [Planning a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md)(Pianificazione di una distribuzione di Multi-Factor Authentication basata sul cloud).

---

## <a name="august-2019"></a>Agosto 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Le funzionalità migliorate per ricerca, filtri e ordinamento per i gruppi sono disponibili nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Microsoft è lieta di annunciare la disponibilità dell'anteprima pubblica delle esperienze migliorate di gestione dei gruppi nel portale di Azure AD. Questi miglioramenti consentono di gestire in modo più efficiente i gruppi e gli elenchi di membri, grazie a:

- Funzionalità di ricerca avanzate, ad esempio la ricerca di substring negli elenchi di gruppi.
- Opzioni di filtro e ordinamento avanzate negli elenchi di membri e proprietari.
- Nuove funzionalità di ricerca per gli elenchi di membri e proprietari.
- Conteggi più accurati per i gruppi di grandi dimensioni.

Per altre informazioni, vedere [Gestire i gruppi nel portale di Azure](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Sono disponibili nuovi ruoli personalizzati per la gestione della registrazione di app (anteprima pubblica)

**Digitare:** Nuova funzionalità **Categoria di servizio: ruoli** Azure AD Funzionalità del **prodotto: Controllo** di accesso

I ruoli personalizzati (disponibili con una sottoscrizione di Azure AD P1 o P2) possono ora offrire l'accesso con granularità fine, consentendo di creare le definizioni dei ruoli con autorizzazioni specifiche e quindi di assegnare tali ruoli a risorse specifiche. È attualmente possibile creare ruoli personalizzati usando le autorizzazioni per la gestione delle registrazioni delle app e quindi per l'assegnazione del ruolo a un'app specifica. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli di amministratore personalizzati in Azure Active Directory (anteprima)](../roles/custom-overview.md).

Se sono necessarie altre autorizzazioni o risorse supportate, che attualmente non sono visualizzate, è possibile scrivere sul [sito del feedback di Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032). La richiesta verrà aggiunta alla roadmap degli aggiornamenti.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>I nuovi log di provisioning possono contribuire al monitoraggio e alla risoluzione dei problemi della distribuzione del provisioning di app (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

Sono disponibili nuovi log di provisioning che consentono di monitorare e risolvere i problemi relativi alla distribuzione del provisioning di utenti e gruppi. Questi nuovi file di log includono informazioni su:

- Gruppi correttamente creati in [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Quali ruoli sono stati importati da [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Dipendenti non importati da [Workday](../saas-apps/workday-inbound-tutorial.md)

Per altre informazioni, vedere [Report di provisioning nel portale di Azure Active Directory (anteprima)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nuovi report di sicurezza per tutti gli amministratori di Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per impostazione predefinita, tutti gli amministratori di Azure AD potranno a breve accedere a report di sicurezza moderni in Azure AD. Fino alla fine di settembre sarà possibile usare il banner nella parte superiore dei report di sicurezza moderni per tornare ai report precedenti.

I report di sicurezza moderni forniranno funzionalità aggiuntive rispetto alle versioni precedenti, tra cui:

- Filtro e ordinamento avanzati
- Azioni in blocco, ad esempio ignorare il rischio utente
- Conferma di entità compromesse o sicure
- Stato del rischio che include: A rischio, Ignorato, Con correzione e Confermato compromesso
- Nuovi rilevamenti correlati ai rischi (disponibili per i sottoscrittori di Azure AD Premium)

Per altre informazioni, vedere [Utenti a rischio](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [Accessi a rischio](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins) e [Rilevamento dei rischi](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L'identità gestita assegnata dall'utente è disponibile per le macchine virtuali e i set di scalabilità di macchine virtuali (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identità gestite per le risorse di Azure **Funzionalità del prodotto:** esperienza di sviluppo

Le identità gestite assegnate dall'utente sono ora disponibili a livello generale per le macchine virtuali e i set di scalabilità di macchine virtuali. Azure ora può quindi creare un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso e che può essere assegnato a una o più istanze del servizio di Azure. Per altre informazioni sulle identità gestite assegnate dall'utente, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gli utenti possono reimpostare le proprie password usando un'app per dispositivi mobili o un token hardware (disponibilità generale)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Gli utenti che hanno registrato un'app per dispositivi mobili con l'organizzazione possono ora reimpostare la password approvando una notifica dell'app Microsoft Authenticator o immettendo un codice dall'app per dispositivi mobili o dal token hardware.

Per altre informazioni, vedere [Funzionamento: Reimpostazione della password self-service di Azure AD](../authentication/concept-sspr-howitworks.md). Per altre informazioni sull'esperienza utente, vedere [Panoramica della reimpostazione della password per un account aziendale o dell'istituto di istruzione](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la cache condivisa di MSAL.NET per scenari On-Behalf-Of

**Tipo:** Fisso **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

A partire da Azure AD Authentication Library (ADAL.NET) versione di anteprima 5.0.0, gli sviluppatori di app devono [serializzare una cache per ogni account per le app Web e le API Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). In caso contrario, alcuni scenari che usano il flusso [on-behalf-of](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) per Java, insieme ad alcuni casi d'uso specifici di , possono comportare `UserAssertion` un'elevazione dei privilegi. Per evitare questa vulnerabilità, ADAL.NET ora ignora la cache condivisa di Microsoft Authentication Library per dotnet (MSAL.NET) per gli scenari on-behalf-of.

Per altre informazioni su questo problema, vedere [Vulnerabilità relativa all'elevazione dei privilegi di Azure Active Directory Authentication Library](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD - Agosto 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di agosto 2019 sono state aggiunte 26 nuove app con il supporto della federazione alla raccolta di app:

[Civic Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Attendance](../saas-apps/academy-attendance-tutorial.md), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliver.media&trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Sono disponibili nuove versioni dei moduli PowerShell AzureAD e AzureADPreview

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Directory

Sono disponibili nuovi aggiornamenti ai moduli di PowerShell AzureAD e AzureAD Preview:

- Un nuovo parametro `-Filter` è stato aggiunto al parametro `Get-AzureADDirectoryRole` nel modulo AzureAD. Questo parametro consente di applicare filtri ai ruoli della directory restituiti dal cmdlet.
- Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per definire e assegnare ruoli personalizzati in Azure AD, tra cui:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Miglioramenti all'interfaccia utente del generatore di regole del gruppo dinamico nel portale di Azure

**Tipo:** Funzionalità modificata **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Sono stati apportati alcuni miglioramenti all'interfaccia utente per il generatore di regole del gruppo dinamico, disponibile nel portale di Azure, per semplificare la configurazione di una nuova regola o la modifica delle regole esistenti. Questo miglioramento alla struttura consente di creare regole con un massimo di cinque espressioni, invece di una sola. È stato anche aggiornato l'elenco di proprietà dei dispositivi per rimuovere le proprietà deprecate dei dispositivi.

Per altre informazioni, vedere [Gestire le regole di appartenenza dinamica](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nuova autorizzazione dell'app Microsoft Graph disponibile per l'uso con le verifiche di accesso

**Tipo:** Funzionalità modificata **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Identity Governance

È stata introdotta una nuova autorizzazione dell'app Microsoft Graph, `AccessReview.ReadWrite.Membership`, che consente alle app di creare automaticamente e recuperare le verifiche di accesso per le appartenenze a gruppi e le assegnazioni di app. Questa autorizzazione può essere usata dai processi pianificati o come parte dell'automazione, senza richiedere il contesto di un utente connesso.

Per altre informazioni, vedere il [blog Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241) (Esempio di creazione di verifiche di accesso di Azure AD con autorizzazioni dell'app Microsoft Graph con PowerShell).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>I log attività di Azure AD sono ora disponibili per istanze del cloud per enti pubblici in Monitoraggio di Azure

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD sono ora disponibili per istanze del cloud per enti pubblici in Monitoraggio di Azure. È ora possibile inviare log di Azure AD all'account di archiviazione o a un hub eventi per l'integrazione con gli strumenti di informazioni di sicurezza e gestione degli eventi, come [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md) e [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Per altre informazioni sulla configurazione di Monitoraggio di Azure, vedere [Log attività di Azure AD in Monitoraggio di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aggiornare gli utenti alla nuova esperienza avanzata dell'informazione di sicurezza

**Tipo:** Funzionalità modificata **Categoria di servizio:**  Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Il 25 settembre 2019 verrà disattivata l'esperienza precedente e non ottimizzata dell'informazione di sicurezza per la registrazione e la gestione dell'informazione di sicurezza dell'utente e verrà attivata solo la nuova [versione ottimizzata](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Gli utenti non potranno quindi più usare l'esperienza precedente.

Per altre informazioni sull'esperienza dell'informazione di sicurezza avanzata, vedere la [documentazione per gli amministratori](../authentication/concept-registration-mfa-sspr-combined.md) e la [documentazione per gli utenti](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Per attivare questa nuova esperienza, è necessario:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.

2. Passare ad **Azure Active Directory > Impostazioni utente > Gestisci le impostazioni per le funzionalità in anteprima del pannello di accesso**.

3. Nell'area **Gli utenti possono usare le funzionalità in anteprima per la registrazione e la gestione delle informazioni di sicurezza - Avanzato** selezionare **Selezionato** e quindi scegliere un gruppo di utenti o scegliere **Tutti** per attivare questa funzionalità per tutti gli utenti nel tenant.

4. Nell'area **Gli utenti possono usare le funzionalità in anteprima per la registrazione e la gestione delle informazioni di **sicurezza**** selezionare **Nessuno**.

5. Salvare le impostazioni.

    Dopo il salvataggio delle impostazioni non sarà più possibile accedere all'esperienza precedente dell'informazione di sicurezza.

>[!Important]
>Se non si completa questa procedura prima del 25 settembre 2019, il tenant di Azure Active Directory verrà abilitato automaticamente per l'esperienza avanzata. In caso di domande, contattare Microsoft all'indirizzo registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Le richieste di autenticazione che usano accessi POST verranno convalidate in modo più rigoroso

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Standard

A partire dal 2 settembre 2019, le richieste di autenticazione che usano il metodo POST verranno convalidate in modo più rigoroso rispetto agli standard HTTP. In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori dei moduli di richiesta. Queste modifiche non provocheranno interruzioni nei client esistenti e contribuiranno ad assicurare che le richieste inviate ad Azure AD vengano gestite ogni volta in modo affidabile.

Per altre informazioni, vedere [Avvisi relativi a modifiche che causano un'interruzione per Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Luglio 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Modifica prevista: Aggiornamento del servizio Application Proxy per consentire solo il supporto di TLS 1.2

**Tipo:** Modifica prevista **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia più avanzata disponibile, l'accesso al servizio Application Proxy verrà limitato solo ai protocolli TLS 1.2. Questa limitazione verrà implementata inizialmente nei clienti che usano già i protocolli TLS 1.2, quindi non si verificherà alcun impatto. I protocolli TLS 1.0 e TLS 1.1 saranno definitivamente deprecati il 31 agosto 2019. I clienti che usano ancora TLS 1.0 e TLS 1.1 riceveranno preavvisi per la preparazione alla modifica.

Per mantenere la connessione al servizio Application Proxy durante questa modifica, è consigliabile assicurarsi che le combinazioni client-server e browser-server siano aggiornate per usare TLS 1.2. È anche consigliabile assicurarsi di includere eventuali sistemi client usati dai dipendenti per accedere alle app pubblicate tramite il servizio Application Proxy.

Per altre informazioni, vedere [Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Modifica prevista: Aggiornamenti alla struttura presto disponibili per la raccolta di applicazioni

**Tipo:** Modifica prevista **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Nuove modifiche all'interfaccia utente saranno presto disponibili per la struttura dell'area **Aggiungi dalla raccolta** del pannello **Aggiungi applicazione**. Queste modifiche contribuiranno a semplificare l'individuazione delle app che supportano il provisioning automatico, OpenID Connect, SAML (Security Assertion Markup Language) e Password Single Sign-On (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Modifica prevista: Rimozione dell'indirizzo IP del server MFA dall'indirizzo IP di Office 365

**Tipo:** Modifica prevista **Categoria di servizio:** MFA **Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'indirizzo IP del server MFA verrà rimosso dal [servizio Web per URL e indirizzi IP di Office 365](/office365/enterprise/office-365-ip-web-service). Se queste pagine vengono usate attualmente per aggiornare le impostazioni del firewall, è necessario assicurarsi di includere anche l'elenco di indirizzi IP documentati nella sezione **Requisiti del firewall del server Azure Multi-Factor Authentication** dell'articolo [Introduzione al server Azure Multi-Factor Authentication](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>I token solo per l'app richiedono ora la presenza dell'app client nel tenant della risorsa

**Tipo:** Fisso **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Il 26 luglio 2019 sono state apportate modifiche al modo in cui vengono forniti i token solo per l'app tramite la [concessione di credenziali client](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Le app potevano ottenere in precedenza token per chiamare altre app, indipendentemente dalla presenza o meno dell'app client nel tenant. Questo comportamento è stato aggiornato in modo che le risorse a singolo tenant, definite a volte API Web, possano essere chiamate solo da app client presenti nel tenant della risorsa.

Se l'app non si trova nel tenant della risorsa, verrà visualizzato il messaggio di errore `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` Per risolvere il problema, è necessario creare l'entità servizio dell'app client nel tenant usando l'[endpoint del consenso amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) o [PowerShell](../develop/howto-authenticate-service-principal-powershell.md), in modo da assicurarsi che il tenant abbia fornito all'app l'autorizzazione per eseguire operazioni nel tenant.

Per altre informazioni, vedere [Novità per l'autenticazione](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Il consenso esistente tra client e API continua a non essere obbligatorio. Le app devono ancora eseguire i rispettivi controlli dell'autorizzazione.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuovo accesso senza password ad Azure AD con le chiavi di sicurezza FIDO2

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

I clienti di Azure AD possono ora configurare criteri per gestire le chiavi di sicurezza FIDO2 per gli utenti e i gruppi dell'organizzazione. Gli utenti finali possono eseguire la registrazione automatica delle chiavi di sicurezza, usare le chiavi per accedere ai propri account Microsoft nei siti Web in dispositivi idonei per FIDO, oltre ad accedere ai propri dispositivi Windows 10 aggiunti ad Azure AD.

Per altre informazioni, vedere [Abilitare l'accesso senza password per Azure AD (anteprima)](../authentication/concept-authentication-passwordless.md) per informazioni correlate all'amministratore e [Configurare l'informazione di sicurezza per l'uso di una chiave di sicurezza (anteprima)](../user-help/security-info-setup-security-key.md) per informazioni correlate all'utente finale.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD - Luglio 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di luglio 2019 sono state aggiunte queste 18 nuove app con il supporto della federazione alla raccolta di app:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [Clever Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office&trade;](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizza il provisioning degli account utente per queste app SaaS supportate di recente

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nuovo tag del servizio Azure AD Domain Services per il gruppo di sicurezza di rete

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Se si vuole evitare la gestione di lunghi elenchi di indirizzi IP e intervalli di indirizzi IP, è possibile usare il nuovo tag **AzureActiveDirectoryDomainServices** del servizio di rete nel gruppo di sicurezza di rete di Azure per contribuire alla protezione del traffico in ingresso verso la subnet della rete virtuale di Azure AD Domain Services.

Per altre informazioni su questo nuovo tag del servizio, vedere [Gruppi di sicurezza di rete per Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Controllo di sicurezza di Azure AD Domain Service è ora disponibile per l'anteprima pubblica. Il Controllo di sicurezza contribuisce a fornire informazioni dettagliate essenziali nei servizi di autenticazione eseguendo lo streaming degli eventi di controllo verso le risorse specificate come destinazione, tra cui Archiviazione di Azure, aree di lavoro di Azure Log Analytics e Hub eventi di Azure, usando il portale di Azure AD Domain Service.

Per altre informazioni vedere [Abilitare i controlli di sicurezza di Azure AD Domain Services (anteprima)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nuovi report su utilizzo e informazioni dettagliate dei metodi di autenticazione (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Monitoraggio e creazione report

I nuovi report di informazioni dettagliate sull'utilizzo dei metodi di autenticazione consentono di comprendere in che modo funzionalità come Azure AD Multi-Factor Authentication e la reimpostazione della password & self-service vengono registrate e usate nell'organizzazione, incluso il numero di utenti registrati per ogni funzionalità, la frequenza con cui viene usata la reimpostazione della password self-service per reimpostare le password e con quale metodo viene eseguita la reimpostazione.

Per altre informazioni, vedere [Utilizzo e informazioni dettagliate dei metodi di autenticazione (anteprima)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Sono disponibili nuovi report di sicurezza per tutti gli amministratori di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Tutti gli amministratori di Azure AD possono ora selezionare il banner nella parte superiore dei report di sicurezza esistenti, ad esempio il report **Utenti contrassegnati per il rischio**, per iniziare a usare la nuova esperienza di sicurezza, come illustrato nei report **Utenti a rischio** e **Accessi a rischio**. Prossimamente tutti i report di sicurezza verranno spostati dalle versioni precedenti alle nuove versioni e i nuovi report offriranno le funzionalità aggiuntive seguenti:

- Filtro e ordinamento avanzati

- Azioni in blocco, ad esempio ignorare il rischio utente

- Conferma di entità compromesse o sicure

- Stato del rischio che include: A rischio, Ignorato, Con correzione e Confermato compromesso

Per altre informazioni, vedere [Report Utenti a rischio](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) e [Report Accessi a rischio](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Controllo di sicurezza di Azure AD Domain Service è ora disponibile per l'anteprima pubblica. Il Controllo di sicurezza contribuisce a fornire informazioni dettagliate essenziali nei servizi di autenticazione eseguendo lo streaming degli eventi di controllo verso le risorse specificate come destinazione, tra cui Archiviazione di Azure, aree di lavoro di Azure Log Analytics e Hub eventi di Azure, usando il portale di Azure AD Domain Service.

Per altre informazioni vedere [Abilitare i controlli di sicurezza di Azure AD Domain Services (anteprima)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nuova federazione diretta B2B con SAML/WS-Fed (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C

La federazione diretta semplifica la collaborazione con i partner la cui soluzione di identità gestita per IT non è Azure AD mediante sistemi di gestione dell'identità che supportano gli standard SAML o WS-Fed. Dopo la configurazione di una relazione di federazione diretta con un partner, eventuali nuovi utenti guest invitati dal dominio possono collaborare con l'utente usando il rispettivo account dell'organizzazione esistente, rendendo più semplice l'esperienza utente per gli utenti guest.

Per altre informazioni, vedere [Federazione diretta con Active Directory Federation Services (AD FS) e provider di terze parti per utenti guest (anteprima)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizza il provisioning degli account utente per queste app SaaS supportate di recente

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nuovo controllo per nomi di gruppo duplicati nel portale di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Quando si crea o si aggiorna un nome di gruppo dal portale di Azure AD, verrà ora eseguito un controllo per verificare se si sta duplicando un nome di gruppo esistente nella risorsa. Se viene determinato che il nome è già usato da un altro gruppo, verrà richiesto di modificare il nome.

Per altre informazioni, vedere [Gestire i gruppi nel portale di Azure AD](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD supporta ora parametri di query statici negli URI di risposta (reindirizzamento)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Le app Azure AD possono ora eseguire la registrazione e usare URI di risposta (reindirizzamento) con parametri di query statici, ad esempio `https://contoso.com/oauth2?idp=microsoft`, per le richieste OAuth 2.0. Il parametro di query statico è soggetto alla corrispondenza di stringhe per gli URI di risposta, analogamente a qualsiasi altre parte dell'URI di risposta. Se non è presente alcuna stringa registrata che corrisponde a "URL-decoded redirect-uri", la richiesta viene rifiutata. Se l'URI di risposta viene trovato, l'intera stringa viene usata per reindirizzare l'utente, incluso il parametro di query statico.

Gli URI di risposta dinamici non sono ancora consentiti perché rappresentano un rischio di sicurezza e non possono essere usati per conservare le informazioni sullo stato in una richiesta di autenticazione. Per questo scopo, usare il parametro `state`.

Le schermate di registrazione app del portale di Azure bloccano attualmente i parametri di query. È tuttavia possibile modificare manualmente il manifesto dell'app per aggiungere e testare parametri di query nell'app. Per altre informazioni, vedere [Novità per l'autenticazione](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Sono ora disponibili log attività (API MS Graph) per Azure AD tramite i cmdlet di PowerShell

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD (report su controllo e accesso) sono ora disponibili tramite il modulo PowerShell di Azure AD. In precedenza era possibile creare script personalizzati usando endpoint dell'API MS Graph e tale funzionalità è stata ora estesa ai cmdlet di PowerShell.

Per altre informazioni su come usare questi cmdlet, vedere [Cmdlet di Azure AD PowerShell per la creazione di report](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlli di filtro aggiornati per i log di controllo e accesso in Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

I report dei log di controllo e di accesso sono stati aggiornati ed è ora possibile applicare diversi filtri senza doverli aggiungere come colonne alle schermate dei report. È inoltre ora possibile decidere quanti filtri visualizzare sullo schermo. Questi aggiornamenti interagiscono tra loro per semplificare la lettura dei report e specificare ambiti più adatti alle esigenze dell'utente.

Per altre informazioni su questi aggiornamenti, vedere [Filtro dei log di controllo](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) e [Filtrare le attività di accesso](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Giugno 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nuova API riskDetections per Microsoft Graph (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora disponibile in anteprima pubblica la nuova API riskDetections per Microsoft Graph. È possibile usare questa nuova API per visualizzare un elenco dei rilevamenti dei rischi relativi a utenti e accessi correlati a Identity Protection per l'organizzazione. È anche possibile usare l'API per eseguire in modo più efficiente query sui rilevamenti dei rischi, inclusi dettagli su tipo, stato, livello di rilevamento e altro ancora.

Per altre informazioni, vedere la [documentazione di riferimento dell'API di rilevamento di rischi](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Giugno 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di giugno 2019 sono state aggiunte queste 22 nuove app con il supporto della federazione alla raccolta di app:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizza il provisioning degli account utente per queste app SaaS supportate di recente

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visualizzare lo stato in tempo reale del servizio di provisioning di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning Azure AD è stata aggiornata per includere una nuova barra di stato che mostra l'avanzamento del processo di provisioning utenti. L'esperienza aggiornata fornisce anche informazioni sul numero di utenti sottoposti a provisioning durante il ciclo corrente, oltre al numero di utenti sottoposti a provisioning fino a oggi.

Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Le informazioni personalizzate distintive dell'azienda vengono ora visualizzate nelle schermate di disconnessione e di errore

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Azure AD è stato aggiornato in modo da visualizzare le informazioni personalizzate distintive dell'azienda nelle schermate di disconnessione e di errore, oltre che nella pagina di accesso. Non è necessario eseguire alcuna operazione per attivare questa funzionalità. Azure AD usa semplicemente gli asset già configurati nell'area **Informazioni personalizzate distintive dell'azienda** del portale di Azure.

Per altre informazioni sulla configurazione delle informazioni personalizzate distintive dell'azienda, vedere [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine di Azure Active Directory dell'organizzazione](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Il server Azure Multi-Factor Authentication (MFA) non è più disponibile per le nuove distribuzioni

**Tipo:** Deprecato **Categoria di servizio:** MFA **Funzionalità del prodotto:** Protezione e sicurezza delle identità

A partire dal 1° luglio 2019, Microsoft non offrirà più il server MFA per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori nell'organizzazione devono ora usare l'autenticazione basata sul cloud Azure AD Multi-Factor Authentication. I clienti che hanno attivato il server MFA prima del 1° luglio non vedranno modifiche. Sarà comunque possibile scaricare la versione più recente, ottenere gli aggiornamenti futuri e generare le credenziali di attivazione.

Per altre informazioni, vedere [Introduzione al server Azure Multi-Factor Authentication](../authentication/howto-mfaserver-deploy.md). Per altre informazioni sull'Azure AD multi-factor authentication basata sul cloud, vedere Planning [a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: Supporto futuro solo per i protocolli TLS 1.2 nel servizio Application Proxy

**Tipo:** Modifica prevista **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Per fornire ai clienti la crittografia migliore della categoria, l'accesso verrà limitato ai soli protocolli TLS 1.2 nel servizio Application Proxy. Questa modifica viene implementata gradualmente per i clienti che usano già i protocolli TLS 1.2, quindi non verranno rilevati cambiamenti.

TLS 1.0 e TLS 1.1 saranno deprecati dal 31 agosto 2019, ma verrà inviata una notifica avanzata aggiuntiva, per consentire agli utenti di prepararsi per questa modifica. Per prepararsi per questa modifica, verificare che le combinazioni client-server e browser-server, inclusi i client usati dagli utenti per accedere alle app pubblicate tramite Application Proxy, siano aggiornate per usare il protocollo TLS 1.2 per mantenere la connessione al servizio Application Proxy. Per altre informazioni, vedere [Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy in Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report su utilizzo e informazioni dettagliate per visualizzare i dati di accesso relativi all'app

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile usare il report su utilizzo e informazioni dettagliate, disponibile nell'area **Applicazioni aziendali** del portale di Azure, per ottenere una visualizzazione dei dati di accesso basata sulle applicazioni, incluse le informazioni su:

- App più usate per l'organizzazione

- App con il maggior numero di accessi non riusciti

- Errori di accesso più frequenti per ogni app

Per altre informazioni su questa funzionalità, vedere [Report su utilizzo e informazioni dettagliate nel portale di Azure Active Directory](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning utenti nelle app cloud usando Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Monitoraggio e creazione report

Seguire queste nuove esercitazioni per usare il servizio di provisioning di Azure AD per automatizzare la creazione, l'eliminazione e l'aggiornamento degli account utente per le app basate sul cloud seguenti:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

È anche possibile seguire questa nuova [esercitazione su Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), che fornisce informazioni su come effettuare il provisioning di oggetti gruppo.

Per altre informazioni su come migliorare la sicurezza dell'organizzazione tramite il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Il punteggio di sicurezza delle identità è ora disponibile in Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** N/D **Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile monitorare e migliorare le condizioni di sicurezza delle identità usando la funzionalità di punteggio di sicurezza delle identità in Azure AD. La funzionalità di punteggio di sicurezza delle identità usa un unico dashboard che consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità, in base a un punteggio compreso tra 1 e 223.

- Pianificare i miglioramenti per la sicurezza delle identità

- Verificare il successo dei miglioramenti per la sicurezza

Per altre informazioni sulla funzionalità di punteggio di sicurezza delle identità, vedere [Informazioni sul punteggio di sicurezza delle identità in Azure Active Directory](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>È ora disponibile una nuova esperienza Registrazioni app (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** esperienza di sviluppo

La nuova esperienza [Registrazioni app](https://aka.ms/appregistrations) è ora disponibile a livello generale. Questa nuova esperienza include tutte le funzionalità chiave del portale di Azure e del portale di registrazione delle applicazioni con cui si ha familiarità e vi apporta miglioramenti tramite:

- **Migliore gestione delle app.** Invece di visualizzare le app in portali diversi, è ora possibile visualizzare tutte le app in un'unica posizione.

- **Registrazione delle app semplificata.** Grazie all'esperienza di navigazione migliorata e all'esperienza di selezione delle autorizzazioni rinnovata, ora è più semplice registrare e gestire le app.

- **Informazioni più dettagliate.** È possibile trovare più dettagli sull'app, incluse le guide di avvio rapido e altro ancora.

Per altre informazioni, vedere [Microsoft Identity Platform](../develop/index.yml) e l'annuncio del blog [App registrations experience is now generally available!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) (Esperienza Registrazioni app disponibile a livello generale).

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API Utenti a rischio per Identity Protection

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile usare l'API Utenti a rischio per recuperare la cronologia di rischio degli utenti, ignorare gli utenti a rischio e confermare la compromissione degli utenti. Questa modifica consente di aggiornare con maggiore efficienza lo stato di rischio degli utenti e di comprendere la rispettiva cronologia di rischio.

Per altre informazioni, vedere la [documentazione di riferimento dell'API Utenti a rischio](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Maggio 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di maggio 2019 sono state aggiunte queste 21 nuove app con il supporto della federazione alla raccolta di app:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), [Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Esperienze migliorate per la creazione e la gestione di gruppi nel portale di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Sono stati apportati miglioramenti alle esperienze correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di migliorare la gestione di elenchi di gruppi ed elenchi di membri e di fornire opzioni di creazione aggiuntive.

I miglioramenti includono:

- Funzionalità di base per i filtri per tipo di appartenenza e tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio Origine e Indirizzo di posta elettronica.

- Possibilità di selezione multipla per gruppi, membri ed elenchi di proprietari per semplificare l'eliminazione.

- Possibilità di scegliere un indirizzo di posta elettronica e di aggiungere proprietari durante la creazione di gruppi.

Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare un criterio di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica contribuisce all'applicazione di convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare criteri di denominazione per gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi di gruppo, ad esempio "CEO, Retribuzioni, Risorse umane".

Per altre informazioni, vedere [Applicare criteri di denominazione per i gruppi di Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Gli endpoint API Microsoft Graph sono ora disponibili per i log attività di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

Il supporto degli endpoint API Microsoft Graph per i log attività di Azure AD è disponibile a livello generale. Questa versione consente ora di usare la versione 1.0 dei log di controllo di Azure AD e delle API dei log di accesso.

Per altre informazioni, vedere [Panoramica delle API dei log di controllo di Azure AD](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora usare l'accesso condizionale per il processo di registrazione combinato (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Gli amministratori possono ora usare creare criteri di accesso condizionale che vengono usati dalla pagina di registrazione combinata. Questo approccio include l'applicazione di criteri per consentire la registrazione se:

- Gli utenti usano una rete attendibile.

- Gli utenti hanno un rischio di accesso basso.

- Gli utenti usano un dispositivo gestito.

- Gli utenti accettano le condizioni per l'utilizzo dell'organizzazione.

Per altre informazioni sui criteri di Accesso condizionale per il processo di registrazione combinato, vedere il [post di blog sull'Accesso condizionale per l'esperienza di registrazione combinata di Azure AD per MFA e reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Per altre informazioni sui criteri di Accesso condizionale per il processo di registrazione combinato, vedere [Criteri di Accesso condizionale per la registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Per altre informazioni sulla funzionalità Condizioni per l'utilizzo di Azure AD, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Il nuovo rilevamento di intelligence per le minacce di Azure AD è ora disponibile in Azure AD Identity Protection

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il rilevamento di intelligence per le minacce di Azure AD è ora disponibile nella funzionalità Azure AD Identity Protection aggiornata. Questa nuova funzionalità consente di indicare attività utente insolite per un utente specifico o coerenti con criteri di attacco noti in base all'intelligence per le minacce interno ed esterno di Microsoft.

Per altre informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere il blog [Four major Azure AD Identity Protection enhancements are now in public preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) (Quattro miglioramenti rilevanti per Azure AD Identity Protection sono ora disponibili in anteprima pubblica) e l'articolo [Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md) (versione aggiornata). Per altre informazioni sul rilevamento di intelligence per le minacce di Azure AD, vedere l'articolo [Rilevamenti di rischi di Azure Active Directory Identity Protection](../identity-protection/concept-identity-protection-risks.md).

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Gestione entitlement di Azure AD è ora disponibile (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Governance **Funzionalità del prodotto:** Identity Governance

La gestione degli entitlement di Azure AD, ora disponibile in anteprima pubblica, aiuta i clienti a delegare la gestione dei pacchetti di accesso, che definisce il modo in cui i dipendenti e i partner aziendali possono richiedere l'accesso, chi è responsabile per l'approvazione e la durata dell'accesso. I pacchetti di accesso possono gestire l'appartenenza ai gruppi di Azure AD e Office 365, le assegnazioni di ruolo nelle applicazioni aziendali e le assegnazioni di ruolo per i siti di SharePoint Online. Per altre informazioni sulla gestione degli entitlement, vedere la [panoramica della gestione degli entitlement di Azure AD](../governance/entitlement-management-overview.md). Per altre informazioni sulle numerose funzionalità disponibili per Azure AD Identity Governance, tra cui Privileged Identity Management, le verifiche di accesso e le condizioni per l'utilizzo, vedere [Informazioni su Azure AD Identity Governance](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare un criterio di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica contribuisce all'applicazione di convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare criteri di denominazione per gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi di gruppo, ad esempio "CEO, Retribuzioni, Risorse umane".

Per altre informazioni, vedere [Applicare criteri di denominazione per i gruppi di Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività di Azure AD ora sono disponibili in Monitoraggio di Azure (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

Per contribuire alla risposta al feedback degli utenti sulle visualizzazioni con i log attività di Azure AD, in Log Analytics è ora disponibile la nuova funzionalità Informazioni dettagliate. Questa funzionalità consente di ottenere informazioni dettagliate sulle risorse di Azure AD usando modelli interattivi, definiti cartelle di lavoro. Queste cartelle di lavoro predefinite possono fornire dettagli ad app o utenti e includono le informazioni seguenti:

- **Accessi.** Fornisce dettagli per app e utenti, tra cui la posizione di accesso, il sistema operativo in uso o il client e la versione del browser e il numero di accessi riusciti o non riusciti.

- **Autenticazione e accesso condizionale legacy.** Fornisce dettagli per app e utenti che usano l'autenticazione legacy, tra cui l'utilizzo di Multi-Factor Authentication attivato da criteri di accesso condizionale, le app che usano i criteri di accesso condizionale e così via.

- **Analisi degli errori di accesso.** Consente di determinare se gli errori di accesso si verificano a causa di un'azione di un utente, problemi dei criteri o infrastruttura.

- **Report personalizzati.** È possibile creare nuove cartelle di lavoro o modificare cartelle di lavoro esistenti per contribuire alla personalizzazione della funzionalità Informazioni dettagliate per l'organizzazione.

Per altre informazioni, vedere [Come usare le cartelle di lavoro di Monitoraggio di Azure per i report Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Aprile 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di aprile 2019 sono state aggiunte queste 21 nuove app con il supporto della federazione alla raccolta di app:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [Benchling](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (Role-based SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nuova opzione per la frequenza delle verifiche di accesso e selezione di più ruoli

**Tipo:** Nuova funzionalità **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Identity Governance

I nuovi aggiornamenti per le verifiche di accesso di Azure AD consentono di:

- Modificare la frequenza delle verifiche di accesso impostandola su **Semestrale**, in aggiunta alle opzioni esistenti in precedenza, ovvero settimanale, mensile, trimestrale e annuale.

- Selezionare più ruoli delle risorse di Azure AD e Azure durante la creazione di una singola verifica di accesso. In questa situazione tutti i ruoli vengono configurati con le stesse impostazioni e tutti i revisori ricevono una notifica nello stesso momento.

Per altre informazioni su come creare una verifica di accesso, vedere [Creare una verifica di accesso di gruppi o applicazioni nelle verifiche di accesso di Azure AD](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Verrà eseguita la transizione dei sistemi di avviso tramite posta elettronica di Azure AD Connect e verranno quindi inviate informazioni sul nuovo mittente per alcuni clienti

**Tipo:** Funzionalità modificata **Categoria di servizio:** AD Sync **Funzionalità del prodotto:** Piattaforma

Azure AD Connect sta eseguendo la transizione dei sistemi di avvisi tramite posta elettronica, mostrando potenzialmente ad alcuni clienti un nuovo mittente di messaggi di posta elettronica. Per risolvere questo problema, è necessario aggiungere all'elenco elementi consentiti dell'organizzazione oppure non sarà possibile continuare a ricevere avvisi importanti dai servizi di sincronizzazione di `azure-noreply@microsoft.com` Office 365, Azure o.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>È ora possibile eseguire correttamente modifiche al suffisso UPN tra domini federati in Azure AD Connect

**Tipo:** Fisso **Categoria di servizio:** AD Sync **Funzionalità del prodotto:** Piattaforma

È ora possibile modificare il suffisso UPN di un utente da un dominio federato a un altro dominio federato in Azure AD Connect. Grazie a questa correzione, non dovrebbe essere più visualizzato il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o non si dovrebbe più ricevere la notifica tramite posta elettronica con testo "Non è possibile aggiornare l'oggetto in Azure Active Directory, perché l'attributo [FederatedUser.UserPrincipalName] non è valido. Aggiornare il valore nei servizi directory locali".

Per altre informazioni, vedere [Risoluzione dei problemi durante la sincronizzazione](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Miglioramento della sicurezza con i criteri di Accesso condizionale basati sulla protezione delle app in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'Accesso condizionale basato sulla protezione dell'app è ora disponibile mediante il criterio **Richiedi la protezione dell'app**. Questo nuovo criterio contribuisce al miglioramento della sicurezza dell'organizzazione perché aiuta a prevenire le situazioni seguenti:

- Utenti che ottengono l'accesso alle app senza una licenza di Microsoft Intune.

- Utenti che riescono a ottenere un criterio di protezione app di Microsoft Intune.

- Utenti che ottengono l'accesso alle app senza un criterio di protezione app configurato di Microsoft Intune.

Per altre informazioni, vedere [Come richiedere criteri di protezione dell'app per l'accesso alle app cloud con l'Accesso condizionale](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per l'accesso Single Sign-On e l'Accesso condizionale di Azure AD in Microsoft Edge (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Sono stati apportati miglioramenti al supporto di Azure AD per Microsoft Edge, inclusa la disponibilità del nuovo supporto per l'accesso Single Sign-On e l'Accesso condizionale di Azure AD. Se è stato usato in precedenza Microsoft Intune Managed Browser, è ora possibile usare Microsoft Edge.

Per altre informazioni sulla configurazione e sulla gestione dei dispositivi e delle app con l'Accesso condizionale, vedere [Richiedere dispositivi gestiti per l'accesso alle app cloud con l'Accesso condizionale](../conditional-access/require-managed-devices.md) e [Richiedere app client approvate per l'accesso alle app cloud con l'Accesso condizionale](../conditional-access/app-based-conditional-access.md). Per altre informazioni su come gestire l'accesso usando Microsoft Edge con i criteri di Microsoft Intune, vedere [Gestire l'accesso Web usando un browser protetto con criteri di Microsoft Intune](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>È ora disponibile il supporto per Identity Experience Framework e criteri personalizzati in Azure Active Directory B2C (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È ora possibile creare criteri personalizzati in Azure AD B2C, anche per le attività seguenti, che sono supportate su larga scala e in base al contratto di servizio di Azure:

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare ed eseguire il mapping delle attestazioni per l'uso in decisioni e comunicazioni in tempo reale.

- Usare servizi abilitati per API REST nei percorsi utente di autenticazione personalizzata, ad esempio con provider di posta elettronica, CRM e sistemi di autorizzazione proprietari.

- Eseguire la federazione con i provider di identità conformi al protocollo OpenIDConnect, ad esempio con provider di Azure AD multi-tenant, provider di account di social network o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere [Note per gli sviluppatori sui criteri personalizzati in Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) e leggere il [post di Blog di Alex Simon, inclusi i case study](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Marzo 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di marzo 2019 sono state aggiunte queste 14 nuove app con il supporto della federazione alla raccolta di app:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovi connettori per il provisioning di Zscaler e Atlassian nella raccolta di Azure AD - Marzo 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Integrazione con app di terze parti

Automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per le app seguenti:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione tramite il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di individuare i gruppi disponibili per il ripristino, oltre a permettere di eliminare in modo permanente i gruppi superflui per l'organizzazione.

Per altre informazioni, vedere [Ripristinare i gruppi scaduti o eliminati](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>L'accesso Single Sign-On è ora disponibile per le app locali con protezione SAML di Azure AD tramite Application Proxy (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

È ora possibile offrire un'esperienza di accesso Single Sign-On (SSO) per app locali con autenticazione SAML, oltre a offrire l'accesso remoto a tali app tramite Application Proxy. Per altre informazioni su come configurare l'accesso Single Sign-On SAML con le app locali, vedere [Accesso Single Sign-On SAML per applicazioni locali con Application Proxy (anteprima)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client nei cicli di richieste verranno interrotte per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Le app client possono generare erroneamente centinaia di richieste di accesso uguali in un breve periodo di tempo. Indipendentemente dall'esito positivo o negativo, queste richieste contribuiscono a creare un'esperienza utente di scarsa qualità e incrementano i carichi di lavoro per l'IdP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IdP.

Questo aggiornamento invia l'errore di tipo `invalid_grant` seguente: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` alle app client che generano richieste duplicate più volte in un breve periodo di tempo, oltre all'ambito dell'operatività normale. Nelle app client in cui si verifica tale problema dovrebbe essere visualizzato un prompt interattivo che richiede all'utente di ripetere l'accesso. Per altre informazioni su questa modifica e su come risolvere l'errore se viene rilevato nell'app, vedere [Novità per l'autenticazione](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nuova esperienza utente ora disponibile per i log di controllo

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

È stata creata una nuova pagina **Log di controllo** di Azure AD per contribuire al miglioramento della leggibilità e della modalità di ricerca delle informazioni. Per visualizzare la nuova pagina **Log di controllo**, selezionare **Log di controllo** nella sezione **Attività** di Azure AD.

![Nuova pagina Log di controllo con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per altre informazioni sulla nuova pagina **Log di controllo**, vedere [Report delle attività di controllo nel portale di Azure Active Directory](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per contribuire alla prevenzione del blocco accidentale degli amministratori a causa di criteri di accesso condizionale con configurazione errata

**Tipo:** Funzionalità modificata **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per aiutare gli amministratori a evitare di rimanere esclusi dai rispettivi tenant a causa di criteri di accesso condizionale con configurazione errata, sono stati creati nuovi avvisi e sono state aggiornate le indicazioni nel portale di Azure. Per altre informazioni sulle nuove indicazioni, vedere [Informazioni sulle dipendenze del servizio nell'accesso condizionale di Azure Active Directory](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Miglioramento delle esperienze relative alle condizioni per l'utilizzo per gli utenti finali nei dispositivi mobili

**Tipo:** Funzionalità modificata **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Le esperienze esistenti per le condizioni per l'utilizzo sono state aggiornate per contribuire al miglioramento della modalità di verifica e di concessione del consenso per le condizioni per l'utilizzo in un dispositivo mobile. È ora possibile aumentare e ridurre la visualizzazione, tornare indietro, scaricare le informazioni e selezionare i collegamenti ipertestuali. Per altre informazioni sulle condizioni per l'utilizzo aggiornate, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuova esperienza disponibile per il download dei log attività di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile scaricare quantità elevate di log attività direttamente dal portale di Azure. Questo aggiornamento consente di:

- Scaricare fino a 250.000 righe.

- Ottenere una notifica al completamento del download.

- Personalizzare il nome del file.

- Determinare il formato di output, scegliendo JSON o CSV.

Per altre informazioni su questa funzionalità, vedere [Avvio rapido: Scaricare il report di controllo con il portale di Azure](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica che causa un'interruzione: aggiornamenti alla valutazione delle condizioni da parte di Exchange ActiveSync (EAS)

**Tipo:** Modifica prevista **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Controllo di accesso

Verrà aggiornata la modalità con cui Exchange ActiveSync (EAS) valuta le condizioni seguenti:

- Posizione dell'utente, in base a paese, area geografica o indirizzo IP

- Rischio di accesso

- Piattaforma del dispositivo.

Se queste condizioni sono state usate in precedenza nei criteri di accesso condizionale, occorre notare che il comportamento della condizione potrebbe subire modifiche. Se ad esempio è stata usata in precedenza la condizione relativa alla posizione dell'utente in un criterio, è possibile che il criterio venga ora ignorato in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Crittografia dei token SAML di Azure AD configurabile (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

È ora possibile configurare qualsiasi app SAML supportata per la ricezione di token SAML crittografati. In caso di configurazione e uso con un'app, Azure AD crittografa le asserzioni SAML rilasciate con una chiave pubblica ottenuta da un certificato archiviato in Azure AD.

Per altre informazioni sulla configurazione della crittografia del token SAML, vedere [Configurare la crittografia dei token SAML di Azure AD](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creare una verifica di accesso per gruppi o app con le verifiche di accesso di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Governance

È ora possibile includere più gruppi o app in una singola verifica di accesso di Azure AD per un'appartenenza a gruppi o un'assegnazione di app. Le verifiche di accesso con più gruppi o app sono configurate con le stesse impostazioni e tutti i revisori inclusi ricevono una notifica nello stesso momento.

Per altre informazioni su come creare una verifica di accesso usando le verifiche di accesso di Azure AD, vedere [Creare una verifica di accesso di gruppi o applicazioni nelle verifiche di accesso di Azure AD](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Febbraio 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di febbraio 2019, alla raccolta di app sono state aggiunte queste 27 nuove app con il supporto della federazione:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [Seismic](../saas-apps/seismic-tutorial.md), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Miglioramento della registrazione combinata per MFA/Reimpostazione della password self-service

**Tipo:** Funzionalità modificata **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

In seguito al feedback dei clienti, l'esperienza di anteprima della registrazione combinata per MFA/Reimpostazione della password self-service è stata migliorata, per consentire agli utenti di accelerare la registrazione dell'informazione di sicurezza per MFA e Reimpostazione della password self-service.

**Per attivare subito l'esperienza avanzata per gli utenti, seguire questa procedura:**

1. Accedere come amministratore globale o amministratore utenti al portale di Azure e passare ad **Azure Active Directory > Impostazioni utente > Gestione delle impostazioni per le funzionalità di anteprima del pannello di accesso**.

2. Nell'opzione **Utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione dell'informazione di sicurezza - Aggiorna** scegliere di attivare le funzionalità per un **Gruppo selezionato di utenti** o per **Tutti gli utenti**.

Nelle prossime settimane verrà rimossa la possibilità di attivare l'esperienza di anteprima di registrazione combinata per MFA/Reimpostazione della password self-service per i tenant che non hanno già attivato tale esperienza.

**Per verificare se il controllo verrà rimosso per il tenant specifico, seguire questa procedura:**

1. Accedere come amministratore globale o amministratore utenti al portale di Azure e passare ad **Azure Active Directory > Impostazioni utente > Gestione delle impostazioni per le funzionalità di anteprima del pannello di accesso**.

2. Se l'opzione **Utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione dell'informazione di sicurezza** è impostata su **Nessuno**, l'opzione verrà rimossa dal tenant.

Indipendentemente dal fatto che l'esperienza di anteprima di registrazione combinata per MFA/Reimpostazione della password self-service sia stata attivata o meno in precedenza per gli utenti, l'esperienza precedente verrà disattivata in futuro. È quindi consigliabile passare appena possibile alla nuova esperienza avanzata.

Per altre informazioni sull'esperienza di registrazione avanzata, vedere [Cool enhancements to the Azure AD combined MFA and password reset registration experience](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) (Miglioramenti all'esperienza di registrazione combinata per MFA e Reimpostazione della password di Azure AD).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aggiornamento dell'esperienza di gestione dei criteri per i flussi utente

**Tipo:** Funzionalità modificata **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

Il processo di creazione e gestione dei criteri per i flussi utenti, noti in precedenza come criteri predefiniti, è stato aggiornato e semplificato. Questa nuova esperienza è ora predefinita per tutti i tenant di Azure AD.

È possibile fornire commenti e suggerimenti aggiuntivi usando le icone smile o faccia imbronciata nell'area **Invia commenti e suggerimenti** nella parte superiore della schermata del portale.

Per altre informazioni sulla nuova esperienza di gestione dei criteri, vedere il blog relativo alla [disponibilità in Azure AD B2C della personalizzazione per JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scegliere versioni specifiche degli elementi della pagina fornite da Azure AD B2C

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica degli elementi di pagina forniti da Azure AD B2C. Se si seleziona una versione specifica, è possibile testare gli aggiornamenti prima che vengano visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. È anche possibile acconsentire esplicitamente all'imposizione di versioni specifiche della pagina per consentire le personalizzazioni per JavaScript. Per attivare questa funzionalità, passare alla pagina **Proprietà** nei flussi utente.

Per altre informazioni sulla scelta di versioni specifiche degli elementi di pagina, vedere il blog relativo alla [disponibilità in Azure AD B2C della personalizzazione per JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti configurabili per le password degli utenti finali per B2C (disponibilità generale)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È ora possibile configurare la complessità delle password dell'organizzazione per gli utenti finali, invece di usare i criteri password nativi di Azure AD. Dal pannello **Proprietà** dei flussi utenti, definiti in precedenza criteri predefiniti, è possibile scegliere una complessità di tipo **Semplice** o **Alta** per la password oppure è possibile creare un set di requisiti **Personalizzato**.

Per altre informazioni sulla configurazione dei requisiti per la complessità delle password, vedere [Configurare i requisiti di complessità delle password in Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per esperienze di autenticazione personalizzate

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È possibile usare i nuovi modelli predefiniti, disponibili nel pannello **Layout di pagina** dei flussi utente, definiti in precedenza criteri personalizzati, per creare un'esperienza di autenticazione personalizzata per gli utenti.

Per altre informazioni sull'uso dei modelli, vedere il blog relativo alla [disponibilità in Azure AD B2C della personalizzazione per JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Gennaio 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Collaborazione B2B di Active Directory tramite l'autenticazione con passcode monouso (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C

È stata introdotta l'autenticazione con passcode monouso (OTP) per autenticare gli utenti guest B2B che non possono essere autenticati tramite altri mezzi quali Azure AD, un account Microsoft (account del servizio gestito) o la federazione di Google. Con questo nuovo metodo di autenticazione, gli utenti guest non devono creare un nuovo account Microsoft. Durante il riscatto di un invito o l'accesso a una risorsa condivisa, un utente guest può invece richiedere l'invio di un codice temporaneo a un indirizzo di posta elettronica. Usando questo codice temporaneo, l'utente guest può continuare ad accedere.

Per altre informazioni, vedere [Autenticazione con passcode monouso tramite indirizzo di posta elettronica (anteprima)](../external-identities/one-time-passcode.md) e il blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD semplifica le condivisioni e le collaborazioni per qualsiasi utente con qualsiasi account).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nuove impostazioni dei cookie di Azure AD Application Proxy

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Sono state introdotte tre nuove impostazioni dei cookie, disponibili per le app pubblicate tramite il proxy di applicazione:

- **Usa un cookie di tipo solo HTTP.** Consente di impostare il flag **HTTPOnly** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, tra cui la possibilità di evitare la copia o la modifica dei cookie tramite script lato client. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie sicuro.** Consente di impostare il flag **Secure** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, assicurando che i cookie vengano trasmessi solo su canali TLS sicuri, ad esempio HTTPS. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie permanente.** Consente di impedire la scadenza dei cookie di accesso alla chiusura del Web browser. Questi cookie rimangono validi per l'intera durata del token di accesso. I cookie vengono tuttavia reimpostati se viene raggiunta la scadenza o se l'utente elimina manualmente il cookie. È consigliabile mantenere l'impostazione predefinita **No**, attivando l'impostazione solo per app meno recenti che non condividono cookie tra i processi.

Per altre informazioni sui nuovi cookie, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2019

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di gennaio 2019, alla raccolta di app sono state aggiunte queste 35 nuove app con il supporto della federazione:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nuovi miglioramenti ad Azure AD Identity Protection (anteprima pubblica)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

All'offerta di anteprima pubblica di Azure AD Identity Protection sono stati aggiunti i miglioramenti seguenti:

- Interfaccia utente aggiornata e più integrata

- API aggiuntive

- Valutazione migliorata dei rischi tramite Machine Learning

- Allineamento a livello di prodotto per utenti e accessi rischiosi

Per altre informazioni sui miglioramenti, vedere [Informazioni su Azure Active Directory Identity Protection (versione aggiornata)](../identity-protection/overview-identity-protection.md). È possibile ottenere informazioni aggiuntive e condividere le proprie opinioni tramite i prompt nel prodotto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nuova funzionalità di blocco dell'app per l'app Microsoft Authenticator in dispositivi iOS e Android

**Tipo:** Nuova funzionalità **Categoria di servizio:** App Microsoft Authenticator **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per rendere più sicuri i passcode monouso, le informazioni sull'app e le impostazioni dell'app, è possibile attivare la funzionalità di blocco dell'app nell'app Microsoft Authenticator. Attivando il blocco dell'app verrà chiesto di eseguire l'autenticazione usando il PIN o la biometria ogni volta che si apre l'app Microsoft Authenticator.

Per altre informazioni, vedere [Domande frequenti sull'app Microsoft Authenticator](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Miglioramenti alle funzionalità di esportazione di Azure AD Privileged Identity Management (PIM)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Gli amministratori di Privileged Identity Management (PIM) possono ora esportare tutte le assegnazioni di ruolo attive e idonee per una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica.

Per altre informazioni, vedere [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Novembre/Dicembre 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gli utenti non rimossi dall'ambito di sincronizzazione non passano più ad account di tipo solo cloud

**Tipo:** Fisso **Categoria di servizio:** Gestione utenti **Funzionalità del prodotto:** Directory

>[!Important]
>Microsoft ha ascoltato e compreso i commenti di insoddisfazione dei clienti in merito a questa correzione. Ha quindi ripristinato lo stato antecedente alla modifica finché non sarà più facile per i clienti implementare questa correzione nella loro organizzazione.

È stato risolto un bug a causa del quale il flag DirSyncEnabled di un utente viene erroneamente impostato su **False** quando l'oggetto Active Directory Domain Services viene escluso dall'ambito di sincronizzazione e quindi spostato nel Cestino in Azure AD durante il ciclo di sincronizzazione successivo. In seguito a questa correzione, se l'utente viene escluso dall'ambito di sincronizzazione e successivamente ripristinato dal Cestino di Azure AD, l'account utente resta sincronizzato da AD locale come previsto e non può essere gestito nel cloud poiché la relativa origine di autorità rimane AD locale.

Prima di questa correzione si verificava un problema quando il flag DirSyncEnabled veniva impostato su False. Veniva data l'erronea l'impressione che questi account fossero stati convertiti in oggetti solo cloud e potessero essere gestiti solo nel cloud. Tuttavia, gli account mantenevano l'origine di autorità come locale e tutte le proprietà sincronizzate (attributi shadow) provenienti da AD locale. Questa condizione causava vari problemi in Azure AD e in altri carichi di lavoro cloud, ad esempio Exchange Online, che prevedevano di gestire questi account come sincronizzati da AD, ma che si comportavano ora come account solo cloud.

Attualmente, l'unico modo per convertire realmente un account sincronizzato da AD in un account solo cloud è disabilitare DirSync a livello di tenant, attivando così un'operazione di back-end per trasferire l'origine di autorità. Questo tipo di modifica dell'origine di autorità richiede, tra l'altro, la pulizia di tutti gli attributi locali correlati (ad esempio LastDirSyncTime e gli attributi shadow) e l'invio di un segnale ad altri carichi di lavoro cloud perché convertano anch'essi il rispettivo oggetto in un account solo cloud.

Questa correzione, di conseguenza, consente di evitare gli aggiornamenti diretti dell'attributo ImmutableID di un utente sincronizzato da AD, che in precedenza erano necessari in alcuni scenari. Per impostazione predefinita, l'attributo ImmutableID di un oggetto in Azure AD, come suggerisce il nome, deve essere immutabile. Per questi scenari sono disponibili nuove funzionalità implementate in Azure AD Connect Health e nel client di sincronizzazione di Azure AD Connect:

- **Aggiornamento di ImmutableID su larga scala per molti utenti in un approccio a fasi**

  Ad esempio, è necessario eseguire una lunga migrazione tra foreste di Active Directory Domain Services. Soluzione: Usare Azure AD Connect per **Configurare l'ancoraggio di origine** e, quando viene completata la migrazione dell'utente, copiare i valori di ImmutableID esistenti da Azure AD nell'attributo ms-DS-Consistency-Guid dell'utente di Active Directory Domain Services locale della nuova foresta. Per altre informazioni, vedere [Uso di ms-DS-ConsistencyGuid come sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aggiornamenti di ImmutableID su larga scala per molti utenti in un unico passaggio**

  Ad esempio, se durante l'implementazione di Azure AD Connect si commette un errore ed è necessario modificare l'attributo SourceAnchor. Soluzione: Disabilitare DirSync a livello di tenant e cancellare tutti i valori di ImmutableID non validi. Per altre informazioni, vedere [Disabilitare la sincronizzazione della directory per Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Abbinare di nuovo un utente locale e un utente esistente in Azure AD** Ad esempio, un utente che è stato ricreato in Active Directory Domain Services genera un duplicato nell'account Azure AD anziché essere abbinato di nuovo a un account Azure AD esistente (oggetto orfano). Soluzione: Usare Azure AD Connect Health nel portale di Azure per eseguire di nuovo il mapping di ancoraggio di origine o ImmutableID. Per altre informazioni, vedere [Scenario con oggetto orfano](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Modifica che causa un'interruzione: aggiornamenti apportati allo schema dei log di controllo e di accesso tramite Monitoraggio di Azure

**Tipo:** Funzionalità modificata **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

Sono attualmente in fase di pubblicazione flussi di log di controllo e accesso tramite Monitoraggio di Azure, per poter integrare uniformemente i file di log con gli strumenti di informazioni di sicurezza e gestione degli eventi o con Log Analytics. In base ai commenti e suggerimenti degli utenti e in preparazione all'annuncio della disponibilità generale di questa funzionalità, Microsoft sta apportando le modifiche seguenti allo schema. Queste modifiche dello schema e i relativi aggiornamenti alla documentazione verranno completati entro la prima settimana di gennaio.

#### <a name="new-fields-in-the-audit-schema"></a>Nuovi campi nello schema di controllo
Sta per essere aggiunto il nuovo campo **Tipo di operazione**, che permetterà di specificare il tipo di operazione eseguita sulla risorsa. Ad esempio, **aggiunta**, **aggiornamento** o **eliminazione**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campi modificati nello schema di controllo
È in corso la modifica dei campi seguenti nello schema di controllo:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|Category|Campo **Nome servizio** nelle versioni precedenti. Si tratta ora del campo **Audit Categories** (Categorie di controllo). Il campo **Nome del servizio** è stato rinominato in **loggedByService**.|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione password self-service</li></ul>|<ul><li>User Management</li><li>Gestione di gruppi</li><li>Gestione app</li></ul>|
|targetResources|Include **TargetResourceType** al livello superiore.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Utente</li><li>Gruppo</li></ul>|
|loggedByService|Specifica il nome del servizio che ha generato il log di controllo.|Null|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione della password self-service</li></ul>|
|Risultato|Indica il risultato dei log di controllo. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campi modificati nello schema di accesso
È in corso la modifica dei campi seguenti nello schema di accesso:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Campo **conditionalaccessPolicies** nelle versioni precedenti. Si tratta ora del campo **appliedConditionalAccessPolicies**.|Nessuna modifica|Nessuna modifica|
|conditionalAccessStatus|Indica il risultato dello stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li><li>Non applicato</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: risultato|Indica il risultato del singolo stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Operazione completata</li><li>Operazioni non riuscite</li><li>Non applicato</li><li>Disabled</li></ul>|

Per altre informazioni sullo schema, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Miglioramenti relativi a Identity Protection apportati al modello di apprendimento automatico supervisionato e al motore dei punteggi di rischio

**Tipo:** Funzionalità modificata **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Punteggi di rischio

I miglioramenti apportati al motore di valutazione dei punteggi di rischio utente e di accesso in relazione a Identity Protection ottimizzano la copertura e la precisione del rischio utente. Gli amministratori potrebbero notare che il livello di rischio utente non è più direttamente collegato al livello di rischio di rilevamenti specifici e che vi è stato un aumento nel numero e nel livello di eventi di accesso rischiosi.

I rilevamenti del rischio vengono ora valutati dal modello di Machine Learning supervisionato, che calcola il rischio utente tramite funzionalità aggiuntive degli accessi dell'utente e un modello dei rilevamenti. Attraverso questo modello l'amministratore può individuare gli utenti con punteggi di rischio elevato, anche se i rilevamenti associati all'utente indicano un rischio basso o medio.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Possibilità per gli amministratori di reimpostare la propria password tramite l'app Microsoft Authenticator (anteprima pubblica)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Gli amministratori di Azure AD possono ora reimpostare la propria password seguendo le notifiche dell'app Microsoft Authenticator o tramite un codice da qualsiasi app di autenticazione per dispositivi mobili o token hardware. Per reimpostare la propria password, gli amministratori possono ora usare due metodi diversi:

- Notifica dell'app Microsoft Authenticator

- Altro codice di token hardware/app di autenticazione per dispositivi mobili

- Email

- Chiamata telefonica

- SMS

Per altre informazioni sull'uso dell'app Microsoft Authenticator per reimpostare le password, vedere [Reimpostazione della password self-service di Azure AD - App per dispositivi mobili e SSPR (anteprima)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nuovo ruolo Amministratore dispositivo cloud di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Registrazione e gestione dei dispositivi **Funzionalità del prodotto:** Controllo di accesso

Gli amministratori possono assegnare agli utenti il nuovo ruolo Amministratore dispositivo cloud per l'esecuzione di attività di amministrazione dei dispositivi cloud. Gli utenti assegnati al ruolo Amministratore dispositivo cloud possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure.

Per altre informazioni sui ruoli e sulle autorizzazioni, vedere [Assegnazione di ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gestione dei dispositivi tramite il nuovo timestamp di attività in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Registrazione e gestione dei dispositivi **Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Con il passare del tempo, sarà necessario aggiornare e ritirare i dispositivi dell'organizzazione in Azure AD, per evitare la presenza di dispositivi non aggiornati nell'ambiente. Per semplificare questo processo, Azure AD aggiorna ora i dispositivi con un nuovo timestamp di attività, che permette di gestirne il ciclo di vita.

Per altre informazioni su come ottenere e usare questo timestamp, vedere [Procedura: Gestire i dispositivi non aggiornati in Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Possibilità per gli amministratori di chiedere agli utenti di accettare le condizioni per l'utilizzo in ogni dispositivo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Gli amministratori possono ora attivare l'opzione **Richiedi il consenso degli utenti in ogni dispositivo** per chiedere agli utenti di accettare le condizioni per l'utilizzo su ogni dispositivo usato nel tenant.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Possibilità per gli amministratori di configurare la scadenza delle condizioni per l'utilizzo in base a una pianificazione ricorrente

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance


Gli amministratori possono ora attivare l'opzione **Scadenza consensi** per impostare una scadenza delle condizioni per l'utilizzo per tutti gli utenti in base alla pianificazione ricorrente specificata. La pianificazione può essere annuale, biennale, trimestrale o mensile. Dopo la scadenza delle condizioni per l'utilizzo, gli utenti devono riaccettarle.

Per altre informazioni, vedere la [sezione Aggiungere le condizioni per l'utilizzo nell'articolo Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Possibilità per gli amministratori di configurare la scadenza delle condizioni per l'utilizzo in base alla pianificazione di ogni utente

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Gli amministratori possono ora specificare un periodo di tempo dopo il quale un utente deve riaccettare le condizioni per l'utilizzo. Ad esempio, gli amministratori possono specificare che gli utenti devono riaccettare le condizioni per l'utilizzo ogni 90 giorni.

Per altre informazioni, vedere la [sezione Aggiungere le condizioni per l'utilizzo nell'articolo Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nuovi messaggi di posta elettronica di Azure AD Privileged Identity Management (PIM) per ruoli di Azure Active Directory

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

I clienti che usano Azure AD Privileged Identity Management (PIM) possono ora ricevere un messaggio di posta elettronica di riepilogo settimanale, che include le informazioni seguenti per gli ultimi sette giorni:

- Panoramica delle assegnazioni di ruoli permanenti e idonei principali

- Numero di utenti che attivano ruoli

- Numero di utenti assegnati a ruoli in PIM

- Numero di utenti assegnati a ruoli al di fuori di PIM

- Numero di utenti "resi permanenti" in PIM

Per altre informazioni su PIM e sulle notifiche di posta elettronica disponibili, vedere [Notifiche tramite posta elettronica in PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licenze basate sui gruppi ora disponibili a livello generale

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Directory

Le licenze basate sui gruppi sono ora in anteprima pubblica e disponibili a livello generale. Nell'ambito della disponibilità generale questa funzionalità è stata resa più scalabile e sono state aggiunte la possibilità di rielaborare assegnazioni di licenze basate sui gruppi per un singolo utente e quella di usare licenze basate sui gruppi con licenze di Office 365 E3/A3.

Per altre informazioni sulle licenze basate sui gruppi, vedere [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di novembre 2018 sono state aggiunte alla raccolta di app queste 26 nuove app con supporto per la federazione:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps - Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Ottobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Log di Azure AD ora funziona con Azure Log Analytics (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che è ora possibile inoltrare i log di Azure AD ad Azure Log Analytics. Questa funzionalità molto richiesta consente di fornire un accesso ancora migliore ad analisi per l'azienda, operazioni e sicurezza, nonché un modo per monitorare l'infrastruttura. Per altre informazioni, consultare il blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Log attività di Azure Active Directory in Azure Log Analytics ora disponibile).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di ottobre 2018 sono state aggiunte 14 nuove app con il supporto della federazione alla raccolta di app:

[My Award Points](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifiche tramite posta elettronica di Azure AD Domain Services

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD Domain Services **Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Azure AD Domain Services offre la funzionalità di avvisi relativi ad errori o problemi di configurazione sul portale di Azure. Questi avvisi includono le procedure dettagliate per tentare di risolvere i problemi senza dover contattare l'assistenza.

A partire da ottobre, sarà possibile personalizzare le impostazioni di notifica per il dominio gestito, in modo che quando vengono generati nuovi avvisi, viene inviato un messaggio di posta elettronica a un gruppo di utenti specificato, eliminando la necessità di controllare continuamente il portale per verificare la presenza di aggiornamenti.

Per altre informazioni, vedere [Impostazioni di notifica in Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Il portale di Azure AD supporta l'uso dell'API di dominio ForceDelete per eliminare i domini personalizzati

**Tipo:** Funzionalità modificata **Categoria di servizio:** Gestione directory **Funzionalità del prodotto:** Directory

Siamo lieti di annunciare che è ora possibile usare l'API di dominio ForceDelete per eliminare i nomi di dominio personalizzati tramite la ridenominazione asincrona dei riferimenti, ad esempio utenti, gruppi e app dal nome di dominio personalizzato (contoso.com) al nome di dominio predefinito iniziale (contoso.onmicrosoft.com).

Questa modifica consente di eliminare in modo più rapido i nomi di dominio personalizzati se l'organizzazione non usa più il nome o se deve usare il nome di dominio in un'altra directory di Azure AD.

Per altre informazioni, vedere [Delete a custom domain name](../enterprise-users/domains-manage.md#delete-a-custom-domain-name) (Eliminare un nome di dominio personalizzato).

---

## <a name="september-2018"></a>Settembre 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Autorizzazioni del ruolo di amministratore aggiornate per i gruppi dinamici

**Tipo:** Fisso **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

È stato risolto un problema e ora i ruoli di amministratore specifici possono creare e aggiornare le regole di appartenenza dinamica, senza la necessità di essere il proprietario del gruppo.

I ruoli sono:

- Amministratore globale

- Amministratore di Intune

- Amministratore utenti

Per altre informazioni, vedere [Creare un gruppo dinamico e controllare lo stato](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Impostazioni di configurazione di Single Sign-On (SSO) semplificate per alcune app di terze parti

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

È risaputo che la configurazione di Single Sign-On (SSO) per le app software come un servizio (SaaS, Software as a Service) può risultare complessa a causa della natura unica della configurazione di ogni app. È stata creata un'esperienza di configurazione semplificata per popolare automaticamente le impostazioni di configurazione di SSO per le app SaaS di terze parti seguenti:

- Zendesk

- ArcGis Online

- Jamf Pro

Per iniziare a usare questa esperienza con un clic, passare al **portale di Azure** >  pagina **SSO configuration** (Configurazione SSO) per l'app. Per altre informazioni, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - Pagina Dove vengono archiviati i tuoi dati?

**Tipo:** Nuova funzionalità **Categoria di servizio:** Altro **Funzionalità del prodotto:** GoLocal

Selezionare l'area della società nella pagina **Azure Active Directory - Dove vengono archiviati i tuoi dati** per visualizzare il data center di Azure che ospita i dati inattivi di Azure AD per tutti i servizi di Azure AD. È possibile filtrare le informazioni in base ai servizi di Azure AD specifici per l'area della società.

Per accedere a questa funzionalità e per altre informazioni, vedere [Azure Active Directory - Dove vengono archiviati i tuoi dati](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nuovo piano di distribuzione disponibile per il pannello di accesso App personali

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** SSO

Vedere il nuovo piano di distribuzione disponibile per il pannello di accesso App personali (https://aka.ms/deploymentplans).
Il pannello di accesso App personali fornisce agli utenti un'unica posizione per trovare le app e accedervi. Questo portale offre agli utenti anche l'opportunità di eseguire attività self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso a queste risorse per conto di altri.

Per altre informazioni, vedere [Informazioni sul portale delle app personali](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nuova scheda Risoluzione dei problemi e supporto nella pagina Accessi del portale di Azure

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

La nuova scheda **Risoluzione dei problemi e supporto** nella pagina **Accessi** del portale di Azure ha lo scopo di semplificare la risoluzione dei problemi relativi agli accessi ad Azure AD per amministratori e tecnici del supporto. Questa nuova scheda indica il codice errore, il messaggio di errore ed eventuali consigli per la correzione per risolvere il problema. Se il problema non è risolvibile, è anche possibile creare un ticket di supporto usando l'esperienza **Copia negli Appunti**, che popola i campi **ID richiesta** e **Data (UTC)** per il file di log nel ticket di supporto.

![Log degli accessi con la nuova scheda](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Supporto migliorato per le proprietà di estensione personalizzate usate per creare regole di appartenenza dinamica

**Tipo:** Funzionalità modificata **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

Con questo aggiornamento, è ora possibile fare clic sul collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dei gruppi utenti dinamica, immettere l'ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica per gli utenti. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

Per altre informazioni sull'uso delle proprietà di estensione personalizzate per le regole di appartenenza dinamica, vedere [Proprietà di estensione e proprietà di estensione personalizzate](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

**Tipo:** Modifica prevista **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft To-Do

- Microsoft Stream

Per altre informazioni, vedere:

- [Accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nuovo supporto per la reimpostazione della password self-service dalla schermata di blocco di Windows 7/8/8.1

**Tipo:** Nuova funzionalità **Categoria di servizio:** Reimpostazione della password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Dopo aver configurato questa nuova funzionalità, gli utenti visualizzeranno un collegamento per reimpostare la password dalla schermata **Blocco** di un dispositivo che esegue Windows 7, Windows 8 o Windows 8.1. Facendo clic su tale collegamento, viene avviato lo stesso flusso di reimpostazione della password del Web browser.

Per altre informazioni, vedere [Come abilitare la reimpostazione della password da Windows 7, 8 e 8.1](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: codici di autorizzazione non più disponibili per il riutilizzo

**Tipo:** Modifica prevista **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di settembre 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO for Azure, SurveyMonkey, [Convene](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Supporto per i metodi aggiuntivi di trasformazione delle attestazioni

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Sono stati introdotti nuovi metodi di trasformazione delle attestazioni, ToLower() e ToUpper(), che possono essere applicati ai token SAML dalla pagina **Configurazione Single Sign-on** basata su SAML.

Per altre informazioni, vedere [Come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interfaccia utente di configurazione delle app basate su SAML aggiornata (anteprima)

**Tipo:** Funzionalità modificata **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Caratteristiche dell'interfaccia utente di configurazione delle app basate su SAML aggiornata:

- Esperienza aggiornata per la configurazione delle app basate su SAML.

- Maggiore visibilità sugli elementi mancanti o non corretti nella configurazione.

- Possibilità di aggiungere più indirizzi di posta elettronica per la notifica della scadenza dei certificati.

- Nuovi metodi di trasformazione delle attestazioni, ToLower () e ToUpper (), e altro ancora.

- Possibilità di caricare il proprio certificato per la firma di token per le app aziendali.

- Possibilità di impostare il formato di NameID per le app SAML e di impostare il valore di NameID come estensioni della directory.

Per attivare questa visualizzazione aggiornata, fare clic sul collegamento **Prova la nuova esperienza** nella parte superiore della pagina **Single Sign-On**. Per altre informazioni, vedere [Esercitazione: Configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Agosto 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Modifiche agli intervalli di indirizzi IP di Azure Active Directory

**Tipo:** Modifica prevista **Categoria di servizio:** Altro **Funzionalità del prodotto:** Piattaforma

È in corso l'introduzione di intervalli IP più ampi in Azure AD. Ciò indica che se gli intervalli di indirizzi IP di Azure AD per i firewall, i router o i gruppi di sicurezza di rete sono già stati configurati, è necessario aggiornarli. Questo aggiornamento ha lo scopo di evitare altre modifiche alla configurazione degli intervalli IP dei firewall, dei router o dei gruppi di sicurezza di rete quando Azure AD aggiunge nuovi endpoint.

Il traffico di rete passerà a questi nuovi intervalli nel corso dei prossimi due mesi. Per garantirsi la continuità del servizio, è necessario aggiungere questi valori aggiornati agli indirizzi IP prima del 10 settembre 2018:

- 20.190.128.0/18

- 40.126.0.0/18

È consigliabile rimuovere gli intervalli di indirizzi IP precedenti solo dopo che tutto il traffico di rete è passato ai nuovi intervalli. Per aggiornamenti sul passaggio e per informazioni su quando rimuovere gli intervalli precedenti, vedere [Office 365 URLs and IP address ranges](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) (URL e intervalli di indirizzi IP di Office 365).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: codici di autorizzazione non più disponibili per il riutilizzo

**Tipo:** Modifica prevista **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione self-service della password e l'autenticazione MFA (Multi-Factor Authentication)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Reimpostazione della password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio numero di telefono, app per dispositivi mobili e così via) per la reimpostazione password self-service e Multi-Factor Authentication in un'unica posizione e con un'unica esperienza, a differenza della situazione precedente in cui l'operazione veniva eseguita in due posizioni diverse.

Questa esperienza convergente si applica anche agli utenti che usano la reimpostazione password self-service o Multi-Factor Authentication. Se l'organizzazione non prevede la registrazione per Multi-Factor Authentication o la reimpostazione password self-service, gli utenti possono comunque registrare qualsiasi metodo correlato alle informazioni di sicurezza consentito dall'organizzazione nel portale App personali.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per tutti gli utenti di un tenant o solo per un gruppo selezionato. Per altre informazioni sull'esperienza convergente, vedere il blog sull'[esperienza convergente](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nuova impostazione HTTP-Only cookies (Cookie solo HTTP) nelle app del proxy di applicazione di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Nelle app del proxy di applicazione è presente una nuova impostazione denominata **HTTP-Only Cookies** (Cookie solo HTTP). Questa impostazione consente di fornire sicurezza aggiuntiva includendo il flag HTTPOnly nell'intestazione della risposta HTTP per entrambi i cookie di sessione e di accesso al proxy di applicazione, interrompendo l'accesso al cookie da uno script lato client ed evitando ulteriormente azioni come la copia o la modifica del cookie. Anche se questo flag non è stato usato in precedenza, i cookie sono sempre stati crittografati e trasmessi tramite una connessione TLS per consentire la protezione da modifiche non corrette.

Questa impostazione non è compatibile con le app che usano i controlli ActiveX, ad esempio Desktop remoto. In questo caso è consigliabile disattivare l'impostazione.

Per altre informazioni sull'impostazione HTTP-Only Cookies (Cookie solo HTTP), vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) per le risorse di Azure supporta i tipi di risorsa dei gruppi di gestione

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Le impostazioni JIT e di assegnazione possono ora essere applicate ai tipi di risorsa dei gruppi di gestione, in modo analogo a come accade per sottoscrizioni, gruppi di risorse e risorse (ad esempio macchine virtuali, Servizi app e altro ancora). Tutti gli utenti con un ruolo che consente a un amministratore di accedere a un gruppo di gestione possono individuare e gestire tale risorsa in PIM.

Per altre informazioni su PIM e sulle risorse di Azure, vedere [Individuare e gestire le risorse di Azure usando Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>L'accesso alle applicazioni (in anteprima) consente di accedere più rapidamente al portale di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

L'attivazione di un ruolo tramite PIM può richiedere ora oltre 10 minuti prima che le autorizzazioni diventino effettive. Se si sceglie di usare l'accesso alle applicazioni, ora in versione di anteprima pubblica, gli amministratori possono accedere al portale di Azure AD non appena viene completata la richiesta di attivazione.

L'accesso alle applicazioni supporta attualmente solo l'esperienza del portale di Azure AD e le risorse di Azure. Per altre informazioni su PIM e l'accesso alle applicazioni, vedere [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Agosto 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di agosto 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [Sauce Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), [Way We Do](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [Dossier](../saas-apps/dossier-tutorial.md), [N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Il supporto nativo di Tableau è ora disponibile nel proxy di applicazione di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Con l'aggiornamento dal protocollo OpenID Connect al protocollo OAuth 2.0 Code Grant per il nostro protocollo di autenticazione, non è più necessario eseguire alcuna configurazione aggiuntiva per usare Tableau con il proxy di applicazione. Questa modifica al protocollo consente anche al proxy di applicazione di migliorare il supporto di app più moderne usando solo reindirizzamenti HTTP, supportati in genere in tag JavaScript e HTML.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nuovo supporto per aggiungere Google come provider di identità per utenti guest B2B in Azure Active Directory (anteprima)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C

Se si configura la federazione con Google nell'organizzazione, è possibile consentire agli utenti Gmail invitati di accedere alle app e alle risorse condivise tramite i propri account Google esistenti senza la necessità di creare un account Microsoft personale o un account Azure AD.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Per altre informazioni sulla federazione di Google, vedere [Add Google as an identity provider for B2B guest users](../external-identities/google-federation.md) (Aggiungere Google come provider di identità per utenti guest B2B).

---

## <a name="july-2018"></a>Luglio 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Miglioramenti alle notifiche di posta elettronica di Azure Active Directory

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

I messaggi di posta elettronica di Azure Active Directory (Azure AD) ora presentano una struttura grafica aggiornata, nonché modifiche all'indirizzo di posta elettronica e al nome visualizzato del mittente, quando vengono inviati dai servizi seguenti:

- Verifiche di accesso di Azure AD
- Azure AD Connect Health
- Azure AD Identity Protection
- Gestione identità con privilegi di Azure AD
- Notifiche di certificati in scadenza di app aziendali
- Notifiche del servizio di provisioning di app aziendali

Le notifiche di posta elettronica verranno inviate dall'indirizzo di posta elettronica e dal nome visualizzato seguenti:

- Indirizzo di posta elettronica: azure-noreply@microsoft.com
- Nome visualizzato: Microsoft Azure

Per esempi della nuova struttura grafica dei messaggi di posta elettronica e per altre informazioni, vedere [Email notifications in Azure AD PIM](../privileged-identity-management/pim-email-notifications.md) (Notifiche di posta elettronica in Azure AD PIM).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>I log attività di Azure AD ora sono disponibili tramite Monitoraggio di Azure

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD ora sono disponibili in anteprima pubblica per Monitoraggio di Azure (il servizio monitoraggio di Azure a livello di piattaforma). Monitoraggio di Azure offre funzionalità di conservazione a lungo termine e un'integrazione senza problemi, oltre ai miglioramenti seguenti:

- Conservazione a lungo termine tramite il routing dei file di log all'account di Archiviazione di Azure.

- Completa integrazione con i sistemi SIEM, senza che sia necessario scrivere o gestire script personalizzati.

- Completa integrazione con soluzioni personalizzate, strumenti di analisi o soluzioni di gestione degli eventi imprevisti.

Per altre informazioni sulle nuove funzionalità, vedere il blog relativo alla [disponibilità in anteprima pubblica dei log attività di Azure AD nella diagnostica di Monitoraggio di Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e la documentazione [Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Sono state aggiunte informazioni sull'accesso condizionale al report sugli accessi di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Questo aggiornamento consente di visualizzare i criteri che vengono valutati quando un utente esegue l'accesso, insieme al risultato dei criteri. Inoltre, il report ora include il tipo di app client usato dall'utente, quindi è possibile identificare il traffico dei protocolli legacy. È inoltre possibile cercare tra le voci del report un ID di correlazione, che è disponibile nel messaggio di errore per l'utente finale e può essere usato per identificare e risolvere i problemi della richiesta di accesso corrispondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visualizzazione delle autenticazioni legacy tramite i log attività di accesso

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

Con l'introduzione del campo **App client** nei log attività di accesso, i clienti possono ora vedere gli utenti che usano autenticazioni legacy. I clienti potranno accedere a queste informazioni usando l'API Microsoft Graph per gli accessi o tramite i log attività di accesso nel portale di Azure AD, in cui è possibile usare il controllo **App client** per filtrare le autenticazioni legacy. Per altre informazioni, vedere la documentazione.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Luglio 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di luglio 2018 sono state aggiunte queste 16 nuove app con il supporto della federazione alla raccolta di app:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic](../saas-apps/screencast-tutorial.md), PowerSchool Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](../saas-apps/skillsbase-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Luglio 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Integrazione con app di terze parti

Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A luglio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync: un modo più semplice per risolvere gli errori di sincronizzazione di attributi orfani e duplicati

**Tipo:** Nuova funzionalità **Categoria di servizio:** AD Connect **Funzionalità del prodotto:** Monitoraggio e creazione report

Azure AD Connect Health introduce funzionalità di correzione in modalità self-service che consentono di evidenziare e correggere gli errori di sincronizzazione. Questa funzionalità consente di risolvere gli errori di sincronizzazione degli attributi duplicati e di correggere gli oggetti resi orfani da Azure AD. Questa diagnostica offre i vantaggi seguenti:

- Consente di limitare gli errori di sincronizzazione degli attributi duplicati, fornendo correzioni specifiche

- Applica una correzione per gli scenari di Azure AD dedicati, consentendo la risoluzione degli errori in un unico passaggio

- Per attivare e usare questa funzionalità, non sono richiesti aggiornamenti o configurazioni

Per altre informazioni, vedere [Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aggiornamenti dell'interfaccia utente per le esperienze di accesso ad Azure AD e con account del servizio gestito

**Tipo:** Funzionalità modificata **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Autenticazione dell'utente

È stata aggiornata l'interfaccia utente per l'esperienza di accesso dei servizi online di Microsoft, ad esempio per Office 365 e Azure. Questa modifica rende le schermate più semplici e lineari. Per altre informazioni su questa modifica, vedere il blog [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) (Prossimi miglioramenti all'esperienza di accesso di AD Azure).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nuova versione di Azure AD Connect - luglio 2018

**Tipo:** Funzionalità modificata **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

La versione più recente di Azure AD Connect include:

- Correzioni di bug e aggiornamenti relativi al supporto

- Disponibilità generale dell'integrazione di PingFederate

- Aggiornamenti del client più recente di SQL 2012

Per altre informazioni su questo aggiornamento, vedere [Azure AD Connect: Cronologia delle versioni](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aggiornamenti dell'interfaccia utente delle condizioni per l'utilizzo

**Tipo:** Funzionalità modificata **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Stiamo aggiornando la stringa di accettazione nell'interfaccia utente delle Condizioni per l'utilizzo.

**Testo corrente.** Per accedere alle risorse di [NomeTenant], è necessario accettare le condizioni per l'utilizzo.<br>**Nuovo testo.** Per accedere alla risorsa di [NomeTenant], è necessario leggere le condizioni per l'utilizzo.

**Testo corrente:** Scegliendo di accettare, l'utente accetta tutte le condizioni per l'utilizzo indicate.<br>**Nuovo testo:** Fare clic su Accetto per confermare di avere letto e compreso le condizioni per l'utilizzo.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>L'autenticazione pass-through supporta le applicazioni e i protocolli legacy

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

L'autenticazione pass-through ora supporta le app e i protocolli legacy. Le limitazioni seguenti ora sono completamente supportate:

- Accessi utente ad applicazioni client legacy di Office (Office 2010 e Office 2013) senza che sia necessaria l'autenticazione moderna.

- Accesso alla condivisione del calendario e alle informazioni sulla disponibilità negli ambienti ibridi di Exchange solo in Office 2010.

- Accessi utente per applicazioni client Skype for Business senza che sia necessaria l'autenticazione moderna.

- Accessi utente a PowerShell versione 1.0.

- Apple Device Enrollment Program (Apple DEP) con l'Assistente configurazione di iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication

**Tipo:** Nuova funzionalità **Categoria di servizio:** Reimpostazione della password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio, numero di telefono, indirizzo di posta elettronica, app per dispositivi mobili e così via) per la reimpostazione della password self-service e Multi-Factor Authentication in una singola esperienza. Gli utenti non dovranno più registrare le stesse informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication in due diverse esperienze. Questa nuova esperienza si applica anche agli utenti che dispongono della reimpostazione della password self-service o di Multi-Factor Authentication.

Se un'organizzazione non impone la registrazione per Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono registrare le proprie informazioni di sicurezza tramite il portale **App personali**. Da qui, gli utenti possono eseguire la registrazione per qualsiasi metodo abilitato per MFA o SSPR.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per un gruppo selezionato di utenti o tutti gli utenti in un tenant.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Usare l'app Microsoft Authenticator per verificare la propria identità durante la reimpostazione della password

**Tipo:** Funzionalità modificata **Categoria di servizio:** Reimpostazione della password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

Questa funzionalità consente agli utenti non amministratori di verificare la propria identità durante la reimpostazione di una password tramite una notifica o un codice di Microsoft Authenticator (o qualsiasi altra app di autenticazione). Dopo che gli amministratori attivano questo metodo di reimpostazione della password self-service, gli utenti che hanno eseguito la registrazione di un'app per dispositivi mobili tramite aka.ms/mfasetup o aka.ms/setupsecurityinfo possono usare l'app per dispositivi mobili come metodo di verifica durante la reimpostazione della password.

Le notifiche dell'app per dispositivi mobili possono essere attivate solo come parte di un criterio che richiede due metodi di reimpostazione della password.

---

## <a name="june-2018"></a>Giugno 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Avviso di modifica: correzione di sicurezza per il flusso di autorizzazioni delegate per le app che usano l'API dei log di attività di Azure AD

**Tipo:** Modifica prevista **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

A causa dell'applicazione della sicurezza più avanzata, è stato necessario apportare una modifica alle autorizzazioni per le app che usano un flusso di autorizzazioni delegate per accedere alle [API Log attività di Azure AD](../reports-monitoring/concept-reporting-api.md). Questa modifica verrà applicata entro il **26 giugno 2018**.

Se una delle app usa l'API Log attività di Azure AD, seguire questa procedura per verificare che l'app non venga interrotta dopo l'applicazione della modifica.

**Per aggiornare le autorizzazioni delle app**

1. Accedere al portale di Azure, selezionare **Azure Active Directory** e quindi selezionare **Registrazioni per l'app**.
2. Selezionare l'app che usa l'API Log attività di Azure AD, selezionare **Impostazioni**, quindi **Autorizzazioni necessarie** e infine selezionare l'API di **Windows Azure Active Directory**.
3. Nell'area **Autorizzazioni delegate** del pannello **Abilita accesso** selezionare la casella accanto a **Lettura dati directory** e quindi selezionare **Salva**.
4. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**.

    >[!Note]
    >È necessario essere un amministratore globale per concedere le autorizzazioni all'app.

Per altre informazioni, vedere l'area [Concedere le autorizzazioni](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) dell'articolo sui prerequisiti per l'accesso all'API di creazione report di Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurare le impostazioni TLS per connettersi ai servizi di Azure AD per la conformità PCI DSS

**Tipo:** Nuova funzionalità **Categoria di servizio:** N/D **Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) è un protocollo che fornisce riservatezza e integrità dei dati tra due applicazioni in comunicazione ed è il protocollo di sicurezza più usato oggi.

Il [PCI Security Standards Council](https://www.pcisecuritystandards.org/) ha determinato che le versioni precedenti di TLS e Secure Sockets Layer (SSL) devono essere disabilitate a favore dell'abilitazione di protocolli di app nuovi e più sicuri, la cui conformità è prevista a partire dal **30 giugno 2018**. Questo cambiamento comporta che, se ci si connette ai servizi di Azure AD e si richiede la conformità PCI DSS, è necessario disabilitare TLS 1.0. Sono disponibili più versioni di TLS, ma TLS 1.2 è la versione più recente disponibile per i servizi di Azure Active Directory. Si consiglia di passare direttamente a TLS 1.2 per le combinazioni sia browser-server sia client-server.

I browser non aggiornati potrebbero non supportare le versioni più recenti di TLS, ad esempio TLS 1.2. Per vedere quali versioni di TLS sono supportate dal browser, accedere al sito [Qualys SSL Labs](https://www.ssllabs.com/) e fare clic su **Test your browser** (Verifica browser). È consigliabile eseguire l'aggiornamento alla versione più recente del Web browser e preferibilmente abilitare solo TLS 1.2.

**Per abilitare TLS 1.2 nei diversi browser**

- **Microsoft Edge e Internet Explorer (si configurano entrambi usando Internet Explorer)**

    1. Aprire Internet Explorer, selezionare **Strumenti** > **Opzioni Internet** > **Avanzate**.
    2. Nell'area **Sicurezza** selezionare **Usa TLS 1.2** e quindi fare clic su **OK**.
    3. Chiudere tutte le finestre del browser e riavviare Internet Explorer.

- **Google Chrome**

    1. Aprire Google Chrome, digitare *chrome://settings/* nella barra degli indirizzi e premere **Invio**.
    2. Espandere le opzioni **Avanzate**, passare all'area **Sistema** e selezionare **Apri le impostazioni proxy**.
    3. Nella casella **Proprietà Internet** selezionare la scheda **Avanzate**, passare all'area **Sicurezza**, selezionare **Usa TLS 1.2** e fare clic su **OK**.
    4. Chiudere tutte le finestre del browser e riavviare Google Chrome.

- **Mozilla Firefox**

    1. Aprire Firefox, digitare *about:config* nella barra degli indirizzi e premere **Invio**.
    2. Cercare il termine *TLS* e quindi selezionare la voce **security.tls.version.max**.
    3. Impostare il valore su **3** per forzare il browser a usare TLS fino alla versione 1.2, quindi selezionare **OK**.

        >[!NOTE]
        >Firefox versione 60.0 supporta TLS 1.3, perciò è possibile impostare il valore security.tls.version.max anche su **4**.

    4. Chiudere tutte le finestre del browser e riavviare Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Giugno 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di giugno 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Skytap](../saas-apps/skytap-tutorial.md), [Settling music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>La protezione delle password di AD Azure è disponibile in anteprima pubblica

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Autenticazione dell'utente

Usare la protezione delle password di Azure Active Directory per eliminare le password facilmente individuabili dall'ambiente. Eliminando queste password è possibile diminuire il rischio di compromissione da attacco password spraying.

In particolare, la protezione delle password di Azure AD aiuta a:

- Proteggere gli account dell'organizzazione sia in Azure AD che in Windows Server Active Directory (AD).
- Impedisce agli utenti di usare le password di un elenco di più di 500 delle password utilizzate più di frequente e oltre 1 milione di varianti di tali password con sostituzione di caratteri.
- Amministrare la protezione delle password di Azure AD da un'unica posizione nel portale di Azure AD sia per Azure AD che per Windows Server Active Directory locale.

Per altre informazioni sulla protezione della password di Azure AD, vedere [Eliminare le password non appropriate nell'organizzazione](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "tutti i guest" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Durante la creazione delle condizioni per l'utilizzo viene anche creato un nuovo modello di criteri di accesso condizionale per "tutti i guest" e "tutte le app". Questo nuovo modello di criteri si applica alle Condizioni per l'utilizzo appena create, semplificando il processo di creazione e applicazione per i guest.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nuovo modello di criteri di accesso condizionale "personalizzato" creato durante la creazione delle condizioni per l'utilizzo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Governance

Durante la creazione delle condizioni per l'utilizzo viene anche creato un nuovo modello di criteri di accesso condizionale "personalizzato". Questo nuovo modello di criteri consente di creare le condizioni per l'utilizzo e passare immediatamente al pannello di creazione dei criteri di accesso condizionale, senza doversi spostare manualmente all'interno del portale.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Linee guida nuove e complete sulla distribuzione Azure AD Multi-Factor Authentication

**Tipo:** Nuova funzionalità **Categoria di servizio:** Altro **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Sono stati rilasciati nuovi consigli dettagliati su come distribuire Azure AD Multi-Factor Authentication (MFA) nell'organizzazione.

Per visualizzare la guida alla distribuzione di Multi-Factor Authentication, visitare il repository [Identity Deployment Guides](./active-directory-deployment-plans.md) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>I ruoli di gestione delle app delegati di Azure Active Directory sono in anteprima pubblica

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Controllo di accesso

Gli amministratori ora possono delegare le attività di gestione delle app senza assegnare il ruolo di amministratore globale. I nuovi ruoli e funzionalità sono:

- **Nuovi ruoli amministrativi standard di Azure AD:**

    - **Amministratore di applicazioni.** Concede la capacità di gestire tutti gli aspetti di tutte le app, fra cui registrazione, impostazioni SSO, assegnazioni di app e licenze, impostazioni proxy di applicazione e consenso (tranne che per le risorse di Azure AD).

    - **Amministratore di applicazioni cloud.** Concede tutte le capacità di Amministratore di applicazioni, eccetto Proxy di app perché non fornisce l'accesso locale.

    - **Sviluppatore di applicazioni.** Concede la capacità di creare registrazioni delle app, anche se l'opzione **consentire agli utenti di registrare le app** è disattivata.

- **La proprietà, configurata per registrazione di app e per app aziendale, analogamente al processo di proprietà del gruppo:**

    - **Proprietario della registrazione delle app.** Concede la capacità di gestire tutti gli aspetti della registrazione delle app di proprietà, inclusi il manifesto dell'app e l'aggiunta di altri proprietari.

    - **Proprietario di app aziendali.** Concede la capacità di gestire numerosi aspetti delle app aziendali di proprietà, fra cui impostazioni SSO, assegnazioni di app e consenso (tranne che per le risorse di Azure AD).

Per altre informazioni sull'anteprima pubblica, vedere il blog [Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (I ruoli di gestione delle app delegati di Azure AD sono in anteprima pubblica) . Per altre informazioni sui ruoli e le autorizzazioni, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maggio 2018

### <a name="expressroute-support-changes"></a>Modifiche al supporto di ExpressRoute

**Tipo:** Modifica prevista **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Piattaforma

Le offerte di software come un servizio, ad esempio Azure Active Directory (Azure AD), sono progettate per ottenere la massima efficienza accedendo direttamente a Internet, senza la necessità di ExpressRoute o di altri tunnel VPN privati. Per questo motivo, l'**1 agosto 2018**, verrà interrotto il supporto di ExpressRoute per i servizi di Azure AD che usano il peering pubblico di Azure e le community di Azure nel peering Microsoft. Tutti i servizi interessati da questa modifica potrebbero riscontrare il graduale passaggio del traffico di Azure AD da ExpressRoute a Internet.

Anche se la modifica al supporto è in corso, è risaputo che esistono ancora situazioni in cui potrebbe essere necessario usare un set di circuiti dedicato per il traffico di autenticazione. Per questo motivo, Azure AD continuerà a supportare le restrizioni per gli intervalli IP in base al tenant usando ExpressRoute e i servizi già inclusi nel peering Microsoft con la community "Altri servizi Online di Office 365". Se i servizi in uso sono interessati, ma è richiesto ExpressRoute, è necessario seguire questa procedura:

- **Se si è nel peering pubblico di Azure.** Passare al peering Microsoft e iscriversi alla community **Altri servizi online di Office 365 (12076:5100)** . Per altre informazioni su come passare dal peering pubblico di Azure al peering Microsoft, vedere l'articolo [Spostare un peering pubblico nel peering Microsoft](../../expressroute/how-to-move-peering.md).

- **Se si è nel peering Microsoft.** Iscriversi alla community **Altri servizi online di Office 365 (12076:5100)** . Per altre informazioni sui requisiti per il routing, vedere la [sezione Supporto per le community BGP](../../expressroute/expressroute-routing.md#bgp) dell'articolo Requisiti per il routing di ExpressRoute.

Se è necessario continuare a usare circuiti dedicati, sarà necessario contattare il team degli account Microsoft per informazioni su come ottenere l'autorizzazione per usare la community **Altri servizi online di Office 365 (12076:5100)** . Il Review Board gestito da MS Office verificherà se tali circuiti sono necessari e illustrerà le implicazioni di tecniche che ne seguiranno mantenendoli. Le sottoscrizioni non autorizzate che proveranno a creare filtri della route per Office 365 riceveranno un messaggio di errore.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph per scenari amministrativi relativi alle Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** esperienza di sviluppo

Sono state aggiunte API Microsoft Graph per l'amministrazione delle condizioni per l'utilizzo di Azure AD. È possibile creare, aggiornare ed eliminare l'oggetto condizioni per l'utilizzo.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Aggiunta di un endpoint multi-tenant di Azure AD come provider di identità in Azure AD B2C

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

Usando criteri personalizzati, è ora possibile aggiungere l'endpoint comune di Azure AD come provider di identità in Azure AD B2C. In questo modo, si otterrà un singolo punto di ingresso per tutti gli utenti di Azure AD che accedono alle applicazioni. Per altre informazioni, vedere [Azure Active Directory B2C: Consentire agli utenti di accedere a un provider di identità Azure AD multi-tenant tramite i criteri personalizzati](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Uso di URL interni per accedere alle app da qualsiasi posizione con l'estensione My Apps Sign-in e Azure AD Application Proxy

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** SSO

Gli utenti possono ora accedere alle applicazioni tramite URL interni quando si trovano all'esterno della rete aziendale usando l'estensione My Apps Secure Sign-in per Azure AD. Questa estensione funziona con tutte le applicazioni pubblicate tramite Azure AD Application Proxy su qualsiasi browser in cui è anche installata l'estensione del browser per il pannello di accesso. La funzionalità di reindirizzamento degli URL viene automaticamente abilitata quando un utente accede all'estensione. L'estensione è disponibile per il download in [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Dati in Europa per i clienti europei

**Tipo:** Nuova funzionalità **Categoria di servizio:** Altro **Funzionalità del prodotto:** GoLocal

I dati dei clienti in Europa devono restare in Europa e non possono essere replicati al di fuori di data center europei ai fini della conformità alle leggi europee e sulla privacy. Questo [articolo](./active-directory-data-storage-eu.md) include i dettagli specifici su quali informazioni sulle identità verranno archiviate in Europa e fornisce anche informazioni sui dati che verranno archiviati al di fuori dei data center europei.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Maggio 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Integrazione con app di terze parti

Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A maggio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md).

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Le verifiche di accesso per l'accesso di app e gruppi in Azure AD includono ora controlli ricorrenti

**Tipo:** Nuova funzionalità **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Governance

La verifica di accesso di gruppi e app è in genere disponibile come parte di Azure AD Premium P2.  Gli amministratori potranno configurare le verifiche di accesso delle appartenenze ai gruppi e le assegnazioni delle applicazioni in modo che ricorrano a intervalli regolari, ad esempio ogni mese o ogni trimestre.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Log attività di Azure AD (accessi e controllo) ora disponibili tramite Microsoft Graph

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD, che includono log degli accessi e di controllo, sono ora disponibili tramite l'API Microsoft Graph. Sono stati esposti due endpoint tramite l'API Microsoft Graph per permettere l'accesso a questi log. Per iniziare, fare riferimento ai [documenti](../reports-monitoring/concept-reporting-api.md) per l'accesso a livello di codice alle API di creazione report di Azure AD.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Miglioramenti all'esperienza di riscatto B2B e di uscita da un'organizzazione

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C

**Riscatto JIT:** dopo aver condiviso una risorsa con un utente guest tramite l'API B2B, non è necessario inviare un messaggio di posta elettronica di invito speciale. Nella maggior parte dei casi l'utente guest può accedere alla risorsa per poi essere guidato nell'esperienza di riscatto JIT. Di conseguenza, non vi è alcun impatto a causa di messaggi di posta elettronica non ricevuti. Inoltre, non è più necessario chiedere agli utenti guest se hanno fatto clic sul collegamento per la procedura di riscatto inviato dal sistema. Questo significa che quando l'azienda del provider di servizi usa la gestione degli inviti, i file cloud allegati possono avere lo stesso URL canonico per tutti gli utenti, interni ed esterni, in qualsiasi stato di riscatto.

**Esperienza di riscatto moderna:** non è più necessario usare una pagina di destinazione per il riscatto con schermata divisa. Gli utenti vedranno un'esperienza di consenso moderna, che include l'informativa sulla privacy dell'organizzazione che ha emesso l'invito, proprio come avviene con le app di terze parti.

**Gli utenti guest possono lasciare l'organizzazione:** al termine della relazione di un utente con l'organizzazione, l'utente può gestire in modo autonomo l'uscita dall'organizzazione. L'utente non deve più chiamare l'amministratore dell'organizzazione che ha emesso l'invito per chiedere di essere rimosso né è più necessario generare un ticket di supporto.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Maggio 2018

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di maggio 2018 sono state aggiunte queste 18 nuove app con il supporto della federazione alla raccolta di app:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), [Montage Online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nuove guide dettagliate alla distribuzione per Azure Active Directory

**Tipo:** Nuova funzionalità **Categoria di servizio:** Altro **Funzionalità del prodotto:** Directory

Nuove indicazioni dettagliate su come distribuire Azure Active Directory (Azure AD), tra cui la reimpostazione della password self-service (SSPR), l'accesso Single Sign-On (SSO), l'accesso condizionale, il proxy app, il provisioning utenti, Active Directory Federation Services (ADFS) per l'autenticazione pass-through (PTA) e la sincronizzazione dell'hash da ADFS a password (PHS).

Per visualizzare le guide alla distribuzione, visitare il repository [Identity Deployment Guides](./active-directory-deployment-plans.md) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Ricerca di applicazioni aziendali - Caricamento di altre app

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Per risolvere i problemi di individuazione di applicazioni/entità di servizio, è stata aggiunta la possibilità di caricare altre applicazioni nell'elenco di tutte le applicazioni aziendali. Per impostazione predefinita, vengono visualizzate 20 applicazioni. È ora possibile fare clic su **Carica altro** per visualizzare applicazioni aggiuntive.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi.

**Tipo:** Funzionalità modificata **Categoria di servizio:** AD Connect **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. Le note sulla versione sono disponibili [qui](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Verifiche di accesso di Azure AD: applicazione automatica

**Tipo:** Funzionalità modificata **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Governance

Le verifiche di accesso di gruppi e app sono in genere disponibili come parte di Azure AD Premium P2. Un amministratore può configurare l'ambiente per l'applicazione automatica delle modifiche da apportare al gruppo o all'app al termine delle verifiche di accesso. L'amministratore può anche specificare che cosa succederà all'accesso continuo dell'utente se i revisori non rispondono, rimuovere l'accesso, mantenere l'accesso o eseguire azioni consigliate relative al sistema.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>I token ID non possono più essere restituiti tramite response_mode query per le nuove app.

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Le app create a partire dal 25 aprile 2018 non potranno più richiedere **id_token** tramite il response_mode **query**.  Questa novità allinea Azure AD alle specifiche OIDC e contribuisce a ridurre la superficie di attacco delle app.  Alle app create prima del 25 aprile 2018 non viene impedito di usare il response_mode **query** con response_type **id_token**.  L'errore restituito, quando si richiede un id_token da Azure AD, è **AADSTS70007: 'query' non** è un valore supportato di 'response_mode' quando si richiede un token .

I valori di response_mode **fragment** e **form_post** continuano a funzionare quando si creano nuovi oggetti applicazione, ad esempio per l'utilizzo del proxy di app. Assicurarsi di usare uno dei due prima di creare una nuova applicazione.

---

## <a name="april-2018"></a>Aprile 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>I token di accesso di Azure AD B2C sono disponibili a livello generale

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È ora possibile accedere all'API Web protetta da Azure Active Directory B2C tramite i token di accesso. La funzionalità passerà dall'anteprima pubblica alla disponibilità generale. L'esperienza dell'interfaccia utente per configurare applicazioni Azure Active Directory B2C e API Web è stata migliorata e sono stati apportati altri miglioramenti secondari.

Per altre informazioni, vedere [Azure AD B2C: richiesta di token di accesso](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Test della configurazione Single Sign-On per le applicazioni basate su SAML

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Quando si configurano applicazioni SSO basate su SAML, è possibile testare l'integrazione nella pagina di configurazione. Se si verifica un errore durante l'accesso, è possibile fornire l'errore nell'esperienza di test e Azure AD fornisce le procedure di risoluzione per risolvere il problema specifico.

Per altre informazioni, vedere:

- [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../manage-apps/view-applications-portal.md)
- [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Nella funzionalità Condizioni per l'utilizzo di Azure AD è ora disponibile la segnalazione per utente

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

Gli amministratori possono ora selezionare una determinata ToU e vedere tutti gli utenti che hanno acconsentito a tale ToU e la data/ora in cui è stata effettuata.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP a rischio per la protezione tramite blocco della Extranet di AD FS

**Tipo:** Nuova funzionalità **Categoria di servizio:** Altro **Funzionalità del prodotto:** Monitoraggio e creazione report

Connect Health ora supporta la capacità di rilevare gli indirizzi IP che superano una soglia di accessi U/P non riusciti su base oraria o giornaliera. Le funzionalità fornite da questa funzione sono:

- Report completo che mostra l'indirizzo IP e il numero di accessi non riusciti generati su base oraria/giornaliera con soglia personalizzabile.
- Avvisi tramite e-mail che indicano quando un indirizzo IP specifico ha superato la soglia di accessi U/P non riusciti su base oraria/giornaliera.
- Un'opzione di download per eseguire un'analisi dettagliata dei dati

Per altre informazioni, vedere [Report sugli indirizzi IP rischiosi](../hybrid/how-to-connect-health-adfs.md).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Configurazione semplificata dell'app con file di metadati o URL

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Nella pagina Applicazioni aziendali gli amministratori possono caricare un file di metadati SAML per configurare l'accesso basato su SAML per Azure AD Gallery e l'applicazione non della raccolta.

Inoltre, è possibile utilizzare l'URL dei metadati della federazione di applicazioni Azure AD per configurare SSO con l'applicazione di destinazione.

Per altre informazioni, vedere [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Funzionalità Condizioni per l'utilizzo di Azure AD ora disponibile a livello generale

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità


Le condizioni per l'utilizzo di Azure AD sono passate dalla versione di anteprima pubblica alla disponibilità a livello generale.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C


È ora possibile specificare con quali organizzazioni partner si desidera condividere e collaborare in Azure AD B2B Collaboration. Per fare questo, è possibile scegliere di creare un elenco di domini specifici di consenso o negazione. Quando un dominio viene bloccato utilizzando queste funzionalità, i collaboratori non possono più inviare inviti a persone che si trovano in quel dominio.

Ciò consente di controllare l'accesso alle risorse, consentendo nel contempo un'esperienza fluida agli utenti approvati.

Questa funzionalità di Collaborazione B2B è disponibile per tutti i clienti di Azure Active Directory e può essere usata in combinazione con le funzionalità di Azure AD Premium, come l'accesso condizionale e la protezione delle identità per un controllo più granulare su quando gli utenti esterni eseguono l'accesso e su come ottengono tale accesso.

Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di aprile 2018 sono state aggiunte queste 13 nuove app con il supporto della federazione alla raccolta di app:

Criterion HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Monitor prestazioni](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Possibilità di consentire agli utenti B2B di Azure AD l'accesso alle applicazioni locali (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2B **Funzionalità del prodotto:** B2B/B2C

Un'organizzazione che usa Collaborazione B2B di Azure Active Directory (Azure AD) per invitare gli utenti guest di organizzazioni partner in Azure AD può ora fornire a questi utenti B2B l'accesso alle app locali. Queste app locali possono usare l'autenticazione basata su SAML o l'autenticazione integrata di Windows con la delega vincolata Kerberos.

Per altre informazioni, consultare [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Esercitazioni per l'integrazione dell'accesso Single Sign-On disponibili in Azure Marketplace

**Tipo:** Funzionalità modificata **Categoria di servizio:** Altro **Funzionalità del prodotto:** Integrazione con app di terze parti

Se un'applicazione pubblicata in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) supporta l'accesso Single Sign-On basato su SAML, facendo clic su **Scarica adesso** si otterrà l'esercitazione di integrazione associata all'applicazione specifica.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Prestazioni più rapide del provisioning automatico degli utenti di Azure AD nelle applicazioni SaaS

**Tipo:** Funzionalità modificata **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Integrazione con app di terze parti

In precedenza, i clienti che usavano i connettori per il provisioning degli utenti di Azure Active Directory per applicazioni SaaS (ad esempio Salesforce, ServiceNow e Box) potevano sperimentare prestazioni lente se i loro tenant Azure AD contenevano oltre 100.000 utenti e gruppi combinati e usavano le assegnazioni degli utenti e dei gruppi per determinare per quali utenti doveva essere eseguito il provisioning.

Il 2 aprile 2018 il servizio di provisioning Azure AD è stato migliorato in modo significativo, riducendo notevolmente il tempo necessario per eseguire la sincronizzazione iniziale tra Azure Active Directory e le applicazioni SaaS di destinazione.

Di conseguenza, molti clienti con sincronizzazioni iniziali con le applicazioni che hanno richiesto molti giorni o che non sono mai state completate, ora possono completarle in pochi minuti o ore.

Per altre informazioni, vedere [Cosa accade durante il provisioning?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Reimpostazione della password self-service dalla schermata di blocco di Windows 10 per i computer aggiunti ad Azure AD ibrido

**Tipo:** Funzionalità modificata **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Autenticazione dell'utente

È stata aggiornata la funzionalità di Windows 10 SSPR per includere il supporto per i computer che sono parte di Azure AD ibrido. Questa funzionalità è disponibile in Windows 10 RS4 e consente agli utenti di reimpostare la password dalla schermata di blocco di un computer Windows 10. Gli utenti che sono abilitati e registrati per la reimpostazione self-service della password possono utilizzare questa funzionalità.

Per altre informazioni, consultare [Reimpostazione password self-service di Azure AD dalla schermata di accesso](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Marzo 2018

### <a name="certificate-expire-notification"></a>Notifica della scadenza dei certificati

**Tipo:** Fisso **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

Azure AD invia una notifica quando un certificato per un'applicazione inclusa o non inclusa nella raccolta sta per scadere.

Alcuni utenti non ricevevano notifiche per le applicazioni aziendali configurate per l'accesso Single Sign-On basato su SAML. Questo problema è stato risolto. Azure AD invia una notifica per i certificati in scadenza entro 7, 30 e 60 giorni. È possibile visualizzare questo evento nei log di controllo.

Per altre informazioni, vedere:

- [Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Report delle attività di controllo nel portale di Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provider di identità Twitter e GitHub in Azure AD B2C

**Tipo:** Nuova funzionalità **Categoria di servizio:** B2C - Consumer Identity Management **Funzionalità del prodotto:** B2B/B2C

È ora possibile aggiungere Twitter o GitHub come provider di identità in Azure AD B2C. Twitter passerà dall'anteprima pubblica alla disponibilità generale. GitHub verrà rilasciato in anteprima pubblica.

Per altre informazioni, vedere [Informazioni su Collaborazione B2B di Azure AD](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Limitare l'accesso al browser usando Intune Managed Browser con l'accesso condizionale basato su applicazioni di Azure AD per iOS e Android

**Tipo:** Nuova funzionalità **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

**Ora in anteprima pubblica.**

**SSO di Intune Managed Browser:** i dipendenti possono usare Single Sign-On nei client nativi (ad esempio, Microsoft Outlook) e in Intune Managed Browser per tutte le app connesse ad Azure AD.

**Supporto dell'accesso condizionale di Intune Managed Browser:** è ora possibile fare in modo che i dipendenti usino Intune Managed Browser con i criteri di accesso condizionale basato su applicazioni.

Per altre informazioni, vedere il [post di blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Per altre informazioni, vedere:

- [Configurare l'accesso condizionale basato sulle applicazioni](../conditional-access/app-based-conditional-access.md)

- [Configurare i criteri di Managed Browser](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlet di Application Proxy nel modulo di PowerShell disponibile a livello generale

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Controllo di accesso

Il supporto per i cmdlet di Application Proxy è ora incluso nel modulo di PowerShell disponibile a livello generale. Si noti che è necessario rimanere sempre aggiornati sui moduli di PowerShell. Se si rimane indietro di più di un anno, alcuni cmdlet potrebbero non funzionare più.

Per altre informazioni, vedere [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>I client nativi di Office 365 includono il supporto per l'accesso SSO facile tramite un protocollo non interattivo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Gli utenti dei client nativi di Office 365 (versione 16.0.8730.xxxx e successive) possono usufruire di un'esperienza di accesso non interattiva usando l'accesso SSO facile. Questo supporto viene fornito aggiungendo un protocollo non interattivo (WS-Trust) ad Azure AD.

Per altre informazioni, vedere [Funzionamento dell'accesso in un client nativo con Seamless SSO](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work).

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>È disponibile un'esperienza di accesso non interattiva, con accesso SSO facile, se un'applicazione invia le richieste di accesso agli endpoint di tenant di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Gli utenti possono usufruire di un'esperienza di accesso non interattiva con Seamless SSO se un'applicazione, ad esempio `https://contoso.sharepoint.com`, invia le richieste di accesso agli endpoint tenant di Azure AD, ovvero `https://login.microsoftonline.com/contoso.com/<..>` o `https://login.microsoftonline.com/<tenant_ID>/<..>`, anziché all'endpoint comune di Azure AD (`https://login.microsoftonline.com/common/<...>`).

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Per implementare l'accesso SSO facile è necessario aggiungere un solo URL di Azure AD, anziché due URL come in precedenza, alle impostazioni dell'area Intranet degli utenti

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Per consentire agli utenti di usufruire dell'accesso SSO facile, è necessario aggiungere un solo URL di Azure AD alle impostazioni dell'area Intranet degli utenti usando Criteri di gruppo in Active Directory: `https://autologon.microsoftazuread-sso.com`. In precedenza era necessario aggiungere due URL.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di marzo 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant by FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM per Risorse di Azure è disponibile a livello generale

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Se si usa Azure AD Privileged Identity Management (PIM) per i ruoli della directory, è ora possibile eseguire le relative funzionalità di assegnazione e accesso con vincoli di tempo per i ruoli di Risorse di Azure, ad esempio Sottoscrizioni, Gruppi di risorse, Macchine virtuali e qualsiasi altra risorsa supportata da Azure Resource Manager. Applicare Multi-Factor Authentication quando si attivano i ruoli in modalità Just-In-Time e si pianificano le attivazioni in base agli intervalli di modifica approvati. Inoltre, questa versione introduce miglioramenti non disponibili nell'anteprima pubblica, tra cui un'interfaccia utente aggiornata, i flussi di lavoro di approvazione e la possibilità di estendere i ruoli prossimi alla scadenza e rinnovare quelli scaduti.

Per altre informazioni, vedere [PIM per Risorse di Azure (anteprima)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Aggiunta di attestazioni facoltative ai token delle app (anteprima pubblica)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

Un'app di Azure AD può ora richiedere attestazioni personalizzate o facoltative nei token JWT e SAML.  Si tratta di attestazioni relative all'utente o al tenant che non sono incluse per impostazione predefinita nel token a causa di vincoli di dimensione o applicabilità.  Questa funzionalità è attualmente in anteprima pubblica per le app di Azure AD negli endpoint 1.0 e 2.0.  Vedere la documentazione per informazioni su quali attestazioni possono essere aggiunte e su come modificare il manifesto dell'applicazione per richiederle.

Per altre informazioni, vedere [Attestazioni facoltative in Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD supporta PKCE per flussi OAuth più sicuri

**Tipo:** Nuova funzionalità **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

La documentazione di Azure AD è stata aggiornata in modo da mettere in evidenza il supporto per PKCE, che consente comunicazioni più sicure durante il flusso di concessione del codice di autorizzazione OAuth 2.0.  Negli endpoint 1.0 e 2.0 sono supportati entrambi i valori di code_challenge S256 e plaintext.

Per altre informazioni, vedere [Richiedere un codice di autorizzazione](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Supporto per il provisioning di tutti i valori degli attributi utente disponibili nell'API Get_Workers di Workday

**Tipo:** Nuova funzionalità **Categoria di servizio:** Provisioning dell'app **Funzionalità del prodotto:** Integrazione con app di terze parti

L'anteprima pubblica del provisioning in ingresso da Workday ad Active Directory e Azure AD offre ora la possibilità di estrarre tutti i valori degli attributi disponibili nell'API Get_Workers di Workday ed effettuarne il provisioning. Ciò include il supporto per centinaia di attributi standard e personalizzati, oltre a quelli forniti con la versione iniziale del connettore di provisioning in ingresso di Workday.

Per altre informazioni, vedere: [Personalizzazione dell'elenco di attributi utente di Workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Modifica dell'appartenenza ai gruppi da dinamica a statica e viceversa

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione gruppo **Funzionalità del prodotto:** Collaborazione

È possibile modificare il modo in cui viene gestita l'appartenenza in un gruppo. Ciò è utile per mantenere lo stesso ID e nome di gruppo nel sistema in modo che i riferimenti al gruppo esistenti restino validi. Creando un nuovo gruppo sarebbe infatti necessario aggiornare tali riferimenti.
L'interfaccia di amministrazione di Azure AD è stata aggiornata per il supporto di questa funzionalità. I clienti possono ora convertire i gruppi esistenti dall'appartenenza dinamica a quella assegnata e viceversa. Sono ancora disponibili anche i cmdlet di PowerShell esistenti.

Per altre informazioni, vedere [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Miglioramento del comportamento di disconnessione con accesso SSO facile

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione dell'utente

In precedenza, anche se disconnessi in modo esplicito da un'applicazione protetta da Azure AD, gli utenti sarebbero stati riconnessi automaticamente tramite l'accesso SSO facile se avessero provato ad accedere nuovamente a un'applicazione di AD Azure all'interno della rete aziendale dai propri dispositivi aggiunti a un dominio. Con questa modifica, la disconnessione è supportata.  Gli utenti possono pertanto scegliere lo stesso o un altro account di Azure AD per eseguire nuovamente l'accesso anziché essere riconnessi automaticamente tramite l'accesso SSO facile.

Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="application-proxy-connector-version-154020-released"></a>Rilascio della versione 1.5.402.0 del connettore di Application Proxy

**Tipo:** Funzionalità modificata **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Questa versione del connettore è stata gradualmente implementata nel mese di novembre. La nuova versione include le modifiche seguenti:

- Il connettore imposta ora i cookie a livello di dominio anziché a livello di sottodominio. Ciò garantisce un'esperienza di accesso SSO più uniforme, evitando richieste di autenticazione ridondanti.
- È incluso il supporto per le richieste di codifica a blocchi.
- È stato migliorato il monitoraggio dell'integrità del connettore.
- Sono stati introdotti miglioramenti alla stabilità e correzioni di bug.

Per altre informazioni, vedere [Informazioni sui connettori di Azure AD Application Proxy](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Febbraio 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Esplorazione migliorata per la gestione di utenti e gruppi

**Tipo:** Modifica prevista **Categoria di servizio:** Gestione directory **Funzionalità del prodotto:** Directory

L'esperienza di esplorazione per la gestione di utenti e gruppi è stata semplificata. Ora è possibile passare dalla panoramica della directory direttamente all'elenco di tutti gli utenti, con un più facile accesso all'elenco degli utenti eliminati. Si può anche passare dalla panoramica della directory direttamente all'elenco di tutti i gruppi, con un più facile accesso alle impostazioni di gestione dei gruppi. Inoltre, dalla pagina di panoramica della directory è possibile cercare un utente, un gruppo, un'applicazione aziendale o una registrazione di app.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilità dei report di controllo e delle attività di accesso in Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina)

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure Stack **Funzionalità del prodotto:** Monitoraggio e creazione report

I report dei log attività di Azure AD sono ora disponibili nelle istanze di Microsoft Azure gestito da 21Vianet (21Vianet per Azure Cina). Sono inclusi i log seguenti:

- **Log di attività di accesso**: includono tutti i log sugli accessi associati al tenant.

- **Log di controllo delle password self-service**: includono tutti i log di controllo relativi alla reimpostazione password self-service.

- **Log di controllo della gestione directory**: includono tutti i log di controllo correlati alla gestione delle directory, come Gestione utenti, Gestione app e altri.

Questi log permettono di ottenere informazioni approfondite sullo stato dell'ambiente. I dati forniti consentono di:

- Determinare le modalità di utilizzo di app e servizi da parte degli utenti.

- Risolvere problemi che influiscono negativamente sulla produttività degli utenti.

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usare il ruolo "Lettore report" (ruolo non amministrativo) per visualizzare i report attività di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Monitoraggio e creazione report

In seguito alla richiesta degli utenti di consentire ai ruoli non amministrativi di accedere ai log attività di Azure AD, ora gli utenti con ruolo "Lettore report" possono accedere ai log attività di accesso e controllo nel portale di Azure o tramite l'API Microsoft Graph.

Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Attestazione EmployeeID disponibile come attributo utente e identificatore utente

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** SSO

È possibile configurare **EmployeeID** come ID utente e attributo utente per gli utenti membri e i guest B2B nelle applicazioni con accesso basato su SAML dall'interfaccia utente delle applicazioni aziendali.

Per altre informazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestione semplificata delle applicazioni mediante caratteri jolly in Azure AD Application Proxy

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Autenticazione dell'utente

Per facilitare la distribuzione delle applicazioni e ridurre il sovraccarico amministrativo, ora è possibile pubblicare le applicazioni usando caratteri jolly. Per pubblicare un'applicazione con caratteri jolly, è possibile seguire il flusso di pubblicazione standard delle applicazioni ma usare un carattere jolly negli URL interni ed esterni.

Per altre informazioni, vedere [Applicazioni con carattere jolly in Azure Active Directory Application Proxy](../manage-apps/application-proxy-wildcard.md).

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nuovi cmdlet a supporto della configurazione del proxy di applicazione

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Piattaforma

L'ultima versione del modulo di anteprima Azure AD PowerShell contiene nuovi cmdlet che consentono agli utenti di configurare applicazioni proxy di applicazione mediante PowerShell.

I nuovi cmdlet sono:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nuovi cmdlet a supporto della configurazione di gruppi

**Tipo:** Nuova funzionalità **Categoria di servizio:** Proxy app **Funzionalità del prodotto:** Piattaforma

L'ultima versione del modulo Azure AD PowerShell contiene cmdlet per la gestione dei gruppi in Azure AD. Questi cmdlet erano in precedenza disponibili nel modulo AzureADPreview e ora sono stati aggiunti al modulo AzureAD.

I cmdlet di gestione dei gruppi con disponibilità generale sono:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Disponibilità di una nuova versione di Azure AD Connect

**Tipo:** Nuova funzionalità **Categoria di servizio:** AD Sync **Funzionalità del prodotto:** Piattaforma

Azure AD Connect è lo strumento preferito per sincronizzare i dati tra Azure AD e le origini dati locali, inclusi Windows Server Active Directory e LDAP.

>[!Important]
>Questa build introduce modifiche alle regole dello schema e di sincronizzazione. Il servizio di sincronizzazione Azure AD Connect attiva i passaggi di importazione e sincronizzazione complete dopo un aggiornamento. Per informazioni su come modificare questo comportamento, vedere [Come rinviare la sincronizzazione completa dopo l'aggiornamento](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Questa versione contiene gli aggiornamenti e le modifiche seguenti:

**Problemi risolti**

- Correzione della finestra temporale nelle attività in background per la pagina Filtro partizioni nel passaggio alla pagina successiva.

- Correzione di un bug che provocava la violazione di accesso durante l'azione personalizzata ConfigDB.

- Correzione di un bug per il ripristino dal timeout della connessione SQL.

- Correzione di un bug in cui i certificati con caratteri jolly SAN non superavano la verifica preliminare.

- Correzione di un bug che causa un miiserver.exe durante l Azure AD esporta del connettore.

- Correzione di un bug a causa del quale un tentativo di accesso con password non valida al controller di dominio durante l'esecuzione causava la modifica della configurazione della Azure AD di connessione guidata di Azure AD

**Miglioramenti e nuove funzionalità**

- Dati di telemetria dell'applicazione: gli amministratori possono attivare o disattivare questa classe di dati.

- Dati di integrità di Azure AD: gli amministratori devono visitare il portale dei dati di integrità per gestire le impostazioni di integrità. Dopo aver modificato i criteri del servizio, gli agenti li leggeranno e applicheranno.

- Aggiunta di azioni di configurazione per il writeback dei dispositivi e indicatore di stato per l'inizializzazione della pagina.

- Miglioramento della diagnostica generale con report HTML e raccolta completa di dati in un report in formato ZIP di testo/HTML.

- Migliore affidabilità dell'aggiornamento automatico e aggiunta di ulteriori dati di telemetria per assicurare che sia possibile determinare l'integrità del server.

- Limitazione delle autorizzazioni disponibili per gli account con privilegi sull'account AD Connect. Per le nuove installazioni, la procedura guidata limita le autorizzazioni degli account con privilegi sull'account MSOL dopo la creazione dell'account MSOL stesso. Le modifiche interessano le installazioni rapide e le installazioni personalizzate con account a creazione automatica.

- Modifica del programma di installazione in modo da non richiedere il privilegio SA per l'installazione pulita di Azure AD Connect.

- Nuova utilità per risolvere i problemi di sincronizzazione per un oggetto specifico. L'utilità esegue attualmente le verifiche seguenti:

    - Mancata corrispondenza di UserPrincipalName tra l'oggetto utente sincronizzato e l'account utente nel tenant Azure AD.

    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri di dominio

    - Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri dell'unità organizzativa

- Nuova utilità per la sincronizzazione dell'hash delle password corrente archiviato nell'istanza locale di Active Directory per un account utente specifico. L'utilità non richiede la modifica delle password.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aggiunta di applicazioni che supportano i criteri Protezione app di Intune per l'uso con l'accesso condizionale basato sull'applicazione di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Sono state aggiunte altre applicazioni che supportano l'accesso condizionale basato sull'applicazione. Ora è possibile accedere a Office 365 e ad altre app cloud connesse ad Azure AD usando queste app client approvate.

Le applicazioni seguenti verranno aggiunte entro le fine di febbraio:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Per altre informazioni, vedere:

- [Requisiti per le app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Aggiornamento delle condizioni per l'utilizzo per l'esperienza per i dispositivi mobili

**Tipo:** Funzionalità modificata **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

Quando vengono visualizzate le Condizioni per l'utilizzo, ora è possibile fare clic su **In caso di problemi di visualizzazione, fare clic qui**. Questo collegamento consente di aprire le Condizioni per l'utilizzo in modo nativo sul dispositivo. Indipendentemente dalle dimensioni del carattere del documento o dalle dimensioni dello schermo del dispositivo, è possibile ingrandire la visualizzazione finché non si riesce a leggere il documento.

---

## <a name="january-2018"></a>Gennaio 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel mese di gennaio 2018 sono state aggiunte le nuove app seguenti con il supporto della federazione alla raccolta di app:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [OneTrust Privacy Management Software](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), directory federata IriusRisk e [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Rilevato accesso con rischi aggiuntivi

**Tipo:** Nuova funzionalità **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Protezione e sicurezza delle identità

Le informazioni ottenute per un rilevamento di rischi sono associate alla sottoscrizione di Azure AD in corso. Con l'edizione Azure AD Premium P2, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

Con l'edizione Azure AD Premium P1, i rilevamenti non previsti dalla licenza in uso vengono visualizzati come rilevamento di rischi di accesso con un rischio aggiuntivo rilevato.

Per altre informazioni, vedere [Rilevamenti dei rischi in Azure Active Directory](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Possibilità di nascondere le applicazioni di Office 365 dai pannelli di accesso dell'utente finale

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** SSO

Ora è possibile gestire in maniera più efficiente la modalità di visualizzazione delle applicazioni di Office 365 nei pannelli di accesso dell'utente tramite una nuova impostazione utente. Questa opzione è utile per ridurre il numero di app nei pannelli di accesso di un utente se si preferisce visualizzare solo le app di Office nel portale di Office. L'impostazione è disponibile in **Impostazioni utente** ed è denominata **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.

Per altre informazioni, vedere [Nascondere un'applicazione dall'esperienza utente in Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Accesso trasparente alle app abilitato per l'accesso SSO basato su password direttamente dall'URL dell'app

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** SSO

L'estensione del browser App personali è ora disponibile tramite un utile strumento che mette a disposizione la funzionalità Single-Sign On come collegamento nel browser. Dopo l'installazione, nel browser comparirà un'icona di avvio delle app che assicurerà un accesso rapido alle app. Gli utenti possono ora sfruttare i vantaggi seguenti:

- Possibilità di accedere direttamente ad app con accesso SSO basato su password dalla pagina di accesso dell'app
- Avvio delle app tramite la funzionalità di ricerca rapida
- Collegamenti alle app usate di recente dall'estensione
- L'estensione è disponibile per il download in Microsoft Edge, Chrome e Firefox.

Per altre informazioni, vedere [Estensione per l'accesso sicuro alle app personali](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Esperienza di amministrazione di Azure AD nel portale di Azure classico ritirata

**Tipo:** Deprecato **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Directory

A partire dall'8 gennaio 2018, l'esperienza di amministrazione di Azure AD nel portale di Azure classico è stata ritirata. Questo ritiro ha avuto luogo in concomitanza con quello del portale di Azure classico. In futuro è consigliabile usare il [centro di amministrazione di Azure AD](https://aad.portal.azure.com) per tutte le operazioni di amministrazione basate sul portale di Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Il portale Web di PhoneFactor è stato ritirato

**Tipo:** Deprecato **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Directory

A partire dall'8 gennaio 2018, il portale Web di PhoneFactor è stato ritirato. Questo portale è stato usato per l'amministrazione del server MFA, ma tali funzionalità sono state spostate nel portale di Azure all'indirizzo portal.azure.com.

La configurazione di MFA è accessibile da **Azure Active Directory \> MFA Server**.

---

### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Deprecato **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità


Considerando la disponibilità generale della nuova console di amministrazione di Azure Active Directory e delle nuove API ora disponibili per i report sulle attività e sulla sicurezza, le API di report nell'endpoint "/reports" sono state ritirate a partire dal 31 dicembre 2017.

**Elementi disponibili**

Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa ai rilevamenti di rischi di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.

Per altre informazioni, vedere:

- [Get started with the Azure Active Directory reporting API](../reports-monitoring/concept-reporting-api.md) (Introduzione all'API di creazione report di Azure Active Directory)

- [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Dicembre 2017

### <a name="terms-of-use-in-the-access-panel"></a>Condizioni per l'utilizzo nel riquadro di accesso

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

È ora possibile passare al riquadro di accesso e visualizzare le condizioni per l'utilizzo che sono state precedentemente accettate.

A tale scopo, seguire questa procedura:

1. Aprire il [portale App personali](https://myapps.microsoft.com) ed eseguire l'accesso.

2. Nell'angolo in alto a destra selezionare il nome e quindi scegliere **Profilo** dall'elenco.

3. In **Profilo** selezionare **Verificare le condizioni d'uso**.

4. È ora possibile verificare le condizioni per l'utilizzo accettate.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Autenticazione utente

Azure AD e le interfacce utente del sistema di identità dell'account Microsoft sono stati riprogettati in modo da avere un aspetto uniforme e coerente. Inoltre, nella pagina di accesso di Azure AD viene visualizzato prima di tutto il nome utente, seguito dalle credenziali in una seconda schermata.

Per altre informazioni, vedere [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (La nuova esperienza di accesso di AD Azure è ora in anteprima pubblica).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Meno richieste di accesso: nuova esperienza di tipo "Mantieni l'accesso" per l'accesso ad Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Autenticazione utente

La casella di controllo **Mantieni l'accesso** nella pagina di accesso di Azure AD è stata sostituita con un nuovo prompt che viene visualizzato dopo la corretta esecuzione dell'autenticazione.

Se risponde **Sì** a questo prompt, il servizio offre un token di aggiornamento permanente. Questo comportamento è analogo a quello che succedeva selezionando la casella di controllo **Mantieni l'accesso** nell'esperienza precedente. Per i tenant federati, questo prompt viene visualizzato dopo la corretta autenticazione con il servizio federato.

Per altre informazioni, vedere [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Meno prompt di accesso: la nuova esperienza di tipo "Mantieni l'accesso" per Azure AD è in anteprima).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Aggiunta configurazione per richiedere l'espansione delle condizioni per l'utilizzo prima dell'accettazione

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

Un'opzione per gli amministratori richiede che gli utenti espandano le condizioni per l'utilizzo prima di accettarle.

Selezionare **Sì** o **No** per richiedere che gli utenti espandano le condizioni per l'utilizzo. L'impostazione **Sì** richiede che gli utenti visualizzino le condizioni per l'utilizzo prima di accettarle.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonee

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

È possibile usare l'attivazione con ambito per attivare le assegnazioni di ruolo delle risorse di Azure idonee con meno autonomia rispetto alle impostazioni predefinite dell'assegnazione originale. Un esempio è se all'utente viene assegnato il ruolo di proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare il ruolo di proprietario per un massimo di cinque risorse contenute all'interno della sottoscrizione (ad esempio gruppi di risorse e macchine virtuali). La definizione dell'ambito per l'attivazione potrebbe ridurre la possibilità di apportare modifiche indesiderate alle risorse di Azure critiche.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuove app federate nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** App aziendali **Funzionalità del prodotto:** Integrazione con app di terze parti

Nel dicembre 2017 sono state aggiunte queste nuove app con il supporto della federazione alla raccolta di app:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [integrazione di Azure AD con MobileIron](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO for Bamboo di resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO for Bitbucket di resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, integrazione di Azure AD con Zenegy.

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Il flusso di lavoro di approvazione per i ruoli della directory di Azure AD è disponibile a livello generale.

Con il flusso di lavoro di approvazione, gli amministratori dei ruoli con privilegi possono fare in modo che i membri con ruolo idoneo richiedano l'attivazione del ruolo prima di poter usare il ruolo con privilegi. A più utenti e gruppi possono essere delegate responsabilità di approvazione. I membri con ruolo idoneo ricevono una notifica quando l'approvazione è completata e il ruolo è attivo.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticazione pass-through: supporto per Skype for Business

**Tipo:** Funzionalità modificata **Categoria di servizio:** Autenticazioni (accessi) **Funzionalità del prodotto:** Autenticazione utente

L'autenticazione pass-through supporta ora l'accesso degli utenti alle applicazioni client Skype for Business che supportano l'autenticazione moderna, che include topologie online e ibride.

Per altre informazioni, vedere [Skype for Business topologies supported with modern authentication](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported) (Topologie di Skype for Business supportate con l'autenticazione moderna).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aggiornamenti di Azure AD Privileged Identity Management per Controllo degli accessi in base al ruolo di Azure (anteprima)

**Tipo:** Funzionalità modificata **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Con l'aggiornamento dell'anteprima pubblica di Azure AD Privileged Identity Management (PIM) per il controllo degli accessi in base al ruolo di Azure, è ora possibile:

* Usare Just Enough Administration.
* Richiedere l'approvazione per l'attivazione dei ruoli delle risorse.
* Pianificare un'attivazione futura di un ruolo che richiede l'approvazione Azure AD ruoli di Azure.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources (preview)](../privileged-identity-management/azure-pim-resource-rbac.md) (Privileged Identity Management per risorse di Azure (anteprima)).

---

## <a name="november-2017"></a>Novembre 2017

### <a name="access-control-service-retirement"></a>Ritiro del Servizio di controllo di accesso

**Tipo:** Modifica prevista **Categoria di servizio:** Servizio di controllo di accesso **Funzionalità del prodotto:** Servizio di controllo di accesso

Controllo di accesso di Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato alla fine del 2018. Altre informazioni, tra cui una pianificazione dettagliata e istruzioni di carattere generale sulla migrazione, saranno disponibili nelle prossime settimane. È possibile lasciare un commento in questa pagina con eventuali domande sul Servizio di controllo di accesso. Un membro del team sarà a disposizione per rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitazione dell'accesso del browser a Intune Managed Browser

**Tipo:** Modifica prevista **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

È possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata.

È ora possibile configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita.

Cercare questa funzionalità e altre informazioni nelle note sulla versione e nei blog che verranno pubblicati a breve.

Per altre informazioni, vedere [Accesso condizionale in Azure AD](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

**Tipo:** Modifica prevista **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Per altre informazioni, vedere:

- [Requisiti per le app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'utilizzo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

Gli amministratori possono ora creare nuove condizioni per l'utilizzo contenenti più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.

---

### <a name="real-time-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale

**Tipo:** Nuova funzionalità **Categoria di servizio:** Reimpostazione password self-service **Funzionalità del prodotto:** Autenticazione utente

È ora possibile esaminare lo stato del client relativamente al writeback delle password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina [Reimpostazione password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset).

Se ci sono problemi con la connessione al client di writeback locale, viene visualizzato un messaggio di errore che specifica:

- Informazioni sui motivi per cui non è possibile connettersi al client di writeback locale.
- un collegamento alla documentazione di supporto alla risoluzione del problema.

Per altre informazioni, vedere [Integrazione locale](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Accesso condizionale basato su app di Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Azure AD **Funzionalità del prodotto:** Sicurezza e protezione delle identità

È ora possibile limitare l'accesso a Office 365 e ad altre app cloud connesse ad Azure AD alle [app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps) che supportano i criteri di protezione app di Intune tramite l'[accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md). I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accesso condizionale [basato su app](../conditional-access/app-based-conditional-access.md) con quelli di accesso condizionale [basato su dispositivo](../conditional-access/require-managed-devices.md), si ottiene la flessibilità necessaria per proteggere i dati dei dispositivi personali e aziendali.

I controlli e le condizioni seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su app:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedere app client approvata

Per altre informazioni, vedere [Accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gestione di dispositivi di Azure AD nel portale di Azure

**Tipo:** Nuova funzionalità **Categoria di servizio:** Registrazione e gestione dei dispositivi **Funzionalità del prodotto:** Sicurezza e protezione delle identità

È ora possibile individuare tutti i dispositivi connessi ad Azure AD e le attività correlate al dispositivo in un'unica posizione. È disponibile una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità dei dispositivi nel portale di Azure. In questa versione è possibile:

- Visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure AD.
- Visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure AD ibridi.
- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure AD, gestire il dispositivo con Intune e altro ancora.
- Gestire le impostazioni relative al dispositivo di Azure AD.

Per altre informazioni, vedere [Manage devices by using the Azure portal](../devices/device-management-azure-portal.md) (Gestire dispositivi tramite il portale di Azure).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma del dispositivo per l'accesso condizionale ad Azure AD

**Tipo:** Nuova funzionalità **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

È ora possibile includere o escludere macOS come condizione per la piattaforma del dispositivo nei criteri di accesso condizionale di Azure AD. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune.** Analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. È possibile usare la nuova app del portale aziendale per macOS per registrare un dispositivo con Intune e registrarlo con Azure AD.
- **Assicurarsi che i dispositivi macOS siano conformi ai criteri di conformità dell'organizzazione definiti in Intune.** Con Intune nel portale di Azure è ora possibile impostare criteri di conformità per i dispositivi macOS.
- **Limitare l'accesso alle applicazioni in Azure AD solo ai dispositivi macOS conformi.** La creazione di criteri di accesso condizionale prevede macOS come opzione di piattaforma del dispositivo distinta. È ora possibile creare criteri di accesso condizionale specifici per macOS per il set di applicazione di destinazione in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Accesso condizionale in Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Estensione server dei criteri di rete per Azure AD Multi-Factor Authentication

**Tipo:** Nuova funzionalità **Categoria di servizio:**  Multi-Factor Authentication **Funzionalità del prodotto:** Autenticazione utente

L'estensione Server dei criteri di rete per Azure AD Multi-Factor Authentication aggiunge funzionalità di Multi-Factor Authentication basate sul cloud all'infrastruttura di autenticazione usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente. Pertanto, non è necessario installare, configurare e gestire nuovi server.

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni della rete privata virtuale senza distribuire il server Azure Multi-Factor Authentication. L'estensione Server dei criteri di rete funge da scheda tra RADIUS e l'autenticazione a più fattori Azure AD basata sul cloud per fornire un secondo fattore di autenticazione per gli utenti federati o sincronizzati.

Per altre informazioni, vedere Integrare l'infrastruttura di Server dei criteri di rete [esistente con Azure AD Multi-Factor Authentication.](../authentication/howto-mfa-nps-extension.md)

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati

**Tipo:** Nuova funzionalità **Categoria di servizio:** Gestione utenti **Funzionalità del prodotto:** Directory

Nel centro di amministrazione di Azure Active Directory è possibile:

- Ripristinare un utente eliminato.
- Eliminare definitivamente un utente.

**Per provare il servizio:**

1. Nel centro di amministrazione di Azure AD selezionare [Tutti gli utenti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) nella sezione **Gestisci**.

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**.

3. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

**Tipo:** Funzionalità modificata **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono state aggiunte all'elenco di [app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft Planner
- Azure Information Protection

Per altre informazioni, vedere:

- [Requisiti per le app client approvate](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Accesso condizionale basato su app di Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usare "OR" tra i controlli in un criterio di accesso condizionale

**Tipo:** Funzionalità modificata **Categoria di servizio:** Accesso condizionale **Funzionalità del prodotto:** Sicurezza e protezione delle identità

È ora possibile usare "OR", ovvero richiedere uno dei controlli selezionati, per i controlli di accesso condizionale. È possibile usare questa funzionalità per creare criteri usando l'operatore "OR" tra i controlli di accesso. È ad esempio possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando Multi-Factor Authentication o in alternativa di usare un dispositivo conforme.

Per altre informazioni, vedere [Controlli nell'accesso condizionale di Azure AD](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregazione di rilevamenti di rischi in tempo reale

**Tipo:** Funzionalità modificata **Categoria di servizio:** Identity Protection **Funzionalità del prodotto:** Sicurezza e protezione delle identità

In Azure AD Identity Protection tutti i rilevamenti di rischi in tempo reale creati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ciascun tipo di rilevamento di rischi. Questa modifica consente di limitare il volume dei rilevamenti di rischi mostrati senza modificare la protezione dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si impostano criteri di sicurezza relativi al rischio di accesso per Multi-Factor Authentication o il blocco dell'accesso, verranno attivati per ogni accesso rischioso.

---

## <a name="october-2017"></a>Ottobre 2017

### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati

**Tipo:** Modifica prevista **Categoria di servizio:** Creazione di report **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

Il portale di Azure offre:

- Una nuova console di amministrazione di Azure AD.
- Nuove API per i report sulle attività e sulla sicurezza.

Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint /reports verranno ritirate il 10 dicembre 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso

**Tipo:** Fisso **Categoria di servizio:** App personali **Funzionalità del prodotto:** Single sign-on

Azure AD supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password. Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](https://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettere un URL Web e quindi salvare la pagina.

A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-multi-factor-authentication-features"></a>Nuove funzionalità di Multi-Factor Authentication

**Tipo:** Nuova funzionalità **Categoria di servizio:** Multi-Factor Authentication **Funzionalità del prodotto:** Sicurezza e protezione delle identità

Multi-Factor Authentication (MFA) è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti:

- I risultati della richiesta a più fattori sono integrati direttamente nel report di accesso di Azure AD, che include l'accesso a livello di codice ai risultati dell'autenticazione a più fattori.
- La configurazione di MFA è integrata in modo più approfondito nell'esperienza di configurazione di Azure AD nel portale di Azure.

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. È ora possibile gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure AD.

Per altri dettagli, vedere [Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità **Categoria di servizio:** Condizioni per l'utilizzo **Funzionalità del prodotto:** Conformità

È possibile usare le condizioni per l'utilizzo di Azure AD per presentare agli utenti informazioni quali dichiarazioni di non responsabilità pertinenti per requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione
- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici)
- Condizioni per l'utilizzo specifiche per l'accesso ad app aziendali a impatto elevato, come Salesforce

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management

**Tipo:** Nuova funzionalità **Categoria di servizio:** Privileged Identity Management **Funzionalità del prodotto:** Privileged Identity Management

Con Azure AD Privileged Identity Management, è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure (anteprima) all'interno dell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- Macchine virtuali

Tutte le risorse nel portale di Azure che usano la funzionalità Controllo degli accessi in base al ruolo di Azure possono trarre vantaggio da tutte funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD Privileged Identity Management.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources](../privileged-identity-management/azure-pim-resource-rbac.md) (Privileged Identity Management per risorse di Azure).

---

### <a name="access-reviews"></a>Verifiche di accesso

**Tipo:** Nuova funzionalità **Categoria di servizio:** Verifiche di accesso **Funzionalità del prodotto:** Conformità

Le organizzazioni possono usare le verifiche di accesso (anteprima) per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali:

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso per i rispettivi accessi alle applicazioni e le appartenenze ai gruppi. I revisori possono decidere in modo efficiente se consentire ai guest l'accesso continuo sulla base delle informazioni dettagliate fornite dalle verifiche di accesso.
- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e delle appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Possibilità di nascondere le applicazioni di terze parti dall'utilità di avvio delle app di Office 365 e di App personali

**Tipo:** Nuova funzionalità **Categoria di servizio:** App personali **Funzionalità del prodotto:** Single sign-on

È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. È possibile nascondere le app nei casi in cui vengano visualizzati riquadri delle app per i servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app degli utenti. È possibile abilitare o disabilitare questa proprietà nella sezione **Proprietà** dell'app di terze parti tramite l'opzione **Visibile agli utenti?** . È anche possibile nascondere un'app a livello di codice tramite PowerShell.

Per altre informazioni, vedere [Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa ai rilevamenti di rischi di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix per Identity Manager

**Tipo:** Funzionalità modificata **Categoria di servizio:** Identity Manager **Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Identity Manager 2016 Service Pack 1. Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti.
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di Identity Manager 2016 Service Pack 1 fino alla build 4.4.1459.0 per Identity Manager 2016.
- Richiede Identity Manager 2016 build 4.4.1302.0.

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---
