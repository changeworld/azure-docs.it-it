---
title: Pianificare una Azure Active Directory Application Proxy distribuzione
description: Guida end-to-end per la pianificazione della distribuzione del proxy di applicazione all'interno dell'organizzazione
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: b0a3653d2cc840d745b1bb5788406b8d374c76d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533782"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Pianificare una distribuzione di Azure AD Application Proxy

Azure Active Directory (Azure AD) Application Proxy è una soluzione di accesso remoto sicura ed economica per le applicazioni locali. Offre un percorso di transizione immediato per le organizzazioni "Cloud First" per gestire l'accesso alle applicazioni locali legacy che non sono ancora in grado di usare protocolli moderni. Per altre informazioni introduttive, vedere [Informazioni Application Proxy](./application-proxy.md).

Application Proxy è consigliabile concedere agli utenti remoti l'accesso alle risorse interne. Application Proxy la necessità di una VPN o di un proxy inverso per questi casi d'uso dell'accesso remoto. Non è destinato agli utenti che si trova nella rete aziendale. Questi utenti che usano Application Proxy per l'accesso Intranet possono verificarsi problemi di prestazioni indesiderati.

Questo articolo include le risorse necessarie per pianificare, gestire e gestire Azure AD Application Proxy.

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

La sezione seguente offre un'ampia visualizzazione degli elementi chiave di pianificazione che verranno impostati per un'esperienza di distribuzione efficiente.

### <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di iniziare l'implementazione. In questa esercitazione sono disponibili altre informazioni sulla configurazione dell'ambiente, inclusi questi [prerequisiti.](application-proxy-add-on-premises-application.md)

* **Connettori:** i connettori sono agenti leggeri che è possibile distribuire in:
   * Hardware fisico locale
   * Una macchina virtuale ospitata all'interno di qualsiasi soluzione hypervisor
   * Una macchina virtuale ospitata in Azure per abilitare la connessione in uscita al Application Proxy servizio.

* Vedere [Informazioni App Azure AD connettori proxy per](application-proxy-connectors.md) una panoramica più dettagliata.

     * Prima di installare i connettori, è necessario che i computer del connettore siano abilitati per [TLS 1.2.](application-proxy-add-on-premises-application.md)

     * Se possibile, distribuire i connettori nella [stessa rete e](application-proxy-network-topology.md) nello stesso segmento dei server applicazioni Web back-end. È meglio distribuire i connettori dopo aver completato l'individuazione delle applicazioni.
     * È consigliabile che ogni gruppo di connettori abbia almeno due connettori per offrire disponibilità elevata e scalabilità. La presenza di tre connettori è ottimale nel caso in cui sia necessario eseguire il servizio di un computer in qualsiasi momento. Esaminare la [tabella della capacità del](./application-proxy-connectors.md#capacity-planning) connettore per decidere il tipo di computer in cui installare i connettori. Più grande è il computer, maggiore sarà il buffer e le prestazioni del connettore.

* **Impostazioni di accesso alla** rete: Azure AD Application Proxy connettori si connettono ad Azure tramite [HTTPS (porta TCP 443) e HTTP (porta TCP 80).](application-proxy-add-on-premises-application.md)

   * La terminazione del traffico TLS del connettore non è supportata e impedisce ai connettori di stabilire un canale sicuro con i app Azure endpoint proxy.

   * Evitare tutte le forme di ispezione inline sulle comunicazioni TLS in uscita tra i connettori e Azure. L'ispezione interna tra un connettore e le applicazioni back-end è possibile, ma potrebbe peggiorare l'esperienza utente e, di conseguenza, non è consigliabile.

   * Anche il bilanciamento del carico dei connettori stessi non è supportato né necessario.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerazioni importanti prima di configurare Azure AD Application Proxy

Per configurare e implementare Azure AD Application Proxy, è necessario soddisfare i requisiti di base seguenti.

*  **Onboarding di Azure:** prima di distribuire il proxy dell'applicazione, le identità utente devono essere sincronizzate da una directory locale o create direttamente all'interno Azure AD tenant. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate tramite proxy di applicazione e di ottenere le informazioni degli ID utente necessarie per eseguire l'accesso Single Sign-On (SSO).

* **Requisiti per l'accesso** condizionale: non è consigliabile usare Application Proxy per l'accesso Intranet perché ciò aggiunge una latenza che inciderà sugli utenti. È consigliabile usare Application Proxy criteri di pre-autenticazione e accesso condizionale per l'accesso remoto da Internet.  Un approccio per fornire l'accesso condizionale per l'uso intranet consiste nel modernizzare le applicazioni in modo che possano eseguire direttamente l'autenticazione con AAD. Per altre informazioni, vedere Risorse per la migrazione di applicazioni [ad AAD.](./migration-resources.md)

* **Limiti del servizio:** per proteggersi dall'e sovraconsunzione delle risorse da parte dei singoli tenant, sono impostati limiti di limitazione per ogni applicazione e tenant. Per visualizzare questi limiti, fare riferimento [Azure AD e restrizioni del servizio.](../enterprise-users/directory-service-limits-restrictions.md) Questi limiti di limitazione si basano su un benchmark molto superiore al volume di utilizzo tipico e forniscono un ampio buffer per la maggior parte delle distribuzioni.

* **Certificato pubblico:** se si usano nomi di dominio personalizzati, è necessario ottenere un certificato TLS/SSL. A seconda dei requisiti dell'organizzazione, ottenere un certificato può richiedere del tempo ed è consigliabile iniziare il processo il prima possibile. applicazione Azure Proxy supporta certificati standard, [con caratteri](application-proxy-wildcard.md)jolly o basati su SAN. Per altre informazioni, vedere [Configurare domini personalizzati con Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

* Requisiti **di** dominio: l'accesso Single Sign-On alle applicazioni pubblicate tramite delega vincolata Kerberos richiede che il server che esegue il connettore e il server che esegue l'app siano aggiunti a un dominio e che fanno parte dello stesso dominio o domini trusting.
Per informazioni dettagliate sull'argomento, vedere [KCD per l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) con Application Proxy. Il servizio connettore viene eseguito nel contesto del sistema locale e non deve essere configurato per l'uso di un'identità personalizzata.

* **Record DNS per gli URL**

   * Prima di usare domini personalizzati in Application Proxy è necessario creare un record CNAME nel DNS pubblico, consentendo ai client di risolvere l'URL esterno definito personalizzato nell'indirizzo Application Proxy predefinito. La mancata creazione di un record CNAME per un'applicazione che usa un dominio personalizzato impedirà agli utenti remoti di connettersi all'applicazione. I passaggi necessari per aggiungere record CNAME possono variare [da](../../dns/dns-operations-recordsets-portal.md)provider DNS a provider, quindi informazioni su come gestire record e set di record DNS usando il portale di Azure .

   * Analogamente, gli host del connettore devono essere in grado di risolvere l'URL interno delle applicazioni pubblicate.

* **Diritti e ruoli amministrativi**

   * **L'installazione** del connettore richiede diritti di amministratore locale per il server Windows in cui viene installato. È inoltre necessario almeno un ruolo Amministratore *applicazioni* per autenticare e registrare l'istanza del connettore nel tenant Azure AD servizio.

   * **Per la pubblicazione e l'amministrazione delle** applicazioni è necessario *il ruolo Amministratore* applicazioni. Gli amministratori delle applicazioni possono gestire tutte le applicazioni nella directory, tra cui registrazioni, impostazioni SSO, assegnazioni di utenti e gruppi e licenze, Application Proxy impostazioni e consenso. Non consente tuttavia di gestire l'accesso condizionale. Il *ruolo Amministratore applicazioni cloud* ha tutte le capacità dell'amministratore dell'applicazione, ad eccezione del fatto che non consente la gestione delle Application Proxy predefinite.

* **Licenze:** Application Proxy è disponibile tramite una sottoscrizione Azure AD Premium servizio. Fare riferimento alla pagina [Azure Active Directory Prezzi per](https://azure.microsoft.com/pricing/details/active-directory/) un elenco completo delle opzioni di licenza e delle funzionalità.

### <a name="application-discovery"></a>Individuazione applicazioni

Compilare un inventario di tutte le applicazioni nell'ambito pubblicate tramite Application Proxy raccogliendo le informazioni seguenti:

| Information Type| Informazioni da raccogliere |
|---|---|
| Tipo di servizio| Ad esempio: SharePoint, SAP, CRM, applicazione Web personalizzata, API |
| Piattaforma applicativa | Ad esempio: IIS di Windows, Apache in Linux, Tomcat, NGINX |
| Appartenenza al dominio| Nome di dominio completo (FQDN) del server Web |
| Percorso dell'applicazione | Posizione del server Web o della farm nell'infrastruttura |
| Accesso interno | URL esatto usato per l'accesso interno all'applicazione. <br> Se una farm, quale tipo di bilanciamento del carico è in uso? <br> Indica se l'applicazione disegna contenuto da origini diverse da se stessa.<br> Determinare se l'applicazione opera su WebSockets. |
| Accesso esterno | Soluzione del fornitore a cui l'applicazione è già esposta esternamente. <br> URL da usare per l'accesso esterno. Se SharePoint, assicurarsi che i mapping di accesso alternativo siano configurati in base [a queste indicazioni.](/SharePoint/administration/configure-alternate-access-mappings) In caso contrario, sarà necessario definire URL esterni. |
| Certificato pubblico | Se si usa un dominio personalizzato, ottenere un certificato con un nome soggetto corrispondente. se esiste un certificato, prendere nota del numero di serie e della posizione da cui è possibile ottenere il certificato. |
| Tipo di autenticazione| Tipo di autenticazione supportato dall'applicazione, ad esempio Basic, Windows Integration Authentication, basata su form, basata su intestazione e attestazioni. <br>Se l'applicazione è configurata per l'esecuzione con un account di dominio specifico, prendere nota del nome di dominio completo (FQDN) dell'account del servizio.<br> Se basato su SAML, l'identificatore e gli URL di risposta. <br> Se basato su intestazione, la soluzione del fornitore e il requisito specifico per la gestione del tipo di autenticazione. |
| Nome del gruppo di connettori | Nome logico per il gruppo di connettori che verranno designati per fornire il condotto e l'accesso SSO a questa applicazione back-end. |
| Accesso a utenti/gruppi | Utenti o gruppi di utenti a cui verrà concesso l'accesso esterno all'applicazione. |
| Requisiti aggiuntivi | Prendere nota di eventuali requisiti aggiuntivi di accesso remoto o di sicurezza da tenere in considerazione per la pubblicazione dell'applicazione. |

È possibile scaricare questo foglio di [calcolo dell'inventario delle applicazioni](https://aka.ms/appdiscovery) per eseguire l'inventario delle app.

### <a name="define-organizational-requirements"></a>Definire i requisiti dell'organizzazione

Di seguito sono riportate le aree per le quali è necessario definire i requisiti aziendali dell'organizzazione. Ogni area contiene esempi di requisiti

 **Accesso**

* Gli utenti remoti con dispositivi aggiunti a Azure AD o aggiunti a un dominio possono accedere in modo sicuro alle applicazioni pubblicate con l'accesso Single Sign-On (SSO).

* Gli utenti remoti con dispositivi personali approvati possono accedere in modo sicuro alle applicazioni pubblicate a condizione che siano registrati in MFA e che l'app Microsoft Authenticator sia registrata nel telefono cellulare come metodo di autenticazione.

**Governance**

* Gli amministratori possono definire e monitorare il ciclo di vita delle assegnazioni utente alle applicazioni pubblicate tramite Application Proxy.

**Sicurezza**

* Solo gli utenti assegnati alle applicazioni tramite l'appartenenza a gruppi o singolarmente possono accedere a tali applicazioni.

**Prestazioni**

* Non si verifica alcuna riduzione delle prestazioni dell'applicazione rispetto all'accesso all'applicazione dalla rete interna.

**Esperienza utente**

* Gli utenti sono a conoscenza di come accedere alle proprie applicazioni usando gli URL aziendali familiari in qualsiasi piattaforma di dispositivi.

**Controllo**
* Gli amministratori possono controllare l'attività di accesso degli utenti.


### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota

Determinare il tempo e l'impegno necessari per la completa messa in servizio di una singola applicazione per l'accesso remoto con Single Sign-On (SSO). A tale scopo, eseguire un progetto pilota che consideri l'individuazione iniziale, la pubblicazione e i test generali. L'uso di una semplice applicazione Web basata su IIS già preconfigurata per autenticazione integrata di Windows (IWA) consente di stabilire una baseline, perché questa configurazione richiede un impegno minimo per pilotare correttamente l'accesso remoto e l'accesso SSO.

Gli elementi di progettazione seguenti dovrebbero aumentare il successo dell'implementazione pilota direttamente in un tenant di produzione.

**Gestione dei connettori:**

* I connettori svolgono un ruolo chiave nel fornire il condotto locale alle applicazioni. **L'uso del gruppo di** connettori predefinito è adeguato per i test pilota iniziali delle applicazioni pubblicate prima della messa in produzione. Le applicazioni testate correttamente possono quindi essere spostate in gruppi di connettori di produzione.

**Gestione delle applicazioni:**

* È molto probabile che la forza lavoro ricordi che un URL esterno è familiare e pertinente. Evitare di pubblicare l'applicazione usando i suffissi msappproxy.net o onmicrosoft.com predefiniti. Fornire invece un dominio verificato di primo livello familiare, preceduto da un nome host logico, ad esempio *intranet.<customers_domain>.com*.

* Limitare la visibilità dell'icona dell'applicazione pilota a un gruppo pilota nascondendo l'icona di avvio nel portale di Azure MyApps. Quando si è pronti per l'ambiente di produzione, è possibile impostare l'ambito dell'app ai rispettivi destinatari di destinazione, nello stesso tenant di pre-produzione o pubblicando anche l'applicazione nel tenant di produzione.

Impostazioni di Single **Sign-On:** alcune impostazioni SSO hanno dipendenze specifiche che possono richiedere tempo per la configurazione, quindi evitare ritardi di controllo delle modifiche assicurando che le dipendenze siano indirizzate in anticipo. Ciò include l'aggiunta a un dominio host del connettore per eseguire l'accesso SSO usando la delega vincolata Kerberos (KCD) e la gestione di altre attività dispendiose in termini di tempo. Ad esempio, Configurazione di un'istanza di accesso PING, se è necessario l'accesso SSO basato su intestazione.

**TLS tra l'host del connettore** e l'applicazione di destinazione: la sicurezza è fondamentale, quindi è consigliabile usare sempre TLS tra l'host del connettore e le applicazioni di destinazione. In particolare, se l'applicazione Web è configurata per l'autenticazione basata su form( FBA), poiché le credenziali utente vengono trasmesse in modo efficace in testo non crittografato.

**Implementare in modo incrementale e testare ogni passaggio**.
Eseguire test funzionali di base dopo la pubblicazione di un'applicazione per assicurarsi che tutti i requisiti aziendali e utente siano soddisfatti seguendo le istruzioni seguenti:

1. Testare e convalidare l'accesso generale all'applicazione Web con la pre-autenticazione disabilitata.
2. In caso di esito positivo, abilitare la pre-autenticazione e assegnare utenti e gruppi. Testare e convalidare l'accesso.
3. Aggiungere quindi il metodo SSO per l'applicazione e testare di nuovo per convalidare l'accesso.
4. Applicare i criteri di accesso condizionale e MFA in base alle esigenze. Testare e convalidare l'accesso.

**Strumenti per la risoluzione** dei problemi: quando si esegue la risoluzione dei problemi, iniziare sempre convalidando l'accesso all'applicazione pubblicata dal browser nell'host del connettore e verificare che funzioni come previsto. Più semplice è la configurazione, più facile da determinare la causa radice, quindi è consigliabile provare a riprodurre i problemi con una configurazione minima, ad esempio l'uso di un solo connettore e nessun accesso SSO. In alcuni casi, gli strumenti di debug Web come Fiddler di Telerik possono rivelarsi indispensabili per risolvere i problemi di accesso o di contenuto nelle applicazioni a cui si accede tramite un proxy. Fiddler può anche fungere da proxy per tracciare ed eseguire il debug del traffico per piattaforme mobili come iOS e Android e praticamente qualsiasi elemento che possa essere configurato per il routing tramite un proxy. Per altre [informazioni, vedere](application-proxy-troubleshoot.md) la guida alla risoluzione dei problemi.

## <a name="implement-your-solution"></a>Implementare la soluzione

### <a name="deploy-application-proxy"></a>Distribuire Application Proxy

I passaggi per distribuire il Application Proxy sono descritti in questa esercitazione per l'aggiunta di un'applicazione locale per [l'accesso remoto.](application-proxy-add-on-premises-application.md) Se l'installazione non riesce, selezionare Risoluzione dei Application Proxy nel portale o usare la guida alla risoluzione dei problemi per Problemi relativi all'installazione di [Application Proxy Agent Connector](application-proxy-connector-installation-problem.md). 

### <a name="publish-applications-via-application-proxy"></a>Pubblicare applicazioni tramite Application Proxy

La pubblicazione di applicazioni presuppone che siano stati soddisfatti tutti i prerequisiti e che siano presenti diversi connettori che vengono visualizzati come registrati e attivi nella pagina Application Proxy.

È anche possibile pubblicare applicazioni usando [PowerShell.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)

Di seguito sono riportate alcune procedure consigliate da seguire quando si pubblica un'applicazione:

* **Usa gruppi di connettori:** assegnare un gruppo di connettori designato per la pubblicazione di ogni rispettiva applicazione. È consigliabile che ogni gruppo di connettori abbia almeno due connettori per offrire disponibilità elevata e scalabilità. La presenza di tre connettori è ottimale nel caso in cui sia necessario eseguire il servizio di un computer in qualsiasi momento. Vedere anche [Pubblicare](application-proxy-connector-groups.md) applicazioni in reti e posizioni separate usando gruppi di connettori per vedere come è anche possibile usare i gruppi di connettori per segmentare i connettori in base alla rete o alla posizione.

* **Imposta timeout applicazione back-end:** questa impostazione è utile negli scenari in cui l'applicazione potrebbe richiedere più di 75 secondi per elaborare una transazione client. Ad esempio, quando un client invia una query a un'applicazione Web che funge da front-end a un database. Il front-end invia la query al server di database back-end e attende una risposta, ma quando riceve una risposta, il lato client della conversazione si timeout. L'impostazione del timeout su Long fornisce 180 secondi per il completamento di transazioni più lunghe.

* **Usare i tipi di cookie appropriati**

   * **Cookie solo HTTP:** offre una sicurezza aggiuntiva Application Proxy includere il flag HTTPOnly nelle intestazioni di risposta HTTP set-cookie. Questa impostazione consente di attenuare gli exploit, ad esempio lo scripting tra siti (XSS). Lasciare impostato su No per i client/agenti utente che richiedono l'accesso al cookie di sessione. Ad esempio, il client RDP/MTSC che si connette a un Desktop remoto gateway pubblicato tramite proxy app.

   * **Cookie sicuro:** quando un cookie viene impostato con l'attributo Secure, l'agente utente (app lato client) includerà il cookie nelle richieste HTTP solo se la richiesta viene trasmessa su un canale protetto TLS. In questo modo è possibile ridurre il rischio di compromissione di un cookie tramite canali di testo non crittografato, pertanto è consigliabile attivarne l'azione.

   * **Cookie persistente:** consente Application Proxy cookie di sessione permanente tra le chiusure del browser rimanendo valido fino alla scadenza o all'eliminazione. Usato per scenari in cui un'applicazione avanzata, ad esempio Office, accede a un documento all'interno di un'applicazione Web pubblicata, senza che all'utente venga richiesta nuovamente l'autenticazione. Abilitare tuttavia con cautela, poiché i cookie persistenti possono in definitiva lasciare un servizio a rischio di accesso non autorizzato, se non usato in combinazione con altri controlli di compensazione. Questa impostazione deve essere usata solo per le applicazioni meno recenti che non possono condividere cookie tra processi. È meglio aggiornare l'applicazione per gestire la condivisione dei cookie tra processi anziché usare questa impostazione.

* **Tradurre GLI URL nelle** intestazioni: questa opzione viene abilitata per gli scenari in cui non è possibile configurare DNS interno in modo che corrisponda allo spazio dei nomi pubblico dell'organizzazione(ovvero dns di divisione). A meno che l'applicazione non richieda l'intestazione host originale nella richiesta client, lasciare questo valore impostato su Sì. L'alternativa consiste nel fare in modo che il connettore usi l'FQDN nell'URL interno per il routing del traffico effettivo e il nome di dominio completo nell'URL esterno, come intestazione host. Nella maggior parte dei casi questa alternativa deve consentire all'applicazione di funzionare normalmente, quando si accede in modalità remota, ma gli utenti perdono i vantaggi di avere una corrispondenza all'interno dell'URL & url esterno.

* **Tradurre gli URL nel** corpo dell'applicazione: attivare la traduzione dei collegamenti del corpo dell'applicazione per un'app quando si vuole che i collegamenti di tale app siano tradotti nelle risposte al client. Se abilitata, questa funzione consente di tradurre al meglio tutti i collegamenti interni trovati da Proxy app nelle risposte HTML e CSS restituite ai client. È utile quando si pubblicano app che contengono collegamenti con nome breve Assoluto o NetBIOS hard coded nel contenuto o app con contenuto che si collega ad altre applicazioni locali.

Per gli scenari in cui un'app pubblicata si collega ad altre app pubblicate, abilitare la traduzione dei collegamenti per ogni applicazione in modo da avere il controllo sull'esperienza utente a livello di app.

Si supponga, ad esempio, di avere tre applicazioni pubblicate tramite Application Proxy che si collegano tra loro: Vantaggi, Spese e Viaggi, oltre a una quarta app, Feedback, che non viene pubblicata tramite Application Proxy.

![Figura 1](media/App-proxy-deployment-plan/link-translation.png)

Quando si abilita la traduzione dei collegamenti per l'app Benefits, i collegamenti a Spese e viaggi vengono reindirizzati agli URL esterni per tali app, in modo che gli utenti che accedono alle applicazioni dall'esterno della rete aziendale possano accedervi. I collegamenti da Expenses e Travel back to Benefits non funzionano perché la traduzione dei collegamenti non è stata abilitata per queste due app. Il collegamento a Feedback non viene reindirizzato perché non è presente alcun URL esterno, quindi gli utenti che usano l'app Benefits non potranno accedere all'app di feedback dall'esterno della rete aziendale. Vedere informazioni dettagliate sulla traduzione [dei collegamenti e altre opzioni di reindirizzamento.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Accedere all'applicazione

Esistono diverse opzioni per la gestione dell'accesso alle risorse pubblicate del proxy app, quindi scegliere la più appropriata per lo scenario e le esigenze di scalabilità specificate. Gli approcci comuni includono l'uso di gruppi locali sincronizzati tramite Azure AD Connect, la creazione di gruppi dinamici in Azure AD in base agli attributi utente, l'uso di gruppi self-service gestiti da un proprietario di risorse o una combinazione di tutti questi elementi. Per i vantaggi di ognuna, vedere le risorse collegate.

Il modo più semplice per assegnare agli utenti  l'accesso a un'applicazione è passare alle opzioni Utenti e gruppi nel riquadro a sinistra dell'applicazione pubblicata e assegnare direttamente gruppi o singoli utenti.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

È anche possibile consentire agli utenti di accedere in modalità self-service all'applicazione assegnando un gruppo di cui non sono attualmente membri e configurando le opzioni self-service.

![Immagine 25](media/App-proxy-deployment-plan/allow-access.png)

Se abilitata, gli utenti potranno quindi accedere al portale Delle app e richiedere l'accesso, essere approvati automaticamente e aggiunti al gruppo self-service già autorizzato o richiedere l'approvazione di un responsabile approvazione designato.

Gli utenti guest possono anche essere invitati ad accedere alle applicazioni interne pubblicate [tramite Application Proxy tramite Azure AD B2B.](../external-identities/add-users-information-worker.md)

Per le applicazioni locali normalmente accessibili in modo anonimo e che non richiedono l'autenticazione, è preferibile disabilitare l'opzione disponibile in **Proprietà dell'applicazione.**

![Immagine 26](media/App-proxy-deployment-plan/assignment-required.png)


Lasciare questa opzione impostata su No consente agli utenti di accedere all'applicazione locale tramite App Azure AD Proxy senza autorizzazioni, quindi usare con cautela.

Dopo la pubblicazione, l'applicazione dovrebbe essere accessibile digitandone l'URL esterno in un browser o tramite la relativa icona in [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Abilitare la pre-autenticazione

Verificare che l'applicazione sia accessibile Application Proxy accedervi tramite l'URL esterno.

1. Passare a **Azure Active Directory**  >  **applicazioni aziendali** Tutte le  >  **applicazioni** e scegliere l'app da gestire.

2. Selezionare **Proxy dell'applicazione**.

3. Nel campo **Pre-autenticazione** usare l'elenco a discesa per **selezionare** Azure Active Directory e selezionare **Salva**.

Con la pre-autenticazione abilitata, Azure AD sfiderà prima gli utenti per l'autenticazione e se è configurato l'accesso Single Sign-On, l'applicazione back-end verificherà anche l'utente prima che venga concesso l'accesso all'applicazione. La modifica della modalità di pre-autenticazione da Passthrough a Azure AD configura anche l'URL esterno con HTTPS, quindi qualsiasi applicazione configurata inizialmente per HTTP verrà ora protetta con HTTPS.

### <a name="enable-single-sign-on"></a>Abilitazione dell'accesso Single Sign-On

L'accesso Single Sign-On offre la migliore esperienza utente possibile e la sicurezza, perché gli utenti devono accedere una sola volta quando accedono Azure AD. Dopo la pre-autenticazione di un utente, l'accesso SSO viene eseguito dal connettore Application Proxy che esegue l'autenticazione all'applicazione locale, per conto dell'utente. L'applicazione back-end elabora l'account di accesso come se fosse l'utente stesso.

La scelta **dell'opzione Pass-through** consente agli utenti di accedere all'applicazione pubblicata senza dover eseguire l'autenticazione Azure AD.

L'esecuzione dell'accesso Single Sign-On è possibile solo se Azure AD è in grado di identificare l'utente che richiede l'accesso a una risorsa, quindi l'applicazione deve essere configurata per pre-autenticare gli utenti con Azure AD al momento dell'accesso per consentire il funzionamento dell'accesso SSO, in caso contrario le opzioni SSO verranno disabilitate.

Leggere [Single Sign-On alle](what-is-single-sign-on.md) applicazioni in Azure AD per scegliere il metodo SSO più appropriato durante la configurazione delle applicazioni.

###  <a name="working-with-other-types-of-applications"></a>Uso di altri tipi di applicazioni

Azure AD Application Proxy può supportare anche le applicazioni sviluppate per l'uso di [Microsoft Authentication Library (MSAL).](../develop/v2-overview.md) Supporta le app client native usando Azure AD token emessi ricevuti nelle informazioni di intestazione della richiesta client per eseguire la pre-autenticazione per conto degli utenti.

Leggere [pubblicazione di app client native e per](./application-proxy-configure-native-client-application.md) dispositivi mobili e applicazioni basate sulle [attestazioni](./application-proxy-configure-for-claims-aware-applications.md) per informazioni sulle configurazioni disponibili Application Proxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Usare l'accesso condizionale per rafforzare la sicurezza

La sicurezza delle applicazioni richiede un set avanzato di funzionalità di sicurezza in grado di proteggere e rispondere a minacce complesse in locale e nel cloud. Gli utenti malintenzionati ottengono in genere l'accesso alla rete aziendale tramite credenziali utente deboli, predefinite o rubate.  La sicurezza basata sulle identità Microsoft riduce l'uso di credenziali rubate gestendo e proteggendo identità con e senza privilegi.

Le funzionalità seguenti possono essere usate per supportare Azure AD Application Proxy:

* Accesso condizionale basato su utenti e località: proteggere i dati sensibili limitando l'accesso utente in base alla posizione geografica o a un indirizzo IP con criteri di accesso [condizionale basato sulla posizione.](../conditional-access/location-condition.md)

* Accesso condizionale basato su dispositivo: assicurarsi che solo i dispositivi registrati, approvati e conformi possano accedere ai dati aziendali con [l'accesso condizionale basato su dispositivo.](../conditional-access/require-managed-devices.md)

* Accesso condizionale basato su applicazione: non è necessario arrestare il lavoro quando un utente non è nella rete aziendale. [Proteggere l'accesso al cloud aziendale e alle app locali](../conditional-access/app-based-conditional-access.md) e mantenere il controllo con l'accesso condizionale.

* Accesso condizionale basato sul rischio: proteggere i dati da utenti malintenzionati con criteri di accesso condizionale basati sul rischio che possono essere applicati [a](https://www.microsoft.com/cloud-platform/conditional-access) tutte le app e a tutti gli utenti, in locale o nel cloud.

* Azure AD App personali: con il servizio Application Proxy distribuito e le applicazioni pubblicate in modo sicuro, offrire agli utenti un hub semplice per individuare e accedere a tutte le applicazioni. Aumentare la produttività con funzionalità self-service, ad esempio la possibilità di richiedere l'accesso a nuovi gruppi e app o gestire l'accesso a queste risorse per conto di altri utenti, [tramite App personali](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gestire l'implementazione

### <a name="required-roles"></a>Ruoli obbligatori

Microsoft promuove il principio di concedere il privilegio minimo possibile per eseguire le attività necessarie con Azure AD. [Esaminare i diversi ruoli di Azure disponibili e](../roles/permissions-reference.md) scegliere quello più corretto per soddisfare le esigenze di ogni utente tipo. Potrebbe essere necessario applicare temporaneamente e rimuovere alcuni ruoli al termine della distribuzione.

| Ruolo di business| Attività aziendali| Ruoli di Azure AD |
|---|---|---|
| Amministratore del supporto tecnico | In genere si limitano a qualificare i problemi segnalati dall'utente finale ed eseguire attività limitate, ad esempio la modifica delle password degli utenti, l'invalidazione dei token di aggiornamento e il monitoraggio dell'integrità del servizio. | Amministratore del supporto tecnico |
| Amministratore delle identità| Leggere Azure AD i report di accesso e i log di controllo per eseguire il debug dei problemi correlati al proxy app.| Ruolo con autorizzazioni di lettura per la sicurezza |
| Proprietario dell'applicazione| Creare e gestire tutti gli aspetti delle applicazioni aziendali, delle registrazioni delle applicazioni e delle impostazioni del proxy di applicazione.| Amministratore dell'applicazione |
| Amministratore dell'infrastruttura | Proprietario rollover del certificato | Amministratore dell'applicazione |

Ridurre al minimo il numero di utenti che hanno accesso a informazioni o risorse protette consente di ridurre la probabilità che un attore malintenzionato osentesi ottenere l'accesso non autorizzato o che un utente autorizzato inavvertitamente influisca su una risorsa sensibile.

Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi quotidiani, quindi l'applicazione di criteri di Privileged Identity Management basati [su JIT](../privileged-identity-management/pim-configure.md) (Just-In-Time) per fornire l'accesso con privilegi su richiesta alle risorse di Azure e Azure AD è l'approccio consigliato per gestire in modo efficace l'accesso amministrativo e il controllo.

### <a name="reporting-and-monitoring"></a>Monitoraggio e creazione di report

Azure AD informazioni dettagliate aggiuntive sull'utilizzo delle applicazioni e sull'integrità operativa dell'organizzazione tramite i [log di controllo e i report](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Application Proxy rende anche molto semplice monitorare i connettori dal portale Azure AD e dai registri eventi di Windows.

#### <a name="application-audit-logs"></a>Log di controllo delle applicazioni

Questi log forniscono informazioni dettagliate sugli account di accesso alle applicazioni configurate con Application Proxy e il dispositivo e l'utente che accede all'applicazione. [I log di](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) controllo si trovano nel portale di Azure e nell'API [di controllo per](/graph/api/resources/directoryaudit) l'esportazione. Sono inoltre disponibili [report di utilizzo e informazioni](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) dettagliate per l'applicazione.

#### <a name="application-proxy-connector-monitoring"></a>monitoraggio Application Proxy Connector

I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. È possibile monitorare lo stato dei connettori dalla pagina Application Proxy nel portale Azure AD. Per altre informazioni sulla manutenzione dei connettori, [vedere Informazioni Azure AD Application Proxy connettori.](./application-proxy-connectors.md#maintenance)

![Esempio: Azure AD Application Proxy connettori](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Registri eventi di Windows e contatori delle prestazioni

I connettori hanno log di amministrazione e di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. I log e i contatori si trovano nei registri eventi di Windows per altre informazioni, vedere [Informazioni Azure AD Application Proxy connettori](./application-proxy-connectors.md#under-the-hood). Seguire questa [esercitazione per configurare le origini dati del registro eventi in Monitoraggio di Azure](../../azure-monitor/agents/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Guida alla risoluzione dei problemi e passaggi

Per altre informazioni sui problemi comuni e su come risolverli, vedere la guida alla risoluzione [dei problemi relativi ai](application-proxy-troubleshoot.md) messaggi di errore.

Gli articoli seguenti illustrano scenari comuni che possono essere usati anche per creare guide alla risoluzione dei problemi per l'organizzazione di supporto.

* [Problema durante la visualizzazione di una pagina dell'app](application-proxy-page-appearance-broken-problem.md)
* [Il caricamento dell'applicazione richiede troppo tempo](application-proxy-page-load-speed-problem.md)
* [I collegamenti nella pagina dell'applicazione non funzionano](application-proxy-page-links-broken-problem.md)
* [Porte da aprire per l'app](application-proxy-add-on-premises-application.md)
* [Nessun connettore funzionante in un gruppo di connettori per l'app](application-proxy-connectivity-no-working-connector.md)
* [Configurare nel portale di amministrazione](application-proxy-config-how-to.md)
* [Configurare l'accesso Single Sign-On per l'app](application-proxy-config-sso-how-to.md)
* [Problema durante la creazione di un'app nel portale di amministrazione](application-proxy-config-problem.md)
* [Configurare la delega vincolata di Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurare con PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Errore di accesso all'applicazione aziendale](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problemi di installazione del connettore dell'agente proxy dell'applicazione](application-proxy-connector-installation-problem.md)
* [Problema di accesso](application-sign-in-problem-on-premises-application-proxy.md)
