---
title: Pianificare una Azure Active Directory distribuzione dell'accesso Single Sign-On
description: Guida per pianificare, distribuire e gestire l'accesso SSO nell'organizzazione.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f395eaf21dce0077c1239ef2251973aeb5faa70
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375611"
---
# <a name="plan-a-single-sign-on-deployment"></a>Pianificare una distribuzione di Single Sign-On

Single Sign-On (SSO) significa accedere a tutte le applicazioni e a tutte le risorse necessarie a un utente effettuando l'accesso una sola volta usando un singolo account utente. Con l'accesso Single Sign-On, gli utenti possono accedere a tutte le applicazioni necessarie senza dover eseguire l'autenticazione una seconda volta.

## <a name="benefits-of-sso"></a>Vantaggi dell'accesso Single Sign-On

L'accesso Single Sign-On (SSO) aggiunge sicurezza e praticità quando gli utenti a Azure Active Directory (Azure AD). 

Molte organizzazioni si basano su applicazioni SaaS (Software as a Service), ad esempio Microsoft 365, Box e Salesforce, per la produttività degli utenti finali. In una storia, il personale IT doveva creare e aggiornare singolarmente gli account utente in ogni applicazione SaaS e gli utenti dovevano ricordare una password per ognuno.

Il Azure Marketplace ha più di 3000 applicazioni con connessioni SSO preintegre, semplificando l'integrazione nel tenant.

## <a name="licensing"></a>Gestione delle licenze

- **Azure AD licenze:** l'accesso Single Sign-On per le applicazioni SaaS pre-integrate è gratuito. Tuttavia, il numero di oggetti nella directory e le funzionalità da distribuire possono richiedere licenze aggiuntive. Per un elenco completo dei requisiti di licenza, vedere Azure Active Directory [Prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenze per le** applicazioni: saranno necessarie le licenze appropriate per le applicazioni SaaS per soddisfare le esigenze aziendali. Collaborare con il proprietario dell'applicazione per determinare se gli utenti assegnati all'applicazione hanno le licenze appropriate per i ruoli all'interno dell'applicazione. Se Azure AD il provisioning automatico in base ai ruoli, i ruoli assegnati in Azure AD devono essere allineati al numero di licenze di proprietà dell'applicazione. Un numero non corretto di licenze appartenenti all'applicazione può causare errori durante il provisioning/aggiornamento di un utente.

## <a name="plan-your-sso-team"></a>Pianificare il team SSO

- **Coinvolgere gli stakeholder più** interessati: quando i progetti tecnologici hanno esito negativo, è in genere dovuto a aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare questi problemi, assicurarsi di coinvolgere gli [stakeholder](../fundamentals/active-directory-deployment-plans.md) più interessati e che comprenda il proprio ruolo.
- **Pianificare le** comunicazioni: la comunicazione è fondamentale per il successo di qualsiasi nuovo servizio. Comunicare in modo proattivo agli utenti come cambierà l'esperienza, quando cambierà e come ottenere supporto in caso di problemi. Esaminare le opzioni relative al [modo in cui gli utenti finali accederanno](end-user-experiences.md)alle applicazioni abilitate per l'accesso SSO e creare le comunicazioni in base alla selezione. 

## <a name="plan-your-sso-protocol"></a>Pianificare il protocollo SSO

Un'implementazione SSO basata su protocolli di federazione migliora la sicurezza, l'affidabilità e le esperienze degli utenti finali ed è più facile da implementare. Molte applicazioni sono pre-integrate in Azure AD con [guide dettagliate disponibili.](../saas-apps/tutorial-list.md) È possibile trovarli nella pagina [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informazioni dettagliate su ogni metodo SSO sono disponibili nell'articolo [Accesso Single Sign-On alle](what-is-single-sign-on.md)applicazioni in Azure Active Directory .

Esistono due modi principali per abilitare gli utenti per l'accesso Single Sign-On alle app:

- **Con Single Sign-On federato** Azure AD autentica l'utente nell'applicazione usando il proprio account Azure AD utente. Questo metodo è supportato per le applicazioni che supportano protocolli come SAML 2.0, WS-Federation o OpenID Connect ed è la modalità più avanzata di Single Sign-On. È consigliabile usare SSO federato con Azure AD quando un'applicazione lo supporta, invece di SSO basato su password e ADFS.

- **Con l'accesso Single Sign-On** basato su password, gli utenti accedono all'applicazione con un nome utente e una password la prima volta. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questa opzione sfrutta il processo di accesso esistente fornito dall'applicazione, consente a un amministratore di gestire le password e non richiede all'utente di conoscerla.

### <a name="considerations-for-federation-based-sso"></a>Considerazioni per l'accesso SSO basato sulla federazione

- **Uso di OpenID Connect e OAuth:** se l'applicazione a cui ci si connette la supporta, usare il metodo OIDC/OAuth 2.0 per abilitare l'accesso SSO a tale applicazione. Questo metodo richiede meno configurazione e consente un'esperienza utente più ricca. Per altre informazioni, vedere [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)e [Azure Active Directory per sviluppatori.](../develop/index.yml)
- **Configurazioni degli endpoint per l'accesso SSO** basato su SAML: se si usa SAML, gli sviluppatori avranno bisogno di informazioni specifiche prima di configurare l'applicazione. Per altre informazioni, vedere [Configurare l'accesso Single Sign-On basato su SAML.](configure-saml-single-sign-on.md)
- **Gestione dei certificati per l'accesso SSO** basato su SAML: quando si abilita l'accesso SSO federato per l'applicazione, Azure AD crea un certificato valido per impostazione predefinita per tre anni. Se necessario, è possibile personalizzare la data di scadenza del certificato. Assicurarsi che siano presenti processi per rinnovare i certificati prima della scadenza. Per altre informazioni, vedere Gestione [Azure AD certificati](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>Considerazioni sull'accesso SSO basato su password

L Azure AD per l'accesso SSO basato su password richiede la distribuzione di un'estensione del browser che recupererà in modo sicuro le credenziali e compila i moduli di accesso. Definire un meccanismo per distribuire l'estensione su larga scala con [i browser supportati.](../user-help/my-apps-portal-end-user-access.md) Le opzioni includono:

- [Criteri di gruppo per Internet Explorer](my-apps-deployment-plan.md)
- [Gestione configurazione per Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configurazione guidati dall'utente per Chrome, Firefox, Microsoft Edge o IE](../user-help/my-apps-portal-end-user-access.md)

Per altre informazioni, vedere [Come configurare l'accesso Single Sign-On per le password.](./configure-password-single-sign-on-non-gallery-applications.md)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Acquisizione dei metadati dei moduli di accesso per le applicazioni non presenti nella raccolta

Microsoft supporta l'acquisizione di metadati in un'applicazione Web per l'insieme di credenziali delle password (acquisizione dei campi nome utente e password). Passare all'URL di accesso durante il processo di configurazione dell'applicazione per acquisire i metadati dei moduli. Chiedere al proprietario dell'applicazione l'URL di accesso esatto. Queste informazioni vengono usate durante il processo di accesso, mappando Azure AD credenziali all'applicazione durante l'accesso.

Per altre informazioni, vedere Informazioni sull'accesso alle applicazioni e SSO con [Azure AD? - SSO basato su password.](./what-is-single-sign-on.md)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicazioni che i metadati nei moduli devono essere riequisiti

Quando le applicazioni modificano il layout HTML, potrebbe essere necessario ricavare i metadati per adattare le modifiche. I sintomi comuni che indicano che il layout HTML presenta modifiche includono:

- Gli utenti che segnalano che facendo clic sull'applicazione si bloccano nella pagina di accesso
- Utenti che segnalano che il nome utente o la password non è popolato

#### <a name="shared-accounts"></a>Shared accounts (Account condivisi)

Dal punto di vista dell'accesso, le applicazioni con account condivisi non sono diverse da un'applicazione della raccolta che usa l'accesso SSO basato su password per i singoli utenti. Tuttavia, sono necessari alcuni passaggi aggiuntivi durante la pianificazione e la configurazione di un'applicazione destinata all'uso di account condivisi:

1. Collaborare con gli utenti aziendali dell'applicazione per documentare quanto segue:
   1. Set di utenti dell'organizzazione che useranno l'applicazione
   1. Set esistente di credenziali nell'applicazione associata al set di utenti 
1. Per ogni combinazione di set di utenti e credenziali, creare un gruppo di sicurezza nel cloud o in locale in base alle esigenze.
1. Reimpostare le credenziali condivise. Dopo la distribuzione dell'app in Azure AD, gli utenti non necessitano della password dell'account condiviso. Poiché Azure AD la password verrà archiviata, è consigliabile impostarla come molto lunga e complessa. 
1. Configurare il rollover automatico della password, se l'applicazione la supporta. In questo modo, nemmeno l'amministratore che ha fatto la configurazione iniziale conoscerà la password dell'account condiviso. 

## <a name="plan-your-authentication-method"></a>Pianificare il metodo di autenticazione

La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. Implementare il metodo di autenticazione configurato mediante Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud.

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo. È consigliabile scegliere quello più simile allo scenario specifico. Per altre informazioni, vedere [Scegliere il metodo di autenticazione appropriato per la Azure Active Directory ibrida di gestione delle identità.](../hybrid/choose-ad-authn.md)

## <a name="plan-your-security-and-governance"></a>Pianificare la sicurezza e la governance 

L'identità è il nuovo pivot principale per l'attenzione e gli investimenti per la sicurezza, perché i perimetri di rete sono diventati sempre più porosi e meno efficaci con l'esplosione di dispositivi BYOD e applicazioni cloud. 

### <a name="plan-access-reviews"></a>Pianificare le verifiche di accesso

[Le verifiche di](../governance/create-access-review.md) accesso consentono alle organizzazioni di gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È consigliabile esaminare regolarmente l'accesso degli utenti per assicurarsi che l'accesso continui solo alle persone giuste.

Alcuni degli argomenti chiave da pianificare durante la configurazione delle verifiche di accesso includono:

1. Identificazione di una cadenza per le verifiche di accesso in base alle esigenze aziendali. Può essere frequente come una volta alla settimana, mensilmente, annualmente o come esercizio su richiesta.

1. Creare gruppi che rappresentano i revisori dei report di accesso all'app. È necessario assicurarsi che gli stakeholder che hanno maggiore familiarità con l'app e gli utenti di destinazione e i casi d'uso siano partecipanti alle verifiche di accesso

1. Il completamento di una verifica di accesso include la rimozione delle autorizzazioni di accesso alle app per gli utenti che non hanno più bisogno dell'accesso. Pianificare la gestione di potenziali richieste di supporto da parte di utenti negati. Un utente eliminato rimarrà eliminato in Azure AD per 30 giorni durante i quali può essere ripristinato da un amministratore, se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Usando il Azure Active Directory, un amministratore globale può eliminare in modo esplicito in modo permanente un utente eliminato di recente prima che venga raggiunto tale periodo di tempo.

### <a name="plan-auditing"></a>Pianificare il controllo

Azure AD fornisce [report contenenti informazioni tecniche e aziendali.](../reports-monitoring/overview-reports.md) 

Sono disponibili sia report di sicurezza che di attività. I report di sicurezza mostrano gli utenti contrassegnati per il rischio e gli accesso a rischio. I report attività consentono di comprendere il comportamento degli utenti nell'organizzazione, dettagliando l'attività di accesso e fornendo audit trail di tutti gli account di accesso. È possibile usare i report per gestire i rischi, aumentare la produttività e monitorare la conformità.

| Tipo di report | Verifica di accesso | Report sulla sicurezza | Report di accesso |
|-------------|---------------|------------------|----------------|
| Usare per esaminare | Autorizzazioni e utilizzo dell'applicazione. | Account potenzialmente compromessi | Chi accede alle applicazioni |
| Azioni potenziali | Controllare l'accesso; revocare le autorizzazioni | Revocare l'accesso; forzare la reimpostazione della sicurezza | revoca dell'accesso |

Azure AD conserva la maggior parte dei dati di controllo per 30 giorni e li rende disponibili tramite il portale di amministrazione di Azure o tramite un'API da scaricare nei sistemi di analisi.

### <a name="consider-using-microsoft-cloud-application-security"></a>Prendere in considerazione l'uso di Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) è una soluzione Cloud Access Security Broker (CASB). Offre visibilità su app e servizi cloud, offre analisi sofisticate per identificare e contrastare le minacce informatiche e consente di controllare la modalità di viaggio dei dati.

La distribuzione di MCAS consente di:

- Usare Cloud Discovery per eseguire il mapping e identificare l'ambiente cloud e le app cloud usate dall'organizzazione.
- App che vengono sanzionate e non sanzionate nel cloud
- Usare connettori di app facili da distribuire che sfruttano le API del provider per la visibilità e la governance delle app a cui ci si connette
- Usare Controllo app per l'accesso condizionale per ottenere visibilità e controllo in tempo reale sull'accesso e sulle attività all'interno delle app cloud
- Consente di avere un controllo continuo impostando e quindi continuando l'ottimizzazione dei criteri.

Il controllo della sessione di Microsoft Cloud Application Security (MCAS) è disponibile per qualsiasi browser su qualsiasi piattaforma principale in qualsiasi sistema operativo. È anche possibile bloccare o consentire le app per dispositivi mobili e le app desktop. Grazie all'integrazione nativa con Azure AD, possono essere supportate tutte le app configurate con SAML o le app Open ID Connect con Single Sign-On in Azure AD, tra cui diverse app in primo [piano.](/cloud-app-security/proxy-intro-aad)

Per informazioni su MCAS, vedere la panoramica [Microsoft Cloud App Security.](/cloud-app-security/what-is-cloud-app-security) MCAS è un servizio di sottoscrizione basato sull'utente. È possibile esaminare i dettagli delle licenze nel foglio dati [sulle licenze di MCAS.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Usare l'accesso condizionale

Con l'accesso condizionale è possibile automatizzare le decisioni di controllo degli accessi basate su criteri per le app cloud.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. L'accesso condizionale non è quindi inteso come difesa di prima linea per scenari come attacchi Denial of Service (DoS), ma può usare i segnali provenienti da questi eventi per determinare l'accesso. È ad esempio possibile usare il livello di rischio di accesso, la posizione della richiesta e così via. Per altre informazioni sull'accesso condizionale, vedere [la panoramica](../conditional-access/plan-conditional-access.md) e il piano [di distribuzione](../conditional-access/plan-conditional-access.md).

## <a name="azure-sso-technical-requirements"></a>Requisiti tecnici dell'accesso Single Sign-On di Azure

La sezione seguente contiene informazioni dettagliate sui requisiti per configurare l'applicazione specifica, inclusi gli ambienti necessari, gli endpoint, il mapping delle attestazioni, gli attributi obbligatori, i certificati e i protocolli usati. Queste informazioni sono necessarie per configurare l'accesso SSO [nel portale Azure AD .](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Dettagli del meccanismo di autenticazione

Per tutte le app SaaS pre-integrate, Microsoft offre un'esercitazione e non saranno necessarie queste informazioni. Se l'applicazione non è nel marketplace o nella raccolta di applicazioni, potrebbe essere necessario raccogliere i dati seguenti:

- **Provider di identità corrente che l'applicazione usa per l'accesso SSO,** se applicabile: ad esempio: AD FS, PingFederate, Okta
- **Protocolli supportati dall'applicazione** di destinazione: ad esempio SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protocollo configurato con Azure AD:** ad esempio SAML 2.0 o 1.1, OpenID Connect, OAuth, basato su moduli, WS-Fed

### <a name="attribute-requirements"></a>Requisiti degli attributi

Esiste un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, ad esempio i gruppi. Pianificare il mapping degli attributi utente Azure AD'applicazione e personalizzare i [mapping degli](../app-provisioning/customize-application-attributes.md) attributi predefiniti in base alle esigenze aziendali.

### <a name="certificate-requirements"></a>Requisiti per i certificati

Il certificato per l'applicazione deve essere aggiornato o esiste il rischio che gli utenti non siano in grado di accedere all'applicazione. La maggior parte dei certificati delle applicazioni SaaS è buona per 36 mesi. La durata del certificato viene modificata nel pannello dell'applicazione. Assicurarsi di documentare la scadenza e sapere come gestire il rinnovo del certificato. 

Esistono due modi per gestire i certificati. 

- **Rollover automatico dei certificati:** Microsoft supporta il [rollover della chiave di firma in Azure AD](../develop/active-directory-signing-key-rollover.md). Anche se questo è il metodo preferito per la gestione dei certificati, non tutti gli ISV supportano questo scenario.

- **Aggiornamento manuale:** ogni applicazione ha un proprio certificato che scade in base alla modalità di definizione. Prima della scadenza del certificato dell'applicazione, creare un nuovo certificato e inviarlo all'ISV. Queste informazioni possono essere estratta dai metadati della federazione. [Per altre informazioni sui metadati di federazione, vedere qui.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Implementare l'accesso Single Sign-On

Usare le fasi seguenti per pianificare e distribuire la soluzione nell'organizzazione:

### <a name="user-configuration-for-sso"></a>Configurazione utente per SSO

- **Identificare gli utenti di test**

   Contattare il proprietario dell'app e chiedere di creare almeno tre utenti di test all'interno dell'applicazione. Verificare che le informazioni che verranno usate come identificatore primario siano popolate correttamente e corrispondano a un attributo disponibile in Azure AD. Nella maggior parte dei casi verrà mappato a "NameID" per le applicazioni basate su SAML. Per i token JWT, si tratta del "preferred_username".
   
   Creare l'utente in Azure AD manualmente come utente basato sul cloud o sincronizzare l'utente dall'ambiente locale usando il motore Azure AD Connect sincronizzazione. Verificare che le informazioni corrispondano alle attestazioni inviate all'applicazione.

- **Configurare SSL**

   [Nell'elenco delle applicazioni](../saas-apps/tutorial-list.md)individuare e aprire l'esercitazione sull'accesso SSO per l'applicazione, quindi seguire i passaggi dell'esercitazione per configurare correttamente l'applicazione SaaS.

   Se non è possibile individuare l'applicazione, vedere la [documentazione dell'applicazione personalizzata](./configure-saml-single-sign-on.md). Verrà illustrato come aggiungere un'applicazione che non si trova nella Azure AD raccolta.

   Facoltativamente, è possibile usare le attestazioni rilasciate nel token SAML per [l'applicazione](../develop/active-directory-claims-mapping.md)aziendale usando la documentazione di Microsoft. Assicurarsi che il mapping sia quello che si prevede di ricevere nella risposta SAML per l'applicazione. Se si verificano problemi durante la configurazione, usare le indicazioni su [come eseguire il debug dell'integrazione SSO.](./debug-saml-sso-issues.md)

### <a name="provide-sso-change-communications-to-end-users"></a>Fornire comunicazioni di modifica dell'accesso Single Sign-On agli utenti finali

Implementare il piano di comunicazione. Assicurarsi di far sapere agli utenti finali che una modifica è in arrivo, quando è arrivata, cosa fare ora e come richiedere assistenza.

### <a name="verify-end-user-scenarios-for-sso"></a>Verificare gli scenari degli utenti finali per l'accesso SSO

È possibile usare i test case seguenti per eseguire test su dispositivi personali e di proprietà dell'azienda per assicurarsi che le configurazioni SSO funzionino come previsto. Gli scenari seguenti presuppongono che un utente sia in grado di passare all'URL di un'applicazione e di passare attraverso un flusso di autenticazione avviato dal provider di servizi (flusso di autenticazione avviato da SP).

| Scenario | Risultato previsto nel flusso di autenticazione avviato da SP dall'utente |
|----------|---------------------------------------------------|
| Accedere all'applicazione con IE mentre è in corpnet. | autenticazione integrata di Windows (IWA) si verifica senza richieste aggiuntive. |
| Accedere all'applicazione con IE mentre si è fuori dalla rete corpnet con un nuovo tentativo di accesso. | Richiesta basata su moduli in AD FS Server. L'utente accede correttamente e il browser richiede L'autenticazione a più fattori. |
| Accedere all'applicazione con IE quando si è fuori rete con una sessione corrente e non ha mai eseguito l'autenticazione a più fattori. | L'utente non riceve la richiesta di primo fattore. L'utente riceve una richiesta di autenticazione a più fattori. |
| Accedere all'applicazione con Internet Directory quando si è fuori rete con una sessione corrente ed è già stata eseguita l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. SSO utente nell'applicazione. |
| Accedere all'applicazione con Chrome/Firefox/Safari mentre si è all'di fuori della rete virtuale con una sessione corrente ed è già stata eseguita l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. Accesso Single Sign-On dell'utente nell'applicazione. |
| Accedere all'applicazione con Chrome/Firefox/Safari mentre si è fuori dalla rete virtuale con un nuovo tentativo di accesso. | Richiesta basata su moduli in AD FS Server. L'utente accede correttamente e il browser richiede l'autenticazione a più fattori. |
| Accedere all'applicazione con Chrome/Firefox mentre si è nella rete aziendale con una sessione corrente. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. Accesso Single Sign-On dell'utente nell'applicazione. |
| Accedere all'applicazione con l'app per dispositivi mobili dell'applicazione con un nuovo tentativo di accesso. | Richiesta basata su moduli in AD FS Server. L'utente accede correttamente e il client ADAL richiede l'autenticazione a più fattori. |
| Un utente non autorizzato tenta di accedere all'applicazione con l'URL di accesso. | Richiesta basata su moduli in AD FS Server. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere ma immette una password non corretta. | L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic sull'URL e viene eseguito l'accesso all'applicazione senza richieste aggiuntive. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic sull'URL e chiede di eseguire l'autenticazione con il primo fattore. |
| L'utente autorizzato accede all'applicazione con l'app per dispositivi mobili dell'applicazione (avviata da SP) con un nuovo tentativo di accesso. | Richiesta basata su moduli in AD FS Server. L'utente accede correttamente e il client ADAL richiede L'autenticazione a più fattori. |
| L'utente non autorizzato tenta di accedere all'applicazione con l'URL di accesso (avviato da SP). | Richiesta basata su moduli in AD FS Server. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere ma immette una password non corretta.| L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato si disconnette e quindi esegue di nuovo l'accesso. | Se l'URL di disconnessione è configurato, l'utente viene disconnesso da tutti i servizi e viene richiesto di eseguire l'autenticazione. |
| L'utente autorizzato si disconnette e quindi esegue di nuovo l'accesso. | Se l'URL di disconnessione non è configurato, l'utente verrà connesso automaticamente usando un token esistente dalla sessione del browser Azure AD esistente. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic sull'URL ed è connesso all'applicazione senza richieste aggiuntive. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic sull'URL e viene richiesto di eseguire l'autenticazione con il primo fattore. |

## <a name="manage-sso"></a>Gestire l'accesso Single Sign-On

Questa sezione descrive i requisiti e le raccomandazioni per gestire correttamente l'accesso SSO.

### <a name="required-administrative-roles"></a>Ruoli amministrativi necessari

Usare sempre il ruolo con il numero minimo di autorizzazioni disponibili per eseguire l'attività richiesta all'interno Azure Active Directory. Microsoft consiglia [di esaminare i diversi ruoli disponibili](../roles/permissions-reference.md) e scegliere quello più opportuno per soddisfare le proprie esigenze per ogni utente tipo per questa applicazione. Potrebbe essere necessario applicare temporaneamente e rimuovere alcuni ruoli dopo il completamento della distribuzione.

| Persona| Ruoli | Azure AD ruolo (se necessario) |
|--------|-------|-----------------------------|
| Amministratore del supporto tecnico | Supporto di livello 1 | nessuno |
| Amministratore delle identità | Configurare ed eseguire il debug quando i problemi influiscono Azure AD | Amministratore globale |
| Amministratore dell'applicazione | Attestazione utente nell'applicazione, configurazione per gli utenti con autorizzazioni | nessuno |
| Amministratori dell'infrastruttura | Proprietario del rollover del certificato | Amministratore globale |
| Proprietario/stakeholder dell'azienda | Attestazione utente nell'applicazione, configurazione per gli utenti con autorizzazioni | nessuno |

È consigliabile [usare Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) per gestire i ruoli per fornire controlli, controlli e verifiche di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

### <a name="sso-certificate-lifecycle-management"></a>Gestione del ciclo di vita dei certificati SSO

È importante identificare i ruoli e le liste di distribuzione di posta elettronica con il compito di gestire il ciclo di vita del certificato di firma tra Azure AD e l'applicazione configurata con Single Sign-On. Ecco alcuni dei ruoli chiave che è consigliabile avere:

- Proprietario per l'aggiornamento delle proprietà utente nell'applicazione
- Supporto dell'interruzione/correzione dell'applicazione in chiamata del proprietario
- Elenco di distribuzione di posta elettronica attentamente monitorato per le notifiche di modifica correlate ai certificati

La durata massima di un certificato è di tre anni. È consigliabile stabilire un processo per la gestione di una modifica del certificato tra Azure AD'applicazione. Ciò consente di evitare o ridurre al minimo un'interruzione a causa della scadenza di un certificato o forzare il rollover del certificato.

### <a name="rollback-process"></a>Processo di rollback

Dopo aver completato i test in base ai test case, è possibile passare all'ambiente di produzione con l'applicazione. Passare all'ambiente di produzione significa implementare le configurazioni pianificate e testate nel tenant di produzione e implementarla agli utenti. Tuttavia, è importante pianificare le attività da eseguire nel caso in cui la distribuzione non vada come previsto. Se la configurazione dell'accesso Single Sign-On ha esito negativo durante la distribuzione, è necessario comprendere come attenuare eventuali interruzioni e ridurre l'impatto sugli utenti.

La disponibilità dei metodi di autenticazione all'interno dell'applicazione determinerà la strategia migliore. Assicurarsi sempre di avere una documentazione dettagliata per i proprietari delle app su come tornare esattamente allo stato di configurazione dell'account di accesso originale nel caso in cui la distribuzione si sia verificata in problemi.

- **Se l'app supporta** più provider di identità, ad esempio LDAP e AD FS e Ping, non eliminare la configurazione SSO esistente durante l'implementazione. Disabilitarlo invece durante la migrazione nel caso in cui sia necessario tornare indietro in un secondo momento. 

- Se **l'app** non supporta più PROVIDER di identità, ma consente agli utenti di accedere usando l'autenticazione basata su moduli (nome utente/password), assicurarsi che gli utenti possano eseguire il fall back a questo approccio in caso di errore della nuova implementazione della configurazione SSO.

### <a name="access-management"></a>Gestione degli accessi

È consigliabile scegliere un approccio ridimensionato quando si gestisce l'accesso alle risorse. Gli approcci comuni includono l'uso di gruppi locali tramite la sincronizzazione tramite Azure AD Connect, la creazione di gruppi dinamici [in Azure AD in](../enterprise-users/groups-dynamic-membership.md)base agli attributi utente o la creazione di gruppi [self-service](../enterprise-users/groups-self-service-management.md) in Azure AD gestiti da un proprietario delle risorse.

### <a name="monitor-security"></a>Monitorare la sicurezza

È consigliabile impostare una cadenza regolare in cui esaminare i diversi aspetti della sicurezza delle app SaaS ed eseguire le azioni correttive necessarie.

### <a name="troubleshooting"></a>Risoluzione dei problemi

I collegamenti seguenti presentano scenari di risoluzione dei problemi. È possibile creare una guida specifica per il personale di supporto che incorpora questi scenari e i passaggi per risolverli.

#### <a name="consent-issues"></a>Problemi di consenso

- [Errore di consenso imprevisto](./application-sign-in-unexpected-user-consent-prompt.md)

- [Errore di consenso utente](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Problemi di accesso

- [Problemi di accesso da un portale personalizzato](./application-sign-in-other-problem-access-panel.md)

- [Problemi durante l'accesso da App personali](./application-sign-in-other-problem-access-panel.md)

- [Errore della pagina di accesso dell'applicazione](./application-sign-in-problem-application-error.md)

- [Problemi di accesso a un'applicazione Microsoft](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>Problemi di SSO per le applicazioni

- [Problema con l'accesso SSO con password per le applicazioni](./troubleshoot-password-based-sso.md) 

- [Problemi di accesso ad app configurate per Single Sign-On basato su SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Passaggi successivi

[Eseguire il debug di Single Sign-On basato su SAML](./debug-saml-sso-issues.md)

[Mapping di attestazioni per le app tramite PowerShell](../develop/active-directory-claims-mapping.md)

[Personalizzazione delle attestazioni rilasciate nel token SAML](../develop/active-directory-saml-claims-customization.md)

[Protocollo SAML Single Sign-On](../develop/single-sign-on-saml-protocol.md)

[Protocollo SAML Sign-Out singolo](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (per utenti esterni come partner e fornitori)

[Accesso condizionale di Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Accesso SSO](./what-is-single-sign-on.md)

[Esercitazione sull'accesso Single Sign-On dell'applicazione](../saas-apps/tutorial-list.md)

[Download del white paper - Piano di distribuzione dell'accesso Single Sign-On](https://aka.ms/SSODeploymentPlan)