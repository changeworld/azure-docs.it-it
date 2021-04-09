---
title: Pianificare Azure Active Directory la configurazione delle app personali
description: Guida alla pianificazione per usare in modo efficace le mie app nell'organizzazione.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 5184639d8c34be705aeeb691f1cf38486f850673
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543959"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Pianificare Azure Active Directory la configurazione delle app personali

> [!NOTE]
> Questo articolo è destinato ai professionisti IT che devono pianificare la configurazione del portale delle app personali dell'organizzazione. 
>
> **Per la documentazione dell'utente finale, vedere [accedere e avviare app dal portale app personali](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) app personali è un portale basato sul Web per l'avvio e la gestione delle app. La pagina App personali fornisce agli utenti un'unica posizione per avviare il lavoro e trovare tutte le applicazioni a cui hanno accesso. Gli utenti accedono alle app personali all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Perché configurare App personali

Per impostazione predefinita, il portale app personali è disponibile per gli utenti e non può essere disattivato. È importante configurarlo in modo da ottenere la migliore esperienza possibile e il portale rimane utile. 

Qualsiasi applicazione nell'elenco Azure Active Directory applicazioni aziendali viene visualizzata quando vengono soddisfatte entrambe le condizioni seguenti:

* La proprietà Visibility per l'app è impostata su true. 

* L'app viene assegnata a qualsiasi utente o gruppo. Viene visualizzato per gli utenti assegnati.

La configurazione del portale garantisce che le persone giuste possano trovare facilmente le app corrette.

 
### <a name="how-is-the-my-apps-portal-used"></a>Come viene usato il portale delle app personali?

Gli utenti accedono al portale app personali per:

* Individuare e accedere a tutte le applicazioni connesse all'Azure AD dell'organizzazione a cui hanno accesso.

   * È consigliabile assicurarsi che le app siano configurate per Single Sign-On (SSO) per offrire agli utenti la migliore esperienza.

* Richiedere l'accesso alle nuove app configurate per la gestione self-service.

* Creare raccolte personali di app.

* Consente di gestire l'accesso alle app per altri utenti quando è stato assegnato il ruolo di proprietario del gruppo o controllo delegato per il gruppo usato per concedere l'accesso alle applicazioni.

Gli amministratori possono configurare:

* [Esperienze di consenso](../manage-apps/configure-user-consent.md)  , incluse le condizioni del servizio.

* [Individuazione self-service delle applicazioni e richieste di accesso](../manage-apps/access-panel-manage-self-service-access.md).

* [Raccolte di applicazioni](../manage-apps/access-panel-collections.md).

* Assegnazione delle icone alle applicazioni

* Nomi descrittivi per le applicazioni

* Informazioni personalizzate distintive dell'azienda visualizzate nelle app personali

 

## <a name="plan-consent-configuration"></a>Pianificare la configurazione del consenso

### <a name="user-consent-for-applications"></a>Consenso dell'utente per le applicazioni 

Prima che un utente possa accedere a un'applicazione e l'applicazione possa accedere ai dati dell'organizzazione, un utente o un amministratore deve concedere le autorizzazioni dell'applicazione. È possibile specificare se il consenso dell'utente è consentito e in quali condizioni. **Microsoft consiglia di consentire solo il consenso dell'utente per le applicazioni da autori verificati.**

Per altre informazioni, vedere [configurare il modo in cui gli utenti finali accettano le applicazioni](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Consenso del proprietario del gruppo per le app che accedono ai dati

I proprietari di gruppi e team possono autorizzare le applicazioni, ad esempio le applicazioni pubblicate da fornitori di terze parti, ad accedere ai dati dell'organizzazione associati a un gruppo. Per altre informazioni, vedere il [consenso specifico per le risorse in Microsoft teams](https://docs.microsoft.com/microsoftteams/resource-specific-consent) . 

È possibile specificare se si vuole abilitare o disabilitare questa funzionalità.

Per altre informazioni, vedere [configurare le autorizzazioni di consenso del gruppo](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Informare in modo proattivo gli utenti su come e quando la loro esperienza cambierà e su come ottenere supporto se necessario.

Sebbene le app in genere non creino problemi degli utenti, è importante prepararsi. Creare guide e un elenco di tutte le risorse per il personale di supporto prima del lancio.

#### <a name="communications-templates"></a>Modelli di comunicazione

Microsoft offre [modelli personalizzabili per i messaggi di posta elettronica e altre comunicazioni](https://aka.ms/APTemplates) per le app personali. È possibile adattare questi asset per l'uso in altri canali di comunicazione in base alle impostazioni cultura aziendali.

 

## <a name="plan-your-sso-configuration"></a>Pianificare la configurazione di SSO

È consigliabile se SSO è abilitato per tutte le app nel portale app personali, in modo che gli utenti abbiano un'esperienza uniforme senza dover immettere le proprie credenziali.

Azure AD supporta più opzioni SSO. 

* Per altre informazioni, vedere [Opzioni di Single Sign-on in Azure ad](sso-options.md).

* Per altre informazioni sull'uso di Azure AD come provider di identità per un'app, vedere la [serie di guide introduttive sulla gestione delle applicazioni](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Usa SSO federato, se possibile

Per la migliore esperienza utente con la pagina App personali, iniziare con l'integrazione delle applicazioni cloud disponibili per SSO federato (OpenID Connect o SAML). L'accesso Single Sign-on federato consente agli utenti di disporre di un'esperienza con un clic coerente tra le aree di avvio delle app e tende a essere più affidabile nel controllo della configurazione.

Per ulteriori informazioni su come configurare le applicazioni SaaS (Software as a Service) per SSO, vedere [piano di distribuzione SSO SaaS]. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Considerazioni per particolari circostanze SSO

> [!TIP]
> Per un'esperienza utente migliore, usare SSO federato con Azure AD (OpenID Connect/SAML) quando un'applicazione la supporta, anziché SSO basato su password e ADFS.

Per accedere alle applicazioni SSO basate su password o alle applicazioni a cui è possibile accedere da Azure AD proxy di applicazione, gli utenti devono installare e usare l'estensione di accesso sicuro per le app personali. Agli utenti viene richiesto di installare l'estensione quando avviano per la prima volta l'applicazione SSO basata su password o il proxy di applicazione. 

![Screenshot di](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Per informazioni dettagliate sull'estensione, vedere [installazione dell'estensione del browser app personali](../user-help/my-apps-portal-end-user-access.md).

Se è necessario integrare queste applicazioni, è necessario definire un meccanismo per distribuire l'estensione su larga scala con i [browser supportati](../user-help/my-apps-portal-end-user-access.md). Le opzioni includono:

* [Download e configurazione basati sull'utente per Chrome, Firefox, Microsoft Edge o IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager per Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

L'estensione consente agli utenti di avviare qualsiasi app dalla relativa barra di ricerca, trovare l'accesso alle applicazioni utilizzate di recente e avere un collegamento alla pagina App personali.

![Screenshot della ricerca dell'estensione app personali](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Pianificare l'accesso mobile

Per le applicazioni che usano l'accesso Single Sign-on basato su password o a cui si accede tramite [Microsoft Azure ad proxy di applicazione](../manage-apps/application-proxy.md), è necessario usare Microsoft Edge mobile. Per altre applicazioni, è possibile usare qualsiasi browser per dispositivi mobili. 

### <a name="linked-sso"></a>SSO collegato

È possibile aggiungere applicazioni utilizzando l'opzione collegata SSO. È possibile configurare un riquadro dell'applicazione che si collega all'URL dell'applicazione Web esistente. SSO collegato consente di iniziare a indirizzare gli utenti al portale app personali senza eseguire la migrazione di tutte le applicazioni a Azure AD SSO. È possibile spostarsi gradualmente in Azure AD applicazioni configurate con SSO senza compromettere l'esperienza degli utenti.

## <a name="plan-the-user-experience"></a>Pianificare l'esperienza utente

Per impostazione predefinita, tutte le applicazioni a cui l'utente ha accesso e tutte le applicazioni configurate per l'individuazione Self-Service vengono visualizzate nel pannello app personali dell'utente. Per molte organizzazioni, può trattarsi di un elenco molto grande, che può diventare gravoso se non organizzato

### <a name="plan-my-apps-collections"></a>Pianificare le raccolte di app

Ogni Azure AD applicazione a cui un utente ha accesso verrà visualizzata nelle app personali nella raccolta tutte le app. Usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendole più facili da trovare. È ad esempio possibile utilizzare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di processo, attività, progetti e così via specifici. 

Gli utenti finali possono anche personalizzare la propria esperienza

* Creazione di raccolte di app personalizzate.

* [Nascondere e riordinare le raccolte di app](access-panel-collections.md).

![Screenshot della configurazione self-service](./media/my-apps-deployment-plan/collections.png)

È disponibile un'opzione che consente di nascondere le app dal portale app personali, pur continuando ad accedere da altre posizioni, ad esempio il portale di Microsoft 365. Altre informazioni: [nascondere un'applicazione dall'esperienza dell'utente in Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Solo le app di 950 a cui un utente può accedere possono accedere tramite app personali. Sono incluse le app nascoste dall'utente o dall'amministratore. 

### <a name="plan-self-service-group-management-membership"></a>Pianificare l'appartenenza alla gestione dei gruppi self-service

È possibile consentire agli utenti di creare e gestire i propri gruppi di sicurezza o gruppi di Microsoft 365 in Azure AD. Il proprietario del gruppo può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi self-service non sono disponibili per gruppi di sicurezza abilitati alla posta elettronica o liste di distribuzione.

Per pianificare l'appartenenza al gruppo self-service, determinare se si consentirà a tutti gli utenti dell'organizzazione di creare e gestire gruppi o solo un subset di utenti. Se si consente un subset di utenti, è necessario configurare un gruppo a cui aggiungere tali utenti. 

Per informazioni dettagliate sull'abilitazione di questi scenari, vedere [configurare la gestione dei gruppi in modalità self-service in Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

### <a name="plan-self-service-application-access"></a>Pianificare l'accesso alle applicazioni self-service

È possibile consentire agli utenti di individuare e richiedere l'accesso alle applicazioni tramite il pannello app personali. A tale scopo, è necessario prima 

* abilitare la gestione dei gruppi self-service

* abilitare l'app per SSO

* creare un gruppo per l'accesso alle applicazioni

![Screenshot della configurazione self-service delle app](./media/my-apps-deployment-plan/my-apps-self-service.png)

Quando gli utenti richiedono l'accesso, richiedono l'accesso al gruppo sottostante e i proprietari del gruppo possono delegare l'autorizzazione per gestire l'appartenenza al gruppo e quindi l'accesso alle applicazioni. I flussi di lavoro di approvazione sono disponibili per l'approvazione esplicita per l'accesso alle applicazioni. Gli utenti che sono responsabili approvazione riceveranno le notifiche nel portale app personali quando ci sono richieste in sospeso per l'accesso all'applicazione.

## <a name="plan-reporting-and-auditing"></a>Pianificare la creazione di report e il controllo

Azure AD fornisce [report che offrono informazioni tecniche e aziendali]. /reports-monitoring/overview-reports.md). Collaborare con i proprietari delle applicazioni aziendali e tecniche per assumere la proprietà di questi report e per utilizzarli regolarmente. Nella tabella seguente vengono forniti alcuni esempi di scenari di Reporting tipici.

| Esempio| per gestire i rischi.| Aumentare la produttività| Governance e conformità |
| - | - | - | -|
| Tipi di report| Autorizzazioni e utilizzo dell'applicazione| Attività di provisioning dell'account| Esaminare gli utenti che accedono alle applicazioni |
| Azioni potenziali| Controllare l'accesso; revoca autorizzazioni| Correggere gli errori di provisioning| revoca dell'accesso |


Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni. I dati sono disponibili tramite il portale di amministrazione di Azure o l'API da scaricare nei sistemi di analisi.

#### <a name="auditing"></a>Controllo

I log di controllo per l'accesso alle applicazioni sono disponibili per 30 giorni. Se l'organizzazione richiede un periodo di conservazione più lungo, esportare i log in uno strumento SIEM (Security Information Event and Management), ad esempio Splunk o ArcSight.

Per il controllo, la creazione di report e i backup di ripristino di emergenza, documentare la frequenza di download richiesta, il sistema di destinazione e chi è responsabile della gestione di ogni backup. Potrebbero non essere necessari backup distinti per il controllo e la creazione di report. Il backup del ripristino di emergenza deve essere un'entità separata.

## <a name="validate-your-deployment"></a>Convalidare la distribuzione

Assicurarsi che la distribuzione di app personali sia testata accuratamente e che sia presente un piano di rollback.

Eseguire i test seguenti con i dispositivi di proprietà dell'azienda e i dispositivi personali. Questi test case dovrebbero anche riflettere i casi d'uso aziendali. Di seguito sono riportati alcuni casi basati su scenari tecnici tipici. Aggiungere altri utenti specifici per le proprie esigenze.

#### <a name="application-sso-access-test-case-examples"></a>Esempi di accesso SSO applicazione test case:


| Caso aziendale| Risultato previsto |
| - | - |
| L'utente accede al portale app personali| L'utente può accedere e visualizzare le applicazioni |
| L'utente avvia un'applicazione SSO federata| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'applicazione con password SSO per la prima volta| L'utente deve installare l'estensione My Apps |
| L'utente avvia un'applicazione con password SSO una volta successiva| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal portale di Microsoft 365| L'utente viene connesso automaticamente all'applicazione |
| L'utente avvia un'app dal Managed Browser| L'utente viene connesso automaticamente all'applicazione |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Esempi di funzionalità self-service per le applicazioni test case


| Caso aziendale| Risultato previsto |
| - | - |
| L'utente può gestire l'appartenenza all'applicazione| L'utente può aggiungere/rimuovere membri che hanno accesso all'app |
| L'utente può modificare l'applicazione| L'utente può modificare la descrizione e le credenziali dell'applicazione per le applicazioni SSO con password |


### <a name="rollback-steps"></a>Procedura di ripristino dello stato precedente

È importante pianificare le operazioni da eseguire se la distribuzione non viene completata come previsto. Se la configurazione SSO ha esito negativo durante la distribuzione, è necessario comprendere come [risolvere i problemi relativi a SSO](../hybrid/tshoot-connect-sso.md) e ridurre l'effetto sugli utenti. In casi estremi, potrebbe essere necessario eseguire il [rollback di SSO](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Gestire l'implementazione

Utilizzare il ruolo con privilegi minimi per eseguire un'attività obbligatoria all'interno Azure Active Directory. [Esaminare i diversi ruoli disponibili](../roles/permissions-reference.md) e scegliere quello più adatto per soddisfare le proprie esigenze per ogni utente di questa applicazione. È possibile che alcuni ruoli debbano essere applicati temporaneamente e rimossi al termine della distribuzione.

| Utenti tipo| Ruoli| Ruolo di Azure AD |
| - | - | - |
| Amministratore helpdesk| Supporto di livello 1| nessuno |
| Amministratore identità| Configurare ed eseguire il debug quando i problemi hanno effetto Azure AD| Amministratore globale |
| Amministratore applicazione| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| nessuno |
| Amministratori dell'infrastruttura| Proprietario del rollover del certificato| Amministratore globale |
| Proprietario/stakeholder aziendale| Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni| nessuno |


È possibile usare [Privileged Identity Management](../privileged-identity-management/pim-configure.md) per gestire i ruoli per fornire controllo, controllo e verifica di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione di Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Pianificare una distribuzione del proxy dell'applicazione](application-proxy-deployment-plan.md)

