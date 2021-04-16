---
title: Azure Active Directory guida di riferimento alle operazioni di gestione dell'autenticazione
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da eseguire per proteggere la gestione dell'autenticazione
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531952"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory guida di riferimento alle operazioni di gestione dell'autenticazione

In questa sezione della guida di riferimento sulle operazioni di [Azure AD](active-directory-ops-guide-intro.md) vengono descritti i controlli e le azioni da eseguire per proteggere e gestire le credenziali, definire l'esperienza di autenticazione, delegare l'assegnazione, misurare l'utilizzo e definire i criteri di accesso in base alla sicurezza aziendale.

> [!NOTE]
> Queste raccomandazioni sono aggiornate alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di gestione delle identità con l'evolversi dei prodotti e dei servizi Microsoft nel tempo.

## <a name="key-operational-processes"></a>Processi operativi chiave

### <a name="assign-owners-to-key-tasks"></a>Assegnare proprietari alle attività chiave

La Azure Active Directory richiede l'esecuzione continua di processi e attività operative chiave, che potrebbero non far parte di un progetto di implementazione. È comunque importante configurare queste attività per ottimizzare l'ambiente. Le attività principali e i relativi proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Gestire il ciclo di vita della configurazione dell'accesso Single Sign-On (SSO) in Azure AD | Team operativo IAM |
| Progettare criteri di accesso condizionale per Azure AD applicazioni | Team dell'architettura di InfoSec |
| Archiviare l'attività di accesso in un sistema SIEM | Team operativo InfoSec |
| Archiviare gli eventi di rischio in un sistema SIEM | Team operativo InfoSec |
| Analisi e analisi dei report di sicurezza | Team operativo InfoSec |
| Valutazione e analisi degli eventi di rischio | Team operativo InfoSec |
| Analizzare e analizzare gli utenti contrassegnati per i report di rischio e vulnerabilità Azure AD Identity Protection | Team operativo InfoSec |

> [!NOTE]
> Azure AD Identity Protection richiede una licenza Azure AD Premium P2. Per trovare la licenza più valida per i requisiti, vedere Confronto delle funzionalità disponibili a livello generale delle [Azure AD Free e Azure AD Premium edizioni](https://azure.microsoft.com/pricing/details/active-directory/).

Quando si esamina l'elenco, potrebbe essere necessario assegnare un proprietario per le attività per le quali manca un proprietario o modificare la proprietà per le attività con proprietari non allineati alle raccomandazioni precedenti.

#### <a name="owner-recommended-reading"></a>Lettura consigliata dal proprietario

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Gestione di credenziali

### <a name="password-policies"></a>Criteri per la password

La gestione sicura delle password è una delle parti più importanti della gestione delle identità e degli accessi e spesso il principale obiettivo degli attacchi. Azure AD supporta diverse funzionalità che consentono di impedire la riuscita di un attacco.

Usare la tabella seguente per trovare la soluzione consigliata per attenuare il problema che deve essere risolto:

| Problema | Recommendation |
| :- | :- |
| Nessun meccanismo per la protezione da password deboli | Abilitare Azure AD [reimpostazione della password self-service (SSPR)](../authentication/concept-sspr-howitworks.md) e [la protezione delle password](../authentication/concept-password-ban-bad-on-premises.md) |
| Nessun meccanismo per rilevare le password perse | Abilitare la [sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) per ottenere informazioni dettagliate |
| Uso AD FS e non è possibile passare all'autenticazione gestita | Abilitare [AD FS blocco intelligente Extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e/o Azure AD blocco [intelligente](../authentication/howto-password-smart-lockout.md) |
| I criteri password utilizzano regole basate sulla complessità, ad esempio lunghezza, più set di caratteri o scadenza | Riconsiderare a favore di [Microsoft Recommended Practices](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) e cambiare l'approccio alla gestione delle password e distribuire Azure AD password di [protezione](../authentication/concept-password-ban-bad.md). |
| Gli utenti non sono registrati per l'uso dell'autenticazione a più fattori (MFA) | [Registrare tutte le informazioni di sicurezza dell'utente](../identity-protection/howto-identity-protection-configure-mfa-policy.md) in modo che possano essere usate come meccanismo per verificare l'identità dell'utente insieme alla password |
| Non esiste alcuna revoca delle password in base al rischio utente | Distribuire Azure AD [criteri di rischio utente](../identity-protection/howto-identity-protection-configure-risk-policies.md) di Identity Protection per forzare le modifiche delle password nelle credenziali perse usando la reimpostazione della password di accesso remoto |
| Non esiste un meccanismo di blocco intelligente per proteggere l'autenticazione dannosa da attori malintenzionati provenienti da indirizzi IP identificati | Distribuire l'autenticazione gestita dal cloud con la sincronizzazione dell'hash delle password o [l'autenticazione pass-through](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lettura consigliata dei criteri password

- [Azure AD e AD FS procedure consigliate: Difesa dagli attacchi con password spray - Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Abilitare la reimpostazione della password self-service e la protezione delle password

Gli utenti che devono modificare o reimpostare le password sono una delle principali fonti di volume e costo help desk chiamate. Oltre ai costi, la modifica della password come strumento per mitigare un rischio utente è un passaggio fondamentale per migliorare le condizioni di sicurezza dell'organizzazione.

Come minimo, è consigliabile distribuire la Azure AD reimpostazione della [password self-service](../authentication/concept-sspr-howitworks.md) (SSPR) e la protezione [della password](../authentication/howto-password-ban-bad-on-premises-deploy.md) locale per eseguire le seguenti attività:

- Deviare help desk chiamate.
- Sostituire l'uso di password temporanee.
- Sostituire qualsiasi soluzione di gestione delle password self-service esistente basata su una soluzione locale.
- [Eliminare le password deboli](../authentication/concept-password-ban-bad.md) nell'organizzazione.

> [!NOTE]
> Per le organizzazioni con una sottoscrizione Azure AD Premium P2, è consigliabile distribuire la SSPR e usarla come parte di un criterio di rischio utente [di Identity Protection.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="strong-credential-management"></a>Gestione delle credenziali sicuro

Le password di per sé non sono sufficientemente sicure da impedire agli attori malinteni di accedere all'ambiente. Come minimo, qualsiasi utente con un account con privilegi deve essere abilitato per l'autenticazione a più fattori (MFA). Idealmente, è consigliabile abilitare [la registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md) e richiedere a tutti gli utenti di registrarsi per MFA e SSPR usando l'esperienza di registrazione [combinata](../user-help/security-info-setup-signin.md). Infine, è consigliabile adottare una strategia [per](../authentication/concept-resilient-controls.md) fornire resilienza per ridurre il rischio di blocco a causa di circostanze impreviste.

![Flusso di esperienza utente combinato](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resilienza dell'autenticazione di interruzione locale

Oltre ai vantaggi della semplicità e dell'abilitazione del rilevamento delle credenziali perse, Azure AD Password Hash Sync (PHS) e Azure AD MFA consentono agli utenti di accedere alle applicazioni SaaS e Microsoft 365 nonostante le interruzioni locali a causa di attacchi informatici come [NotPetya.](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) È anche possibile abilitare PHS in combinazione con la federazione. L'abilitazione di PHS consente un fallback dell'autenticazione quando i servizi federativi non sono disponibili.

Se l'organizzazione locale non ha una strategia di resilienza per l'interruzione o ne ha una non integrata con Azure AD, è consigliabile distribuire Azure AD PHS e definire un piano di ripristino di emergenza che includa PHS. L'abilitazione Azure AD PHS consentirà agli utenti di eseguire l'autenticazione Azure AD se il Active Directory locale non è disponibile.

![flusso di sincronizzazione dell'hash delle password](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Per comprendere meglio le opzioni di autenticazione, vedere Scegliere il metodo di autenticazione appropriato per la soluzione di Azure Active Directory [di identità ibrida.](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Utilizzo a livello di codice delle credenziali

Azure AD script che usano PowerShell o applicazioni che usano l'API Microsoft Graph richiedono l'autenticazione sicura. Una gestione delle credenziali scadente che esegue tali script e strumenti aumenta il rischio di furto delle credenziali. Se si usano script o applicazioni che si basano su password hard-coded o richieste di password, è prima necessario esaminare le password nei [](../reports-monitoring/tutorial-access-api-with-certificates.md) file di configurazione o nel codice sorgente, quindi sostituire tali dipendenze e usare identità gestite di Azure, autenticazione Integrated-Windows o certificati quando possibile. Per le applicazioni in cui le soluzioni precedenti non sono possibili, è consigliabile usare [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Se si determina che sono presenti entità servizio con credenziali della password e non si è certi di come tali credenziali siano protette da script o applicazioni, contattare il proprietario dell'applicazione per comprendere meglio i modelli di utilizzo.

Microsoft consiglia inoltre di contattare i proprietari dell'applicazione per comprendere i modelli di utilizzo se sono presenti entità servizio con credenziali della password.

## <a name="authentication-experience"></a>Esperienza di autenticazione

### <a name="on-premises-authentication"></a>Autenticazione locale

L'autenticazione federata con autenticazione integrata di Windows (IWA) o l'autenticazione gestita SSO (Seamless Single Sign-On) con sincronizzazione dell'hash delle password o autenticazione pass-through è l'esperienza utente migliore quando si trova all'interno della rete aziendale con line-of-sight ai controller di dominio locali. Riduce al minimo l'affaticamento della richiesta di credenziali e riduce il rischio che gli utenti cadano in attacchi di phishing. Se si usa già l'autenticazione gestita dal cloud con PHS o PTA, ma gli utenti devono comunque digitare la password durante l'autenticazione locale, è necessario distribuire immediatamente [Seamless SSO.](../hybrid/how-to-connect-sso.md) D'altra parte, se si è attualmente federati con piani per la migrazione all'autenticazione gestita dal cloud, è necessario implementare l'accesso Single Sign-On facile come parte del progetto di migrazione.

### <a name="device-trust-access-policies"></a>Criteri di accesso attendibilità dei dispositivi

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. L'autenticazione del dispositivo e la verifica del tipo di attendibilità migliorano la sicurezza e l'usabilità tramite:

- Evitare problemi, ad esempio, con MFA, quando il dispositivo è attendibile
- Blocco dell'accesso da dispositivi non attendibili
- Per Windows 10 dispositivi, fornire [l'accesso Single Sign-On](../devices/azuread-join-sso.md)alle risorse locali senza problemi.

È possibile realizzare questo obiettivo portando le identità dei dispositivi e gestendole Azure AD usando uno dei metodi seguenti:

- Le organizzazioni possono [usare](/intune/what-is-intune) Microsoft Intune per gestire il dispositivo e applicare i criteri di conformità, attestare l'integrità del dispositivo e impostare i criteri di accesso condizionale in base alla conformità del dispositivo. Microsoft Intune può gestire dispositivi iOS, desktop Mac (tramite l'integrazione JAMF), desktop Windows (in modo nativo tramite Gestione dispositivi mobili per Windows 10 e co-gestione con Microsoft Endpoint Configuration Manager) e dispositivi mobili Android.
- [Azure AD ibrido join fornisce](../devices/hybrid-azuread-join-managed-domains.md) la gestione con Criteri di gruppo o Microsoft Endpoint Configuration Manager in un ambiente con dispositivi computer aggiunti a un dominio di Active Directory. Le organizzazioni possono distribuire un ambiente gestito tramite PHS o PTA con Seamless SSO. Portare i dispositivi in Azure AD ottimizza la produttività degli utenti tramite SSO nel cloud e nelle risorse locali, consentendo [](../conditional-access/overview.md) allo stesso tempo di proteggere l'accesso alle risorse cloud e locali con l'accesso condizionale.

Se si dispone di dispositivi Windows aggiunti a un dominio non registrati nel cloud o di dispositivi Windows aggiunti a un dominio registrati nel cloud ma senza criteri di accesso condizionale, è necessario registrare i dispositivi non registrati e, in entrambi i casi, usare [l'aggiunta Azure AD ibrido](../conditional-access/require-managed-devices.md) come controllo nei criteri di accesso condizionale.

![Screenshot della concessione nei criteri di accesso condizionale che richiedono un dispositivo ibrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se si gestiscono dispositivi con MDM o Microsoft Intune, ma non si usano i [](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) controlli dei dispositivi nei criteri di accesso condizionale, è consigliabile usare Richiedi che il dispositivo sia contrassegnato come conforme come controllo in tali criteri.

![Screenshot della concessione nei criteri di accesso condizionale che richiedono la conformità del dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Lettura consigliata dei criteri di accesso per l'attendibilità dei dispositivi

- [Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](../devices/hybrid-azuread-join-plan.md)
- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

In Windows 10, [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) le password con l'autenticazione a due fattori avanzata nei PC. Windows Hello for Business un'esperienza di autenticazione a più fattori più semplice per gli utenti e riduce la dipendenza dalle password. Se non è stata avviata l'implementazione di dispositivi Windows 10 o sono stati distribuiti solo parzialmente, è consigliabile eseguire l'aggiornamento a Windows 10 e abilitare l'Windows Hello for Business in tutti i dispositivi. [](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

Per altre informazioni sull'autenticazione senza password, vedere Un mondo senza [password con](../authentication/concept-authentication-passwordless.md)Azure Active Directory .

## <a name="application-authentication-and-assignment"></a>Autenticazione e assegnazione dell'applicazione

### <a name="single-sign-on-for-apps"></a>Single Sign-On per le app

Fornire un meccanismo di accesso Single Sign-On standardizzato all'intera azienda è fondamentale per la migliore esperienza utente, la riduzione dei rischi, la capacità di creare report e la governance. Se si usano applicazioni che supportano l'accesso SSO con Azure AD ma sono attualmente configurate per l'uso di account locali, è necessario riconfigurare tali applicazioni per l'uso dell'accesso SSO con Azure AD. Analogamente, se si usano applicazioni che supportano l'accesso SSO con Azure AD ma usano un altro provider di identità, è necessario riconfigurare tali applicazioni per l'uso dell'accesso SSO con Azure AD. Per le applicazioni che non supportano i protocolli di federazione ma supportano l'autenticazione basata su form, è consigliabile configurare l'applicazione per l'uso dell'insieme di credenziali delle [password](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) con Azure AD Application Proxy.

![Accesso basato su password di AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se non si dispone di un meccanismo per individuare le applicazioni non gestite nell'organizzazione, è consigliabile implementare un processo di individuazione usando una soluzione casb (Cloud Access Security Broker), ad esempio [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Infine, se si ha una raccolta di app Azure AD e si usano applicazioni che supportano l'accesso SSO con Azure AD, è consigliabile elencare l'applicazione [nella raccolta di app](../develop/v2-howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Lettura consigliata dell'accesso Single Sign-On

- [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrazione di AD FS applicazioni a Azure AD

[La migrazione di app AD FS a Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) funzionalità aggiuntive per la sicurezza, una gestibilità più coerente e una migliore esperienza di collaborazione. Se sono presenti applicazioni configurate in AD FS che supportano l'accesso SSO con Azure AD, è necessario riconfigurare tali applicazioni per l'uso dell'accesso SSO con Azure AD. Se sono presenti applicazioni configurate in AD FS con configurazioni non comuni non supportate da Azure AD, è necessario contattare i proprietari dell'app per capire se la configurazione speciale è un requisito assoluto dell'applicazione. Se non è necessario, è necessario riconfigurare l'applicazione per l'uso dell'accesso SSO con Azure AD.

![Azure AD come provider di identità primario](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health per ADFS può](../hybrid/how-to-connect-health-adfs.md) essere usato per raccogliere i dettagli di configurazione relativi a ogni applicazione di cui è potenzialmente possibile eseguire la migrazione a Azure AD.

### <a name="assign-users-to-applications"></a>Assegnare utenti alle applicazioni

[L'assegnazione di utenti alle applicazioni](../manage-apps/assign-user-or-group-access-portal.md) è ottimale usando i gruppi perché consentono una maggiore flessibilità e capacità di gestione su larga scala. I vantaggi dell'uso dei gruppi [includono l'appartenenza](../enterprise-users/groups-dynamic-membership.md) dinamica ai gruppi basata su attributi e [la delega ai proprietari delle app.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Pertanto, se si usano e si gestiscono già i gruppi, è consigliabile eseguire le azioni seguenti per migliorare la gestione su larga scala:

- Delegare la gestione e la governance dei gruppi ai proprietari delle applicazioni.
- Consentire l'accesso self-service all'applicazione.
- Definire gruppi dinamici se gli attributi utente possono determinare in modo coerente l'accesso alle applicazioni.
- Implementare l'attestazione ai gruppi usati per l'accesso alle applicazioni [Azure AD verifiche di accesso.](../governance/access-reviews-overview.md)

D'altra parte, se si trovano applicazioni con assegnazione a singoli utenti, assicurarsi di implementare [la governance](../governance/index.yml) per tali applicazioni.

#### <a name="assign-users-to-applications-recommended-reading"></a>Assegnare gli utenti alle applicazioni di lettura consigliata

- [Assegnare utenti e gruppi a un'applicazione in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegare le autorizzazioni di registrazione dell'app in Azure Active Directory](../roles/delegate-app-roles.md)
- [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Criteri di accesso

### <a name="named-locations"></a>Posizioni specifiche

Con [i percorsi denominati](../reports-monitoring/quickstart-configure-named-locations.md) in Azure AD, è possibile etichettare gli intervalli di indirizzi IP attendibili nell'organizzazione. Azure AD usa le località denominate per:

- Evitare falsi positivi negli eventi di rischio. L'accesso da un percorso di rete attendibile riduce il rischio di accesso di un utente.
- Configurare l'[Accesso condizionale basato sulla località](../reports-monitoring/quickstart-configure-named-locations.md).

![Località denominate](./media/active-directory-ops-guide/active-directory-ops-img10.png)

In base alla priorità, usare la tabella seguente per trovare la soluzione consigliata più adatta alle esigenze dell'organizzazione:

| **Priorità** | **Scenario** | **Consiglio** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se si usano PHS o PTA e i percorsi denominati non sono stati definiti | Definire località denominate per migliorare il rilevamento degli eventi di rischio |
| 2 | Se si è federati e non si usa l'attestazione "insideCorporateNetwork" e i percorsi denominati non sono stati definiti | Definire località denominate per migliorare il rilevamento degli eventi di rischio |
| 3 | Se non si usano località denominate nei criteri di accesso condizionale e non sono presenti rischi o controlli dei dispositivi nei criteri di accesso condizionale | Configurare i criteri di accesso condizionale per includere località denominate |
| 4 | Se si è federati e si usa l'attestazione "insideCorporateNetwork" e i percorsi denominati non sono stati definiti | Definire località denominate per migliorare il rilevamento degli eventi di rischio |
| 5 | Se si usano indirizzi IP attendibili con MFA anziché località denominate e li si contrassegna come attendibili | Definire località denominate e contrassegnarle come attendibili per migliorare il rilevamento degli eventi di rischio |

### <a name="risk-based-access-policies"></a>Criteri di accesso basati sul rischio

Azure AD possibile calcolare il rischio per ogni accesso e per ogni utente. L'uso del rischio come criterio nei criteri di accesso può offrire un'esperienza utente migliore, ad esempio un minor numero di richieste di autenticazione e una maggiore sicurezza, ad esempio richiedere agli utenti solo quando sono necessari e automatizzare la risposta e la correzione.

![Criteri di rischio di accesso](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se si è già proprietari di Azure AD Premium P2 che supportano l'uso del rischio nei criteri di accesso, ma non vengono usate, è consigliabile aggiungere rischi al proprio stato di sicurezza.

#### <a name="risk-based-access-policies-recommended-reading"></a>Lettura consigliata dei criteri di accesso basati sul rischio

- [Procedura: Configurare i criteri di rischio di accesso](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Criteri di accesso alle applicazioni client

Microsoft Intune Application Management (MAM) offre la possibilità di eseguire il push dei controlli di protezione dei dati, ad esempio crittografia dell'archiviazione, PIN, pulizia dell'archiviazione remota e così via, in applicazioni client per dispositivi mobili compatibili, ad esempio Outlook Mobile. È anche possibile creare criteri di accesso condizionale per limitare [l'accesso](../conditional-access/app-based-conditional-access.md) ai servizi cloud, ad esempio Exchange Online, da app approvate o compatibili.

Se i dipendenti installano applicazioni con supporto MAM, ad esempio le app di Office per dispositivi mobili, per accedere alle risorse aziendali, ad esempio Exchange Online o SharePoint Online, e si supportano anche BYOD (bring your own device), è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione nei dispositivi di proprietà personale senza registrazione MDM e quindi aggiornare i criteri di accesso condizionale per consentire l'accesso solo dai client con supporto MAM.

![Controllo della concessione di accesso condizionale](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se i dipendenti installano applicazioni con supporto MAM per le risorse aziendali e l'accesso è limitato nei dispositivi gestiti da Intune, è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione per i dispositivi personali e aggiornare i criteri di accesso condizionale per consentire l'accesso solo dai client con supporto MAM.

### <a name="conditional-access-implementation"></a>Implementazione dell'accesso condizionale

L'accesso condizionale è uno strumento essenziale per migliorare le condizioni di sicurezza dell'organizzazione. Pertanto, è importante seguire queste procedure consigliate:

- Assicurarsi che a tutte le applicazioni SaaS sia applicato almeno un criterio
- Evitare di combinare il **filtro Tutte le app** con il controllo **blocca** per evitare il rischio di blocco
- Evitare di usare **Tutti gli utenti** come filtro e di aggiungere inavvertitamente **Guest**
- **Eseguire la migrazione di tutti i criteri "legacy" al portale di Azure**
- Rilevare tutti i criteri per utenti, dispositivi e applicazioni
- Usare i criteri di accesso condizionale [per implementare l'autenticazione](../conditional-access/plan-conditional-access.md)a più fattori anziché un'autenticazione a **più fattori per utente**
- Avere un piccolo set di criteri di base che possono essere applicati a più applicazioni
- Definire gruppi di eccezioni vuoti e aggiungerli ai criteri per avere una strategia di eccezione
- Pianificare account [break glass](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) senza controlli MFA
- Garantire un'esperienza coerente Microsoft 365 applicazioni client, ad esempio Teams, OneDrive, Outlook e così via. implementando lo stesso set di controlli per servizi come Exchange Online e Sharepoint Online
- L'assegnazione ai criteri deve essere implementata tramite gruppi, non singoli utenti
- Eseguire revisioni regolari dei gruppi di eccezioni usati nei criteri per limitare il tempo in cui gli utenti non sono al di fuori del livello di sicurezza. Se si è proprietari Azure AD P2, è possibile usare le verifiche di accesso per automatizzare il processo

#### <a name="conditional-access-recommended-reading"></a>Lettura consigliata dell'accesso condizionale

- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](../conditional-access/overview.md)
- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory informazioni di riferimento per le impostazioni di accesso condizionale](../conditional-access/concept-conditional-access-conditions.md)
- [Criteri comuni di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Accedere alla superficie di attacco

### <a name="legacy-authentication"></a>Autenticazione legacy

Credenziali avanzata, ad esempio MFA, non possono proteggere le app usando protocolli di autenticazione legacy, che lo rendono il vettore di attacco preferito da parte di attori malintenzionati. Il blocco dell'autenticazione legacy è fondamentale per migliorare il comportamento di sicurezza degli accessi.

L'autenticazione legacy è un termine che fa riferimento ai protocolli di autenticazione usati dalle app, ad esempio:

- Client office meno recenti che non usano l'autenticazione moderna (ad esempio, il client office 2010)
- Client che usano protocolli di posta elettronica come IMAP/SMTP/POP

Gli utenti malintenzionati preferiscono fortemente questi protocolli: quasi [il 100%](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) degli attacchi password spraying usa protocolli di autenticazione legacy. I pirati informatici usano protocolli di autenticazione legacy, perché non supportano l'accesso interattivo, che è necessario per problemi di sicurezza aggiuntivi, ad esempio l'autenticazione a più fattori e l'autenticazione dei dispositivi.

Se l'autenticazione legacy viene ampiamente usata nell'ambiente, è [](/office365/enterprise/modern-auth-for-office-2013-and-2016) consigliabile pianificare la migrazione dei client legacy ai client che supportano l'autenticazione moderna appena possibile. Nello stesso token, se alcuni utenti usano già l'autenticazione moderna, mentre altri ancora usano l'autenticazione legacy, è necessario eseguire i passaggi seguenti per bloccare i client di autenticazione legacy:

1. Usare [i report delle attività di accesso](../reports-monitoring/concept-sign-ins.md) per identificare gli utenti che usano ancora l'autenticazione legacy e pianificare la correzione:

   a. Eseguire l'aggiornamento ai client che supportano l'autenticazione moderna agli utenti interessati.
   
   b. Pianificare un intervallo di tempo di cutover per il blocco in base alla procedura seguente.
   
   c. Identificare le applicazioni legacy che hanno una dipendenza rigida dall'autenticazione legacy. Vedere il passaggio 3 di seguito.

2. Disabilitare i protocolli legacy nell'origine (ad esempio Cassetta postale di Exchange) per gli utenti che non usano l'autenticazione legacy per evitare una maggiore esposizione.
3. Per gli account rimanenti (idealmente identità non umane, ad esempio gli account di servizio), usare l'accesso condizionale per limitare i [protocolli legacy](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) dopo l'autenticazione.

#### <a name="legacy-authentication-recommended-reading"></a>Lettura consigliata dell'autenticazione legacy

- [Abilitare o disabilitare l'accesso POP3 o IMAP4 alle cassette postali in Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Concessioni di consenso

In un attacco illecito di concessione del consenso, l'utente malintenzionato crea un'applicazione registrata Azure AD che richiede l'accesso a dati quali informazioni di contatto, posta elettronica o documenti. Gli utenti potrebbero concedere il consenso ad applicazioni dannose tramite attacchi di phishing quando si atterrano in siti Web dannosi.

Di seguito è riportato un elenco di app con autorizzazioni che è possibile esaminare per i servizi cloud Microsoft:

- App con app o \* delegate. Autorizzazioni readwrite
- Le app con autorizzazioni delegate possono leggere, inviare o gestire la posta elettronica per conto dell'utente
- App a cui viene concesso l'oggetto usando le autorizzazioni seguenti:

| Risorsa | Autorizzazione |
| :- | :- |
| Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| API Microsoft Graph | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Alle app è stata concessa la rappresentazione utente completa dell'utente connesso. Ad esempio:

|Risorsa | Autorizzazione |
| :- | :- |
| API Microsoft Graph| Directory.AccessAsUser.All |
| API REST di Azure | user_impersonation |

Per evitare questo scenario, è necessario fare riferimento a rilevare e correggere le concessioni di consenso illecito [in Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) per identificare e correggere eventuali applicazioni con concessioni illecite o applicazioni con più concessioni del necessario. Successivamente, [rimuovere completamente il servizio self-service e](../manage-apps/configure-user-consent.md) stabilire le procedure di [governance.](../manage-apps/configure-admin-consent-workflow.md) Infine, pianificare revisioni regolari delle autorizzazioni dell'app e rimuoverle quando non sono necessarie.

#### <a name="consent-grants-recommended-reading"></a>Letture consigliate per le concessioni di consenso

- [Microsoft Graph autorizzazioni dell'API](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Impostazioni di utenti e gruppi

Di seguito sono riportate le impostazioni di utenti e gruppi che possono essere bloccate se non sono presenti esigenze aziendali esplicite:

#### <a name="user-settings"></a>Impostazioni utente

- **Utenti esterni:** la collaborazione esterna può avvenire in modo organico nell'organizzazione con servizi come Teams, Power BI, Sharepoint Online e Azure Information Protection. Se si hanno vincoli espliciti per controllare la collaborazione esterna avviata dall'utente, è consigliabile abilitare gli utenti esterni usando la gestione entitlement di [Azure AD](../governance/entitlement-management-overview.md) o un'operazione controllata, ad esempio tramite l'help desk. Se non si vuole consentire la collaborazione esterna organica per i servizi, è possibile impedire ai membri di [invitare completamente utenti esterni.](../external-identities/delegate-invitations.md) In alternativa, è anche possibile [consentire o bloccare domini specifici](../external-identities/allow-deny-list.md) negli inviti di utenti esterni.
- **Registrazioni app:** quando Registrazioni app abilitate, gli utenti finali possono eseguire l'onboarded delle applicazioni e concedere l'accesso ai propri dati. Un esempio tipico di registrazione dell'app è dato dagli utenti che abilitano i plug-in di Outlook o gli assistenti vocali, ad esempio Alexa e Siri, per leggere la posta elettronica e il calendario o inviare messaggi di posta elettronica per loro conto. Se il cliente decide di disattivare la registrazione dell'app, i team InfoSec e IAM devono essere coinvolti nella gestione delle eccezioni (registrazioni di app necessarie in base ai requisiti aziendali), perché devono registrare le applicazioni con un account amministratore e molto probabilmente richiedono la progettazione di un processo per operazionare il processo.
- **Portale di** amministrazione: le organizzazioni possono bloccare il pannello Azure AD nel portale di Azure in modo che gli utenti non amministratori non possano accedere alla gestione Azure AD nel portale di Azure e confondersi. Passare alle impostazioni utente nel portale di Azure AD per limitare l'accesso:

![Accesso limitato al portale di amministrazione](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Gli utenti non amministratori possono comunque accedere alle Azure AD di gestione tramite la riga di comando e altre interfacce a livello di codice.

#### <a name="group-settings"></a>Impostazioni dei gruppi

**Gestione gruppi self-service/ Gli utenti possono creare gruppi di sicurezza/Microsoft 365 gruppi.** Se non esiste alcuna iniziativa self-service corrente per i gruppi nel cloud, i clienti potrebbero decidere di disattivarla fino a quando non sono pronti a usare questa funzionalità.

#### <a name="groups-recommended-reading"></a>Lettura consigliata dei gruppi

- [Informazioni su Collaborazione B2B di Azure Active Directory](../external-identities/what-is-b2b.md)
- [Integrazione di applicazioni con Azure Active Directory](../develop/quickstart-register-app.md)
- [App, autorizzazioni e consenso in Azure Active Directory.](../develop/quickstart-register-app.md)
- [Usare i gruppi per gestire l'accesso alle risorse in Azure Active Directory](./active-directory-manage-groups.md)
- [Configurazione della gestione dell'accesso alle applicazioni self-service in Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Traffico proveniente da posizioni impreviste

Gli utenti malintenzionati provengono da varie parti del mondo. Gestire questo rischio usando criteri di accesso condizionale con località come condizione. La [condizione di posizione](../conditional-access/location-condition.md) di un criterio di accesso condizionale consente di bloccare l'accesso per le località da cui non esiste alcun motivo aziendale da cui accedere.

![Creare una nuova posizione denominata](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponibile, usare una soluzione siem (Security Information and Event Management) per analizzare e trovare modelli di accesso tra aree. Se non si usa un prodotto SIEM o non vengono inserite informazioni di autenticazione [](../../azure-monitor/overview.md) da Azure AD, è consigliabile usare Monitoraggio di Azure per identificare i modelli di accesso tra le aree.

## <a name="access-usage"></a>Utilizzo dell'accesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD log archiviati e integrati con i piani di risposta agli eventi imprevisti

Avere accesso alle attività di accesso, ai controlli e agli eventi di rischio per Azure AD è fondamentale per la risoluzione dei problemi, l'analisi dell'utilizzo e le indagini forensi. Azure AD l'accesso a queste origini tramite LE API REST con un periodo di conservazione limitato. Un sistema siem (Security Information and Event Management) o una tecnologia di archiviazione equivalente è fondamentale per l'archiviazione a lungo termine dei controlli e la supportabilità. Per abilitare l'archiviazione a lungo termine Azure AD log, è necessario aggiungerli alla soluzione SIEM esistente [o](../reports-monitoring/concept-activity-logs-azure-monitor.md)usare Monitoraggio di Azure . Archiviare i log che possono essere usati come parte dei piani di risposta agli eventi imprevisti e delle indagini.

#### <a name="logs-recommended-reading"></a>Lettura consigliata dei log

- [Informazioni di riferimento sull'API di controllo di Azure Active Directory](/graph/api/resources/directoryaudit)
- [Riferimento API del report sull'attività di accesso di Azure Active Directory](/graph/api/resources/signin)
- [Ottenere dati con l'API di creazione report di Azure AD con i certificati](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph per Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Informazioni di riferimento sull'API delle attività di gestione di Office 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Come usare il pacchetto Azure Active Directory Power BI distribuzione](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Riepilogo

Un'infrastruttura di gestione delle identità sicura ha 12 aspetti. Questo elenco consente di proteggere e gestire ulteriormente le credenziali, definire l'esperienza di autenticazione, delegare l'assegnazione, misurare l'utilizzo e definire i criteri di accesso in base al livello di sicurezza aziendale.

- Assegnare i proprietari alle attività principali.
- Implementare soluzioni per rilevare password deboli o persi, migliorare la gestione e la protezione delle password e proteggere ulteriormente l'accesso degli utenti alle risorse.
- Gestire l'identità dei dispositivi per proteggere le risorse in qualsiasi momento e da qualsiasi posizione.
- Implementare l'autenticazione senza password.
- Fornire un meccanismo di accesso Single Sign-On standardizzato all'interno dell'organizzazione.
- Eseguire la migrazione delle app AD FS a Azure AD per migliorare la sicurezza e una gestibilità più coerente.
- Assegnare gli utenti alle applicazioni usando i gruppi per consentire una maggiore flessibilità e capacità di gestione su larga scala.
- Configurare i criteri di accesso basati sul rischio.
- Bloccare i protocolli di autenticazione legacy.
- Rilevare e correggere le concessioni di consenso illecite.
- Bloccare le impostazioni di utenti e gruppi.
- Abilitare l'archiviazione a lungo termine dei Azure AD per la risoluzione dei problemi, l'analisi dell'utilizzo e le indagini forensi.

## <a name="next-steps"></a>Passaggi successivi

Introduzione ai controlli [operativi e alle azioni di Identity Governance.](active-directory-ops-guide-govern.md)
