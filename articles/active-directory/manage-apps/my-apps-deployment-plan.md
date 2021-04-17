---
title: Pianificare Azure Active Directory App personali configurazione
description: Guida alla pianificazione per usare in modo efficace App personali nell'organizzazione.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376597"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Pianificare Azure Active Directory App personali configurazione

> [!NOTE]
> Questo articolo è progettato per i professionisti IT che devono pianificare la configurazione del portale App personali aziendale. 
>
> **Per la documentazione [](../user-help/my-apps-portal-end-user-access.md)dell'utente finale,** vedere Accedere e avviare app dal portale App personali .

Azure Active Directory (Azure AD) App personali è un portale basato sul Web per l'avvio e la gestione delle app. La App personali pagina offre agli utenti un'unica posizione in cui iniziare il lavoro e trovare tutte le applicazioni a cui hanno accesso. Gli utenti accedono App personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Perché configurare App personali

Il App personali è disponibile per impostazione predefinita per gli utenti e non può essere disattivato. È importante configurarlo in modo che abbia la migliore esperienza possibile e che il portale rimanga utile. 

Qualsiasi applicazione nell'Azure Active Directory di applicazioni aziendali viene visualizzata quando vengono soddisfatte entrambe le condizioni seguenti:

* La proprietà di visibilità per l'app è impostata su true. 

* L'app viene assegnata a qualsiasi utente o gruppo. Viene visualizzato per gli utenti assegnati.

La configurazione del portale garantisce che le persone giuste possano trovare facilmente le app giuste.

 
### <a name="how-is-the-my-apps-portal-used"></a>Come viene usato App personali portale?

Gli utenti accedono App personali portale di azure a:

* Individuare e accedere a tutte le applicazioni connesse Azure AD dell'organizzazione a cui hanno accesso.

   * È meglio assicurarsi che le app siano configurate per l'accesso Single Sign-On (SSO) per offrire agli utenti la migliore esperienza.

* Richiedere l'accesso alle nuove app configurate per il self-service.

* Creare raccolte personali di app.

* Gestire l'accesso alle app per altri utenti quando viene assegnato il ruolo di proprietario del gruppo o di controllo delegato per il gruppo usato per concedere l'accesso alle applicazioni.

Gli amministratori possono configurare:

* [Esperienze di consenso,](../manage-apps/configure-user-consent.md)  incluse le condizioni per l'utilizzo del servizio.

* [Richieste di individuazione e accesso alle applicazioni self-service](../manage-apps/access-panel-manage-self-service-access.md).

* [Raccolte di applicazioni](../manage-apps/access-panel-collections.md).

* Assegnazione di icone alle applicazioni

* Nomi descrittivi per le applicazioni

* Informazioni di personalizzazione dell'azienda visualizzate App personali

 

## <a name="plan-consent-configuration"></a>Pianificare la configurazione del consenso

### <a name="user-consent-for-applications"></a>Consenso dell'utente per le applicazioni 

Prima che un utente possa accedere a un'applicazione e l'applicazione possa accedere ai dati dell'organizzazione, un utente o un amministratore deve concedere le autorizzazioni dell'applicazione. È possibile configurare se il consenso dell'utente è consentito e in quali condizioni. **Microsoft consiglia di consentire il consenso dell'utente solo per le applicazioni di editori verificati.**

Per altre informazioni, vedere Configurare [il consenso degli utenti finali alle applicazioni](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Consenso del proprietario del gruppo per le app che accedono ai dati

I proprietari di gruppi e team possono autorizzare le applicazioni, ad esempio le applicazioni pubblicate da fornitori di terze parti, ad accedere ai dati dell'organizzazione associati a un gruppo. Per [altre informazioni, vedere Consenso specifico delle risorse in Microsoft Teams.](https://docs.microsoft.com/microsoftteams/resource-specific-consent) 

È possibile specificare se si desidera consentire o disabilitare questa funzionalità.

Per altre informazioni, vedere Configurare [le autorizzazioni di consenso del gruppo.](../manage-apps/configure-user-consent-groups.md)

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Informare in modo proattivo gli utenti su come e quando cambierà l'esperienza e su come ottenere supporto se necessario.

Sebbene App personali non crei in genere problemi utente, è importante prepararsi. Creare guide e un elenco di tutte le risorse per il personale di supporto prima del lancio.

#### <a name="communications-templates"></a>Modelli di comunicazione

Microsoft offre [modelli personalizzabili per i messaggi di posta elettronica e altre comunicazioni per](https://aka.ms/APTemplates) App personali. È possibile adattare questi asset per l'uso in altri canali di comunicazione in base alla cultura aziendale.

 

## <a name="plan-your-sso-configuration"></a>Pianificare la configurazione dell'accesso Single Sign-On

È meglio se l'accesso Single Sign-On è abilitato per tutte le app nel portale di App personali in modo che gli utenti hanno un'esperienza facile senza dover immettere le proprie credenziali.

Azure AD supporta più opzioni SSO. 

* Per altre informazioni, vedere Opzioni di Single [Sign-On in Azure AD](sso-options.md).

* Per altre informazioni sull'uso Azure AD provider di identità per un'app, vedere la [serie di guide introduttive sulla gestione delle applicazioni.](../manage-apps/view-applications-portal.md)

### <a name="use-federated-sso-if-possible"></a>Se possibile, usare l'accesso SSO federato

Per una migliore esperienza utente con la App personali, iniziare con l'integrazione di applicazioni cloud disponibili per l'accesso SSO federato (OpenID Connect o SAML). L'accesso Single Sign-On federato consente agli utenti di avere un'esperienza coerente con un solo clic tra le superfici di avvio delle app e tende a essere più affidabile nel controllo della configurazione.

Per altre informazioni su come configurare le applicazioni SaaS (Software as a Service) per l'accesso SSO, vedere [Piano di distribuzione SSO SaaS].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Considerazioni per particolari circostanze dell'accesso Single Sign-On

> [!TIP]
> Per una migliore esperienza utente, usare l'accesso SSO federato con Azure AD (OpenID Connect/SAML) quando un'applicazione lo supporta, anziché l'accesso SSO basato su password e ADFS.

Per accedere alle applicazioni SSO basate su password o alle applicazioni a cui accede Azure AD Application Proxy, gli utenti devono installare e usare l'App personali di accesso sicuro. Agli utenti viene richiesto di installare l'estensione quando avviano per la prima volta l'applicazione SSO o Application Proxy basata su password. 

![Screenshot di](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Per informazioni dettagliate sull'estensione, vedere [Installazione dell App personali del browser.](../user-help/my-apps-portal-end-user-access.md)

Se è necessario integrare queste applicazioni, è necessario definire un meccanismo per distribuire l'estensione su larga scala con [i browser supportati.](../user-help/my-apps-portal-end-user-access.md) Le opzioni includono:

* [Download e configurazione guidati dall'utente per Chrome, Firefox, Microsoft Edge o IE](../user-help/my-apps-portal-end-user-access.md)

* [Gestione configurazione per Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

L'estensione consente agli utenti di avviare qualsiasi app dalla barra di ricerca, di trovare l'accesso alle applicazioni usate di recente e di avere un collegamento alla App personali pagina.

![Screenshot della ricerca dell'estensione App personali](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Pianificare l'accesso per dispositivi mobili

Per le applicazioni che usano l'accesso SSO basato su password o a cui si accede [Microsoft Azure AD Application Proxy](../manage-apps/application-proxy.md), è necessario usare Microsoft Edge dispositivi mobili. Per altre applicazioni, è possibile usare qualsiasi browser per dispositivi mobili. 

### <a name="linked-sso"></a>SSO collegato

Le applicazioni possono essere aggiunte usando l'opzione Linked SSO (SSO collegato). È possibile configurare un riquadro dell'applicazione che si collega all'URL dell'applicazione Web esistente. L'accesso SSO collegato consente di iniziare a indirizzare gli utenti al portale di App personali senza eseguire la migrazione di tutte le applicazioni Azure AD SSO. È possibile passare gradualmente alle Azure AD configurate per l'accesso Single Sign-On senza interrompere l'esperienza degli utenti.

## <a name="plan-the-user-experience"></a>Pianificare l'esperienza utente

Per impostazione predefinita, tutte le applicazioni a cui l'utente ha accesso e tutte le applicazioni configurate per l'individuazione self-service vengono visualizzate nel pannello App personali dell'utente. Per molte organizzazioni, può trattarsi di un elenco molto ampio, che può diventare oneroso se non organizzato

### <a name="plan-my-apps-collections"></a>Pianificare App personali raccolte

Ogni Azure AD a cui un utente ha accesso verrà visualizzata App personali nella raccolta Tutte le app. Usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendone più semplice l'individuazione. Ad esempio, è possibile usare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di processo, attività, progetti e così via specifici. 

Gli utenti finali possono anche personalizzare la propria esperienza

* Creazione di raccolte di app personali.

* [Nascondere e riordinare le raccolte di app](access-panel-collections.md).

![Screenshot della configurazione self-service](./media/my-apps-deployment-plan/collections.png)

È possibile nascondere le app dal portale App personali, consentendo comunque l'accesso da altre posizioni, ad esempio il portale Microsoft 365. Altre informazioni: [Nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> È possibile accedere solo a 950 app a cui un utente può accedere tramite App personali. Sono incluse le app nascoste dall'utente o dall'amministratore. 

### <a name="plan-self-service-group-management-membership"></a>Pianificare l'appartenenza alla gestione dei gruppi self-service

È possibile abilitare gli utenti per creare e gestire i propri gruppi di sicurezza o Microsoft 365 gruppi in Azure AD. Il proprietario del gruppo può approvare o negare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi self-service non sono disponibili per i gruppi di sicurezza o le liste di distribuzione abilitate per la posta elettronica.

Per pianificare l'appartenenza ai gruppi self-service, determinare se si consentirà a tutti gli utenti dell'organizzazione di creare e gestire gruppi o solo un subset di utenti. Se si consente un subset di utenti, è necessario configurare un gruppo a cui tali utenti vengono aggiunti. 

Per [informazioni dettagliate sull'abilitazione di](../enterprise-users/groups-self-service-management.md) questi scenari, vedere Configurare la gestione dei gruppi self-service in Azure Active Directory.

### <a name="plan-self-service-application-access"></a>Pianificare l'accesso alle applicazioni self-service

È possibile abilitare gli utenti per individuare e richiedere l'accesso alle applicazioni tramite il pannello App personali. A tale scopo, è prima necessario 

* abilitare la gestione self-service dei gruppi

* abilitare l'app per l'accesso SSO

* creare un gruppo per l'accesso all'applicazione

![Screenshot della configurazione App personali self-service](./media/my-apps-deployment-plan/my-apps-self-service.png)

Quando gli utenti richiedono l'accesso, richiedono l'accesso al gruppo sottostante e i proprietari del gruppo possono essere autorizzati a delegare l'autorizzazione per gestire l'appartenenza al gruppo e quindi l'accesso all'applicazione. I flussi di lavoro di approvazione sono disponibili per l'approvazione esplicita per accedere alle applicazioni. Gli utenti responsabili approvazione riceveranno notifiche all'interno App personali portale quando sono presenti richieste di accesso all'applicazione in sospeso.

## <a name="plan-reporting-and-auditing"></a>Pianificare la creazione di report e il controllo

Azure AD fornisce [report che offrono informazioni tecniche e aziendali].. /reports-monitoring/overview-reports.md). Collaborare con i proprietari di applicazioni tecniche e aziendali per assumere la proprietà di questi report e per utilizzare regolarmente i report. Nella tabella seguente vengono forniti alcuni esempi di scenari di creazione di report tipici.

| Esempio| per gestire i rischi.| Aumentare la produttività| Governance e conformità |
| - | - | - | -|
| Tipi di report| Autorizzazioni e utilizzo dell'applicazione| Attività di provisioning dell'account| Verificare chi accede alle applicazioni |
| Azioni potenziali| Controllare l'accesso; revocare le autorizzazioni| Correggere eventuali errori di provisioning| revoca dell'accesso |


Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni. I dati sono disponibili tramite il portale di amministrazione di Azure o l'API per il download nei sistemi di analisi.

#### <a name="auditing"></a>Controllo

I log di controllo per l'accesso alle applicazioni sono disponibili per 30 giorni. Se l'organizzazione richiede una conservazione più lunga, esportare i log in uno strumento SiEM (Security Information Event and Management), ad esempio Splunk o ArcSight.

Per i backup di controllo, creazione di report e ripristino di emergenza, documentare la frequenza di download richiesta, il sistema di destinazione e chi è responsabile della gestione di ogni backup. Potrebbe non essere necessario eseguire backup di controllo e creazione di report separati. Il backup di ripristino di emergenza deve essere un'entità separata.

## <a name="validate-your-deployment"></a>Convalidare la distribuzione

Assicurarsi che App personali la distribuzione sia testata e che sia stato eseguito un piano di rollback.

Eseguire i test seguenti sia con i dispositivi di proprietà dell'azienda che con i dispositivi personali. Questi test case devono riflettere anche i casi d'uso aziendali. Di seguito sono riportati alcuni casi basati su scenari tecnici tipici. Aggiungere altri elementi specifici per le proprie esigenze.

#### <a name="application-sso-access-test-case-examples"></a>Esempi di accesso SSO dell'applicazione test case seguenti:


| Caso aziendale| Risultato previsto |
| - | - |
| L'utente accede al portale App personali| L'utente può accedere e visualizzare le applicazioni |
| L'utente avvia un'applicazione SSO federata| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'applicazione SSO per la prima volta con password| L'utente deve installare l'App personali predefinita |
| L'utente avvia un'applicazione SSO con password a un momento successivo| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'app Microsoft 365 portale| L'utente ha eseguito automaticamente l'accesso all'applicazione |
| L'utente avvia un'app dal Managed Browser| L'utente ha eseguito automaticamente l'accesso all'applicazione |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Esempi di funzionalità self-service test case applicazioni


| Caso aziendale| Risultato previsto |
| - | - |
| L'utente può gestire l'appartenenza all'applicazione| L'utente può aggiungere/rimuovere membri che hanno accesso all'app |
| L'utente può modificare l'applicazione| L'utente può modificare la descrizione e le credenziali dell'applicazione per le applicazioni SSO con password |


### <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la configurazione SSO non riesce durante la distribuzione, è necessario comprendere come risolvere i [problemi di SSO](../hybrid/tshoot-connect-sso.md) e ridurre l'impatto sugli utenti. In casi estremi potrebbe essere necessario eseguire il rollback [dell'accesso SSO.](plan-sso-deployment.md)

## <a name="manage-your-implementation"></a>Gestire l'implementazione

Usare il ruolo con privilegi minimi per eseguire un'attività necessaria all'interno Azure Active Directory. [Esaminare i diversi ruoli disponibili e scegliere](../roles/permissions-reference.md) quello più giusto per soddisfare le proprie esigenze per ogni utente tipo per questa applicazione. Alcuni ruoli potrebbero dover essere applicati temporaneamente e rimossi dopo il completamento della distribuzione.

| Utenti tipo| Ruoli| Ruolo di Azure AD |
| - | - | - |
| Amministratore del supporto help desk| Supporto di livello 1| nessuno |
| Amministratore delle identità| Configurare ed eseguire il debug quando i problemi influiscono Azure AD| Amministratore globale |
| Amministratore dell'applicazione| Attestazione utente nell'applicazione, configurazione per gli utenti con autorizzazioni| nessuno |
| Amministratori dell'infrastruttura| Proprietario del rollover del certificato| Amministratore globale |
| Proprietario/stakeholder dell'azienda| Attestazione utente nell'applicazione, configurazione per gli utenti con autorizzazioni| nessuno |


È possibile [usare](../privileged-identity-management/pim-configure.md) Privileged Identity Management per gestire i ruoli per fornire un controllo, un controllo e una verifica di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione di Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Pianificare una distribuzione del proxy dell'applicazione](application-proxy-deployment-plan.md)

