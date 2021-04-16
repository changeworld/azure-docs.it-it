---
title: Eseguire la migrazione dell'autenticazione dell'applicazione Azure Active Directory
description: Questo white paper dettaglia la pianificazione e i vantaggi della migrazione dell'autenticazione dell'applicazione Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3458f358c12ef33a337e50066e83b6e59273ccf1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376750"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Eseguire la migrazione dell'autenticazione dell'applicazione Azure Active Directory

## <a name="about-this-paper"></a>Informazioni su questo documento

Questo white paper dettaglia la pianificazione e i vantaggi della migrazione dell'autenticazione dell'applicazione Azure AD. È progettato per amministratori di Azure e professionisti delle identità.

Suddividendo il processo in quattro fasi, ognuna con criteri di pianificazione e uscita dettagliati, è progettata per pianificare la strategia di migrazione e comprendere in che modo l'autenticazione Azure AD supporta gli obiettivi dell'organizzazione.

## <a name="introduction"></a>Introduzione

Attualmente, l'organizzazione richiede una grande numero di applicazioni (app) per consentire agli utenti di lavorare. È probabile che si continui ad aggiungere, sviluppare o ritirare le app ogni giorno. Gli utenti accedono a queste applicazioni da una vasta gamma di dispositivi aziendali e personali e posizioni. Aprono le app in molti modi, tra cui:

- tramite una home page o un portale aziendale

- tramite l'aggiunta di segnalibri nei browser

- tramite l'URL di un fornitore per le app SaaS (Software as a Service)

- collegamenti inviati direttamente ai desktop o ai dispositivi mobili dell'utente tramite una soluzione di gestione di dispositivi mobili/applicazioni (MDM/MAM)

È probabile che le applicazioni utilizzino i tipi di autenticazione seguenti:

- Soluzioni di federazione locali (ad esempio Active Directory Federation Services (ADFS) e Ping)

- Active Directory (ad esempio l'autenticazione Kerberos Windows-Integrated Auth)

- Altre soluzioni IAM (Identity and Access Management) basate sul cloud (ad esempio Okta o Oracle)

- Infrastruttura Web locale (ad esempio IIS e Apache)

- Infrastruttura ospitata nel cloud (ad esempio Azure e AWS)

**Per garantire che gli utenti possano accedere alle applicazioni in modo semplice e sicuro, l'obiettivo è avere un unico set di controlli di accesso e criteri negli ambienti locali e cloud.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) offre a persone, partner e clienti una singola identità per accedere alle applicazioni desiderate e collaborare da qualsiasi piattaforma e dispositivo.

![Diagramma della connettività Azure Active Directory rete](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD include una [suite completa di funzionalità di gestione delle identità.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) Standardizzare l'autenticazione e l'autorizzazione dell'app Azure AD consente di ottenere i vantaggi offerti da queste funzionalità.

Altre risorse per la migrazione sono disponibili all'indirizzo [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Vantaggi della migrazione dell'autenticazione delle app Azure AD

Lo spostamento dell'autenticazione delle app Azure AD consente di gestire i rischi e i costi, aumentare la produttività e soddisfare i requisiti di conformità e governance.

### <a name="manage-risk"></a>per gestire i rischi.

La protezione delle app richiede una visualizzazione completa di tutti i fattori di rischio. La migrazione delle app a Azure AD consolida le soluzioni di sicurezza. Con questa è possibile:

- Migliorare l'accesso utente sicuro alle applicazioni e ai dati aziendali associati usando criteri di accesso [condizionale,](../conditional-access/overview.md) [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)e tecnologie di Identity Protection basate sul rischio in [tempo](../identity-protection/overview-identity-protection.md) reale.

- Proteggere l'accesso dell'utente con privilegi all'ambiente con [l'accesso amministratore JUST-In-Time.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Usare la [progettazione multi-tenant, con distribuzione geografica e](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)disponibilità elevata Azure AD per le esigenze aziendali più critiche.

- Proteggere le applicazioni legacy con una delle [integrazioni di partner](https://aka.ms/secure-hybrid-access) con accesso ibrido sicuro già distribuite.

### <a name="manage-cost"></a>Gestire i costi

L'organizzazione può avere più soluzioni IAM (Identity Access Management). La migrazione a Azure AD un'infrastruttura è un'opportunità per ridurre le dipendenze dalle licenze IAM (in locale o nel cloud) e i costi dell'infrastruttura. Nei casi in cui è già stato pagato Azure AD tramite licenze Microsoft 365, non c'è alcun motivo per pagare il costo aggiuntivo di un'altra soluzione IAM.

**Con Azure AD, è possibile ridurre i costi dell'infrastruttura di:**

- Fornire l'accesso remoto sicuro alle app locali [usando Azure AD Application Proxy](./application-proxy.md).

- Separare le app dall'approccio basato sulle credenziali locale nel tenant configurando Azure AD [come provider di identità universali attendibile.](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)

### <a name="increase-productivity"></a>Aumentare la produttività

I vantaggi economici e di sicurezza consentono alle organizzazioni di adottare Azure AD, ma l'adozione e la conformità complete sono più probabili se anche gli utenti ne traggono vantaggio. Con Azure AD, è possibile:

- Migliorare l'esperienza [single Sign-On (SSO)](./what-is-single-sign-on.md) dell'utente finale tramite l'accesso facile e sicuro a qualsiasi applicazione, da qualsiasi dispositivo e da qualsiasi posizione.

- Usare funzionalità IAM self-service, ad esempio reimpostazioni password self-service e [gestione gruppi self-service.](../enterprise-users/groups-self-service-management.md) [](../authentication/concept-sspr-howitworks.md)

- Ridurre il sovraccarico amministrativo gestendo una sola identità per ogni utente in ambienti cloud e locali:

  - [Automatizzare il provisioning](../app-provisioning/user-provisioning.md) degli account utente (in [Azure AD Gallery)](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)in base Azure AD identità
  - Accedere a tutte le app dal pannello MyApps nel [portale di Azure ](https://portal.azure.com/)

- Consentire agli sviluppatori di proteggere l'accesso alle app e migliorare l'esperienza dell'utente finale usando [Microsoft Identity Platform](../develop/v2-overview.md) con Microsoft Authentication Library (MSAL).

- Consentire ai partner di accedere alle risorse cloud usando Azure AD [collaborazione B2B.](../external-identities/what-is-b2b.md) Le risorse cloud eliminano il sovraccarico della configurazione della federazione da punto a punto con i partner.

### <a name="address-compliance-and-governance"></a>Risolvere i problemi di conformità e governance

Garantire la conformità ai requisiti normativi tramite l'applicazione dei criteri di accesso aziendali e il monitoraggio dell'accesso degli utenti alle applicazioni e ai dati associati usando api e strumenti di controllo integrati. Con Azure AD, è possibile monitorare gli accesso alle applicazioni tramite report che usano gli strumenti [SiEM (Security Incident and Event Monitoring).](../reports-monitoring/plan-monitoring-and-reporting.md) È possibile accedere ai report dal portale o dalle API e controllare a livello di codice chi può accedere alle applicazioni e rimuovere l'accesso agli utenti inattivi tramite verifiche di accesso.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Pianificare le fasi di migrazione e la strategia del progetto

Quando i progetti tecnologici hanno esito negativo, spesso è dovuto a aspettative non corrispondenti, alle parti interessate non coinvolte o alla mancanza di comunicazione. È possibile garantire il successo pianificando il progetto stesso.

### <a name="the-phases-of-migration"></a>Fasi della migrazione

Prima di iniziare a utilizzare gli strumenti, è necessario comprendere come eseguire il processo di migrazione. Tramite diversi workshop diretti ai clienti, è consigliabile seguire le quattro fasi seguenti:

![Diagramma delle fasi della migrazione](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Assemblare il team del progetto

La migrazione delle applicazioni è un lavoro di team ed è necessario assicurarsi di avere tutte le posizioni fondamentali riempite. Il supporto di responsabili aziendali senior è importante. Assicurarsi di coinvolgere il set giusto di sponsor esecutivi, decision maker aziendali ed esperti in materia (SME).

Durante il progetto di migrazione, una persona può svolgere più ruoli o più persone adempie a ogni ruolo, a seconda delle dimensioni e della struttura dell'organizzazione. È anche possibile che si abbia una dipendenza da altri team che svolgono un ruolo chiave nel panorama della sicurezza.

La tabella seguente include i ruoli chiave e i relativi contributi:

| Ruolo          | Contributi                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Progetto che deve guidare il progetto, tra cui:<br /> - ottenere il supporto dirigenziare<br /> - Riunire gli stakeholder<br /> - Gestire pianificazioni, documentazione e comunicazioni |
| **Progettista/amministratore App Azure AD identità** | Sono responsabili di quanto segue:<br /> - Progettare la soluzione in collaborazione con gli stakeholder<br /> - Documentare la progettazione della soluzione e le procedure operative per la consegna al team operativo<br /> - Gestire gli ambienti di pre-produzione e produzione |
| **Team operativo di AD locale** | Organizzazione che gestisce le diverse origini di identità locali, ad esempio foreste DI, directory LDAP, sistemi HR e così via.<br /> - Eseguire tutte le attività di correzione necessarie prima della sincronizzazione<br /> - Specificare gli account del servizio necessari per la sincronizzazione<br /> - fornire l'accesso per configurare la federazione Azure AD |
| **Responsabile del supporto IT** | Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. |
| **Proprietario della sicurezza**  | Rappresentante del team di sicurezza che può garantire che il piano soddisfi i requisiti di sicurezza dell'organizzazione. |
| **Proprietari tecnici dell'applicazione** | Include i proprietari tecnici delle app e dei servizi che si integreranno con Azure AD. Forniscono gli attributi di identità delle applicazioni che devono essere inclusi nel processo di sincronizzazione. In genere hanno una relazione con i rappresentanti csv. |
| **Proprietari aziendali dell'applicazione** | Colleghi rappresentativi che possono fornire input sull'esperienza utente e sull'utilità di questa modifica dal punto di vista di un utente e sono proprietari dell'aspetto aziendale complessivo dell'applicazione, che può includere la gestione dell'accesso. |
| **Gruppo pilota di utenti** | Utenti che testeranno come parte del lavoro quotidiano, dell'esperienza pilota e forniranno commenti e suggerimenti per guidare il resto delle distribuzioni. |

### <a name="plan-communications"></a>Pianificare le comunicazioni

L'impegno aziendale e la comunicazione efficaci sono la chiave per il successo. È importante offrire agli stakeholder e agli utenti finali un modo per ottenere informazioni e tenersi informati sugli aggiornamenti della pianificazione. Informare tutti sul valore della migrazione, sulle sequenze temporali previste e su come pianificare eventuali interruzioni temporanee delle attività aziendali. Usare più percorsi, ad esempio sessioni di briefing, messaggi di posta elettronica, riunioni uno-a-uno, banner e municipi.

In base alla strategia di comunicazione scelta per l'app, è possibile ricordare agli utenti il tempo di inattività in sospeso. È inoltre necessario verificare che non siano presenti modifiche o effetti aziendali recenti che richiedono di posticipare la distribuzione.

Nella tabella seguente è riportata la comunicazione minima suggerita per mantenere informati gli stakeholder:

**Fasi del piano e strategia del progetto:**

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| Consapevolezza e valore aziendale/tecnico del progetto | Tutti tranne gli utenti finali |
| Richiesta per le app pilota | - Proprietari di aziende di app<br />- Proprietari tecnici delle app<br />- Team di architetti e identità |

**Fase 1: individuazione e ambito:**

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| - Richiesta di informazioni sull'applicazione<br />- Risultato dell'esercizio di definizione dell'ambito | - Proprietari tecnici delle app<br />- Proprietari di aziende di app |

**Fase 2: classificare le app e pianificare il progetto pilota:**

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| - Risultato delle classificazioni e significato della pianificazione della migrazione<br />- Pianificazione della migrazione preliminare | - Proprietari tecnici delle app<br /> - Proprietari dell'azienda dell'app |

**Fase 3: pianificare la migrazione e il test:**

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| - Risultato dei test di migrazione delle applicazioni | - Proprietari tecnici dell'app<br />- Proprietari dell'azienda dell'app |
| - Notifica dell'arrivo della migrazione e spiegazione delle esperienze degli utenti finali risultanti.<br />- Comunicazioni in arrivo e completa del tempo di inattività, incluse le attività da eseguire, i commenti e i suggerimenti e le modalità per ottenere assistenza | - Utenti finali (e tutti gli altri) |

**Fase 4: gestire e ottenere informazioni dettagliate:**

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| Analisi disponibile e come accedere | - Proprietari tecnici delle app<br />- Proprietari dell'azienda dell'app |

### <a name="migration-states-communication-dashboard"></a>Dashboard di comunicazione degli stati di migrazione

Comunicare lo stato complessivo del progetto di migrazione è fondamentale, in quanto mostra lo stato di avanzamento e aiuta i proprietari delle app le cui app sono in arrivo per la migrazione per prepararsi allo spostamento. È possibile creare un dashboard semplice usando Power BI o altri strumenti di creazione report per fornire visibilità sullo stato delle applicazioni durante la migrazione.

Gli stati di migrazione che è possibile usare sono i seguenti:

| Stati di migrazione       | Piano di azione                                   |
| ---------------------- | --------------------------------------------- |
| **Richiesta iniziale** | Trovare l'app e contattare il proprietario per altre informazioni |
| **Valutazione completata** | Il proprietario dell'app valuta i requisiti dell'app e restituisce il questionarino dell'app</td>
| **Configurazione in corso** | Sviluppare le modifiche necessarie per gestire l'autenticazione in Azure AD |
| **Test della configurazione completato** | Valutare le modifiche e autenticare l'app rispetto al tenant Azure AD test nell'ambiente di test |
| **Configurazione di produzione riuscita** | Modificare le configurazioni in modo che funzionino con il tenant di ACTIVE Directory di produzione e valutare l'autenticazione dell'app nell'ambiente di test |
| **Completamento/Disconnessione** | Distribuire le modifiche per l'app nell'ambiente di produzione ed eseguire nel tenant di Azure AD produzione |

In questo modo i proprietari delle app sapranno quali sono le pianificazioni di migrazione e test delle app quando le app sono in fase di migrazione e quali sono i risultati di altre app di cui è già stata eseguita la migrazione. È anche possibile fornire collegamenti al database di rilevamento dei bug per i proprietari per poter creare e visualizzare i problemi per le app di cui viene eseguita la migrazione.

### <a name="best-practices"></a>Procedure consigliate

Di seguito sono riportate le storie di successo del cliente e del partner e le procedure consigliate consigliate:

- [Cinque suggerimenti per migliorare il processo](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) di migrazione a Azure Active Directory di Patriot Consulting, un membro della rete di partner che si concentra sull'aiutare i clienti a distribuire soluzioni cloud Microsoft in modo sicuro.

- [Sviluppare una strategia di gestione dei rischi per la Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) di applicazioni mobili di Edgile, un partner che si concentra sulle soluzioni IAM e di gestione dei rischi.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: Individuare e ambito delle app

**L'individuazione e l'analisi delle applicazioni sono un esercizio fondamentale per dare un buon punto di partenza.** È possibile che non si sappia tutto, quindi è necessario prepararsi a contenere le app sconosciute.

### <a name="find-your-apps"></a>Trovare le app

Il primo punto decisionale nella migrazione di un'applicazione è l'eventuale migrazione delle app da migrare e le app da deprecare. È sempre possibile deprecare le app che non verranno usate nell'organizzazione. Esistono diversi modi per trovare le app nell'organizzazione. **Durante l'individuazione delle app, assicurarsi di includere le app in fase di sviluppo e pianificate. Usare Azure AD per l'autenticazione in tutte le app future.**

**Uso Active Directory Federation Services (AD FS) Per raccogliere un inventario delle app corretto:**

- **Usare Azure AD Connect Health.** Se si ha una licenza Azure AD Premium, è consigliabile distribuire Azure AD Connect Health [per](../hybrid/how-to-connect-health-adfs.md) analizzare l'utilizzo dell'app nell'ambiente locale. È possibile usare il report dell'applicazione [ADFS](./migrate-adfs-application-activity.md) (anteprima) per individuare le applicazioni ADFS di cui è possibile eseguire la migrazione e valutare la conformità dell'applicazione di cui eseguire la migrazione. Dopo aver completato la migrazione, [distribuire](/cloud-app-security/set-up-cloud-discovery) Cloud Discovery che consente di monitorare in modo continuo Shadow IT nell'organizzazione quando si è nel cloud.

- **AD FS log di analisi**. Se non si hanno licenze Azure AD Premium, è consigliabile usare ADFS per Azure AD strumenti di migrazione delle app basati [su PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Fare riferimento alla [Guida alla soluzione](./migrate-adfs-apps-to-azure.md):

[Migrazione di app da Active Directory Federation Services (AD FS) a Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Uso di altri provider di identità (IdP)

Per altri provider di identità, ad esempio Okta o Ping, è possibile usare i relativi strumenti per esportare l'inventario delle applicazioni. È possibile prendere in considerazione i principi di servizio registrati in Active Directory che corrispondono alle app Web nell'organizzazione.

### <a name="using-cloud-discovery-tools"></a>Uso degli strumenti di cloud discovery

Nell'ambiente cloud sono necessari visibilità avanzata, controllo sui viaggi dei dati e analisi sofisticate per trovare e contrastare le minacce informatiche in tutti i servizi cloud. È possibile raccogliere l'inventario delle app cloud usando gli strumenti seguenti:

- **Cloud Access Security Broker (CASB):** un [CASB](/cloud-app-security/) in genere funziona insieme al firewall per fornire visibilità sull'utilizzo delle applicazioni cloud dei dipendenti e consente di proteggere i dati aziendali dalle minacce alla sicurezza informatica. Il report CASB consente di determinare le app più usate nell'organizzazione e le destinazioni iniziali per la migrazione a Azure AD.

- **Cloud Discovery:** configurando Cloud Discovery [,](/cloud-app-security/set-up-cloud-discovery)si ottiene visibilità sull'utilizzo delle app cloud e si possono individuare app IT non approvazione o shadow.

- **API: per** le app connesse all'infrastruttura cloud, è possibile usare le API e gli strumenti in tali sistemi per iniziare a eseguire un inventario delle app ospitate. Nell'ambiente Azure:

  - Usare il cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) per ottenere informazioni sui siti Web di Azure.

  - Usare il cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) per ottenere informazioni sulle app Web di Azure.
D
  - È possibile trovare tutte le app in esecuzione in Microsoft IIS dalla riga di comando di Windows [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Usare [le applicazioni](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) e le entità [servizio](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) per ottenere informazioni su un'app e un'istanza dell'app in una directory in Azure AD.

### <a name="using-manual-processes"></a>Uso di processi manuali

Dopo aver seguito gli approcci automatizzati descritti in precedenza, sarà possibile gestire correttamente le applicazioni. Tuttavia, è possibile eseguire le operazioni seguenti per assicurarsi di avere una buona copertura in tutte le aree di accesso degli utenti:

- Contattare i vari proprietari aziendali dell'organizzazione per trovare le applicazioni in uso nell'organizzazione.

- Eseguire uno strumento di ispezione HTTP nel server proxy o analizzare i log del proxy per vedere dove viene instradato comunemente il traffico.

- Esaminare i weblog dei siti del portale aziendale più diffusi per vedere quali collegamenti accedono maggiormente agli utenti.

- Contattare i dirigenti o altri membri aziendali chiave per assicurarsi di aver coperto le app business critical.

### <a name="type-of-apps-to-migrate"></a>Tipo di app di cui eseguire la migrazione

Dopo aver trovato le app, si identificheranno questi tipi di app nell'organizzazione:

- App che usano già protocolli di autenticazione moderni

- App che usano protocolli di autenticazione legacy che si sceglie di modernizzare

- App che usano protocolli di autenticazione legacy che si sceglie DI NON modernizzare

- Nuove app Line of Business (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>App che usano già l'autenticazione moderna

Le app già modernizzate sono le più probabilmente spostate in Azure AD. Queste app usano già protocolli di autenticazione moderni (ad esempio SAML o OpenID Connect) e possono essere riconfigurate per l'autenticazione con Azure AD.

Oltre alle opzioni disponibili nella raccolta di [app Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) possono trattarsi di app già esistenti nell'organizzazione o di qualsiasi app di terze parti di un fornitore che non fa parte della raccolta Azure AD (applicazioni non della[raccolta).](./add-application-portal.md)

App legacy che si sceglie di modernizzare

Per le app legacy che si vuole modernizzare, il passaggio a Azure AD per l'autenticazione di [](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) base e l'autorizzazione sblocca tutta la potenza e la potenza dei dati che Microsoft Graph e [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) hanno da offrire.

È consigliabile aggiornare il codice **dello stack** di autenticazione per queste applicazioni dal protocollo legacy (ad esempio autenticazione Windows-Integrated, delega vincolata Kerberos, autenticazione basata su intestazioni HTTP) a un protocollo moderno (ad esempio SAML o OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>App legacy che si sceglie DI NON modernizzare

Per alcune app che usano protocolli di autenticazione legacy, talvolta la modernizzazione dell'autenticazione non è la cosa giusta da fare per motivi aziendali. Sono inclusi i tipi di app seguenti:

- App mantenute in locale per motivi di conformità o controllo.

- App connesse a un'identità locale o a un provider di federazione che non si vuole modificare.

- App sviluppate usando standard di autenticazione locali che non sono in programma di spostare

Azure AD può portare grandi vantaggi a queste app legacy, in quanto è possibile abilitare le moderne funzionalità di [](./access-panel-manage-self-service-access.md)sicurezza e governance [](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) di Azure AD come [Multi-Factor Authentication,](../authentication/concept-mfa-howitworks.md)l'accesso [condizionale,](../conditional-access/overview.md) [Identity Protection,](../identity-protection/index.yml)l'accesso delegato alle applicazioni e le verifiche di accesso per queste app senza toccare affatto l'app.

Per iniziare, estendere queste app nel **cloud** con Azure AD [Application Proxy](./application-proxy-configure-single-sign-on-password-vaulting.md) usando semplici metodi di autenticazione (ad esempio, l'insieme di credenziali delle password) per eseguire rapidamente la migrazione degli utenti o tramite le integrazioni dei [partner](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) con i controller di distribuzione delle applicazioni già distribuiti.

### <a name="new-line-of-business-lob-apps"></a>Nuove app line-of-business (LoB)

In genere si sviluppano app lob per l'uso interno dell'organizzazione. Se sono presenti nuove app nella pipeline, è consigliabile usare [Microsoft Identity Platform per](../develop/v2-overview.md) implementare OpenID Connect.

### <a name="apps-to-deprecate"></a>App da deprecare

Le app senza proprietari chiari e la manutenzione e il monitoraggio chiari presentano un rischio per la sicurezza per l'organizzazione. Prendere in considerazione la deprecazione delle applicazioni quando:

- la **loro funzionalità è altamente ridondante** con altri sistemi • non esiste alcun proprietario **dell'azienda**

- non esiste chiaramente **alcun utilizzo di**.

È consigliabile non **deprecare applicazioni** aziendali critiche a impatto elevato. In questi casi, collaborare con i proprietari aziendali per determinare la strategia giusta.

### <a name="exit-criteria"></a>Criteri uscita

Questa fase ha esito positivo con:

- Una buona conoscenza dei sistemi nell'ambito della migrazione (che è possibile ritirare dopo aver spostato in Azure AD)

- Elenco di app che include:

  - A quali sistemi si connettono le app
  - Da dove e da quali dispositivi gli utenti accedono
  - Se verranno migrati, deprecati o connessi con [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> È possibile scaricare [il foglio di](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) lavoro di Individuazione applicazioni per registrare le applicazioni di cui si vuole eseguire la migrazione all'autenticazione Azure AD e quelle che si desidera lasciare ma gestire usando [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: Classificare le app e pianificare un progetto pilota

Classificare la migrazione delle app è un esercizio importante. Non è necessario eseguire la migrazione e la transizione di tutte le app contemporaneamente. Dopo aver raccolto informazioni su ognuna delle app, è possibile razionalizzare le app di cui eseguire prima la migrazione e ciò potrebbe richiedere tempo.

### <a name="classify-in-scope-apps"></a>Classificare le app nell'ambito

Un modo per considerare questo problema è lungo gli assi della criticità aziendale, dell'utilizzo e della durata, ognuno dei quali dipende da più fattori.

### <a name="business-criticality"></a>Criticità aziendale

La criticità aziendale avrà dimensioni diverse per ogni azienda, ma le due misure da prendere in considerazione sono **le** funzionalità e i **profili utente.** Assegnare alle app con funzionalità univoche un valore di punto superiore rispetto a quelle con funzionalità ridondanti o obsolete.

![Diagramma delle funzionalità e dei profili utente & funzionalità](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Utilizzo

Le applicazioni **con numeri di utilizzo elevati** devono ricevere un valore superiore rispetto alle app con un utilizzo ridotto. Assegnare un valore più elevato alle app con utenti esterni, dirigenti o del team di sicurezza. Per ogni app nel portfolio di migrazione, completare queste valutazioni.

![Diagramma delle dimensioni del volume utente e dell'ampiezza dell'utente](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Dopo aver determinato i valori per la criticità e l'utilizzo aziendali, è possibile determinare la durata dell'applicazione e creare una matrice di priorità. Di seguito è riportata una di queste matrici:

![Diagramma a triangolo che mostra le relazioni tra utilizzo, durata prevista e criticità aziendale](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Assegnare priorità alle app per la migrazione

È possibile scegliere di iniziare la migrazione delle app con le app con la priorità più bassa o con le app con la priorità più alta in base alle esigenze dell'organizzazione.

In uno scenario in cui non si ha esperienza nell'uso  dei Azure AD e dei servizi di gestione delle identità, è consigliabile spostare le app con priorità più bassa Azure AD prima. In questo modo si ridurrà al minimo l'impatto aziendale ed è possibile creare uno stato di forza maggiore. Dopo aver spostato correttamente queste app e aver acquisito la fiducia degli stakeholder, è possibile continuare a eseguire la migrazione delle altre app.

Se non esiste una priorità chiara, è consigliabile spostare prima le app presenti nella raccolta [di Azure AD e](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) supportare più provider di identità (ADFS o Okta) perché sono più facili da integrare. È probabile che queste app siano le app con la **priorità più alta** nell'organizzazione. Per facilitare l'integrazione delle applicazioni SaaS con Azure AD, [](../saas-apps/tutorial-list.md) è stata sviluppata una raccolta di esercitazioni che illustrano la configurazione.

Quando si ha una scadenza per la migrazione delle app, questo bucket di app con priorità più alta avrà il carico di lavoro principale. È possibile selezionare le app con priorità più bassa perché non modificheranno il costo anche se è stata spostata la scadenza. Anche se è necessario rinnovare la licenza, lo sarà per una piccola quantità.

Oltre **a** questa classificazione e a seconda dell'urgenza della migrazione, è anche possibile pianificare la migrazione entro cui i proprietari delle app devono interagire per eseguire la migrazione delle app. Al termine di questo processo, dovrebbe essere presente un elenco di tutte le applicazioni in bucket con priorità per la migrazione.

### <a name="document-your-apps"></a>Documentare le app

Per prima cosa, iniziare raccogliendo i dettagli principali sulle applicazioni. Il [foglio di lavoro per l'individuazione](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)delle applicazioni consente di prendere rapidamente le decisioni relative alla migrazione e di ricevere una raccomandazione al gruppo aziendale in tempi brevi.

Le informazioni importanti per prendere una decisione di migrazione includono:

- **Nome** dell'app: qual è l'app nota come business?

- **Tipo di** app: si tratta di un'app SaaS di terze parti? Un'app Web line-of-business personalizzata? Un'API?

- **Criticità aziendale:** la criticità è elevata? Basso? O da qualche parte?

- **Volume di accesso utente:** tutti gli utenti accedono a questa app o solo ad alcune persone?

- **Durata pianificata:** per quanto tempo l'app sarà in giro? Meno di sei mesi? Più di due anni?

- **Provider di identità corrente:** qual è l'IDP primario per questa app? Oppure si basa sull'archiviazione locale?

- **Metodo di autenticazione:** l'app esegue l'autenticazione usando standard aperti?

- **Se si prevede di aggiornare il codice dell'app:** l'app è in fase di sviluppo pianificato o attivo?

- **Se si prevede di mantenere l'app in** locale, si vuole mantenere l'app nel data center a lungo termine?

- **Se l'app dipende da altre app** o API: l'app chiama attualmente altre app o API?

- **Se l'app si** trova nella raccolta Azure AD: l'app è attualmente già integrata con [Azure AD Gallery?](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)

Altri dati che saranno utili in un secondo momento, ma che non è necessario prendere una decisione di migrazione immediata includono:

- **URL dell'app:** dove gli utenti possono accedere all'app?

- **Descrizione** dell'app: che cos'è una breve descrizione delle funzioni dell'app?

- **Proprietario dell'app:** chi nell'azienda è il punto di controllo di accesso principale per l'app?

- **Commenti o note generali:** qualsiasi altra informazione generale sulla proprietà dell'app o dell'azienda

Dopo aver classificato l'applicazione e aver documentato i dettagli, assicurarsi di ottenere il buy-in del proprietario dell'azienda per la strategia di migrazione pianificata.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

Le app selezionate per il progetto pilota devono rappresentare i requisiti di identità e sicurezza chiave dell'organizzazione ed è necessario avere un buy-in chiaro dai proprietari dell'applicazione. I progetti pilota vengono in genere eseguiti in un ambiente di test separato. Vedere [le procedure consigliate per i progetti pilota](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) nella pagina dei piani di distribuzione.

**Non dimenticare i partner esterni.** Assicurarsi che partecipino alle pianificazioni e ai test della migrazione. Infine, assicurarsi che sia possibile accedere al supporto help desk in caso di problemi di rilievo.

### <a name="plan-for-limitations"></a>Pianificare le limitazioni

Anche se è facile eseguire la migrazione di alcune app, altre potrebbero richiedere più tempo a causa di più server o istanze. Ad esempio, la migrazione a SharePoint potrebbe richiedere più tempo a causa di pagine di accesso personalizzate.

Molti fornitori di app SaaS addebitano la modifica della connessione SSO. Verificare con loro e pianificare questa operazione.

Azure AD sono presenti [anche limiti e restrizioni del servizio](../enterprise-users/directory-service-limits-restrictions.md) di cui è necessario essere a conoscenza.

### <a name="app-owner-sign-off"></a>Approvazione del proprietario dell'app

Le applicazioni business critical e usate universalmente potrebbero richiedere un gruppo di utenti pilota per testare l'app nella fase pilota. Dopo aver testato un'app nell'ambiente di pre-produzione o pilota, assicurarsi che i proprietari dell'azienda di app accedano alle prestazioni prima della migrazione dell'app e di tutti gli utenti all'uso in produzione di Azure AD per l'autenticazione.

### <a name="plan-the-security-posture"></a>Pianificare il posture di sicurezza

Prima di avviare il processo di migrazione, prendere in considerazione completamente il modello di sicurezza che si vuole sviluppare per il sistema di identità aziendale. Si basa sulla raccolta di questi importanti set di informazioni: identità, dispositivi e posizioni **che accedono ai dati.**

### <a name="identities-and-data"></a>Identità e dati

La maggior parte delle organizzazioni ha requisiti specifici relativi alle identità e alla protezione dei dati che variano in base al segmento del settore e alle funzioni lavorative all'interno delle organizzazioni. Fare riferimento alle [configurazioni di identità e accesso](/microsoft-365/enterprise/microsoft-365-policies-configurations) ai dispositivi per le raccomandazioni, tra cui un set prestabilito di criteri di accesso [condizionale](../conditional-access/overview.md) e funzionalità correlate.

È possibile usare queste informazioni per proteggere l'accesso a tutti i servizi integrati con Azure AD. Queste raccomandazioni sono allineate con Microsoft Secure Score e il [punteggio di identità in Azure AD](../fundamentals/identity-secure-score.md). Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità

- Pianificare miglioramenti per la sicurezza delle identità

- Verificare il successo dei miglioramenti apportati

Ciò consente anche di implementare i [cinque passaggi per proteggere l'infrastruttura di gestione delle identità.](../../security/fundamentals/steps-secure-identity.md) Usare le indicazioni come punto di partenza per l'organizzazione e modificare i criteri per soddisfare i requisiti specifici dell'organizzazione.

### <a name="who-is-accessing-your-data"></a>Chi accede ai dati?

Esistono due categorie principali di utenti delle app e delle risorse Azure AD supporta:

- **Interno:** Dipendenti, terzisti e fornitori che dispongono di account all'interno del provider di identità. Potrebbero essere necessari altri pivot con regole diverse per i manager o i dirigenti rispetto ad altri dipendenti.

- **Esterno:** Fornitori, fornitori, distributori o altri partner commerciali che interagiscono con l'organizzazione nel corso regolare dell'attività con Azure AD [collaborazione B2B.](../external-identities/what-is-b2b.md)

È possibile definire gruppi per questi utenti e popolare questi gruppi in modi diversi. È possibile scegliere che un amministratore deve aggiungere manualmente membri a un gruppo oppure abilitare l'appartenenza a gruppi self-service. È possibile stabilire regole che aggiungono automaticamente membri ai gruppi in base ai criteri specificati usando [i gruppi dinamici](../enterprise-users/groups-dynamic-membership.md).

Gli utenti esterni possono anche fare riferimento ai clienti. [Azure AD B2C](../../active-directory-b2c/overview.md), un prodotto separato supporta l'autenticazione del cliente. Tuttavia, non rientra nell'ambito di questo documento.

### <a name="devicelocation-used-to-access-data"></a>Dispositivo/posizione usato per accedere ai dati

Anche il dispositivo e la posizione che un utente usa per accedere a un'app sono importanti. I dispositivi fisicamente connessi alla rete aziendale sono più sicuri. Le connessioni dall'esterno della rete tramite VPN potrebbero richiedere un controllo.

![Diagramma che illustra la relazione tra la posizione utente e l'accesso ai dati](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

In considerazione di questi aspetti relativi a risorse, utenti e dispositivi, è possibile scegliere di usare Azure AD [di accesso condizionale.](../conditional-access/overview.md) L'accesso condizionale va oltre le autorizzazioni utente: si basa su una combinazione di fattori, ad esempio l'identità di un utente o di un gruppo, la rete a cui l'utente è connesso, il dispositivo e l'applicazione in uso e il tipo di dati a cui sta tentando di accedere. L'accesso concesso all'utente si adatta a questo set più ampio di condizioni.

### <a name="exit-criteria"></a>Criteri uscita

Questa fase ha esito positivo quando:

- Conoscere le app
  - Avere documentato completamente le app di cui si intende eseguire la migrazione
  - Avere app classificate in ordine di priorità in base alla criticità aziendale, al volume di utilizzo e alla durata

- Selezionare le app che rappresentano i requisiti per un progetto pilota

- Acquisto da parte del proprietario dell'azienda per la definizione delle priorità e la strategia

- Comprendere le esigenze di sicurezza e come implementarle

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: Pianificare la migrazione e i test

Dopo aver ottenuto l'acquisto da parte dell'azienda, il passaggio successivo consiste nell'avviare la migrazione di queste app Azure AD autenticazione.

### <a name="migration-tools-and-guidance"></a>Strumenti e linee guida per la migrazione

Usare gli strumenti e le linee guida seguenti per seguire i passaggi precisi necessari per eseguire la migrazione delle applicazioni Azure AD:

- **Indicazioni** generali sulla migrazione: usare il white paper, gli strumenti, i modelli di posta elettronica e il questionarino delle applicazioni nel toolkit di migrazione delle app [Azure AD](./migration-resources.md) per individuare, classificare ed eseguire la migrazione delle app.

- **Applicazioni SaaS:** vedere l'elenco di centinaia di esercitazioni sulle [app SaaS](../saas-apps/tutorial-list.md) e il piano di distribuzione completo [dell'accesso Single Sign-On](https://aka.ms/ssodeploymentplan) di Azure AD per eseguire il processo end-to-end.

- **Applicazioni in esecuzione in locale:** informazioni complete sui Azure AD Application Proxy e usare il piano di Azure AD Application Proxy completo [per](https://aka.ms/AppProxyDPDownload) iniziare rapidamente. [](./application-proxy.md)

- **App in fase di sviluppo:** leggere le linee guida dettagliate per l'integrazione [e](../develop/quickstart-register-app.md) [la registrazione.](../develop/quickstart-register-app.md)

Dopo la migrazione, è possibile scegliere di inviare una comunicazione informando gli utenti della corretta distribuzione e ricordando loro eventuali nuovi passaggi da eseguire.

### <a name="plan-testing"></a>Panificare i test

Durante il processo di migrazione, l'app potrebbe già avere un ambiente di test usato durante le distribuzioni regolari. È possibile continuare a usare questo ambiente per i test di migrazione. Se non è attualmente disponibile un ambiente di test, è possibile configurarne uno usando Servizio app di Azure o Macchine virtuali di Azure, a seconda dell'architettura dell'applicazione. È possibile scegliere di configurare un tenant di test separato Azure AD da usare durante lo sviluppo delle configurazioni dell'app. Questo tenant verrà avviato in uno stato pulito e non verrà configurato per la sincronizzazione con alcun sistema.

È possibile testare ogni app accedendo con un utente di test e verificando che tutte le funzionalità siano le stesse di prima della migrazione. Se durante il test si determina che gli utenti dovranno aggiornare le impostazioni [MFA](/azure/active-directory/authentication/howto-mfa-userstates) o [SSPR](../authentication/tutorial-enable-sspr.md)oppure si aggiunge questa funzionalità durante la migrazione, assicurarsi di aggiungerla al piano di comunicazione dell'utente finale. Vedere [Modelli di comunicazione](https://aka.ms/mfatemplates) [dell'utente finale MFA e SSPR.](https://aka.ms/ssprtemplates)

Dopo aver eseguito la migrazione delle app, passare al [portale di Azure](https://aad.portal.azure.com/) per verificare se la migrazione è riuscita. Seguire le istruzioni riportate di seguito:

- Selezionare **Applicazioni aziendali Tutte le &gt; applicazioni** e trovare l'app nell'elenco.

- Selezionare **Gestisci utenti e &gt; gruppi** per assegnare almeno un utente o un gruppo all'app.

- Selezionare **Gestisci &gt; accesso condizionale**. Esaminare l'elenco di criteri e assicurarsi di non bloccare l'accesso all'applicazione con criteri [di accesso condizionale.](../conditional-access/overview.md)

A seconda di come si configura l'app, verificare che l'accesso Single Sign-On funzioni correttamente.

| Tipo di autenticazione      | Test                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Selezionare **Autorizzazioni per &gt; le** applicazioni aziendali e assicurarsi di avere concesso il consenso all'applicazione da usare nell'organizzazione nelle impostazioni utente per l'app. |
| **SSO basato su SAML** | Usare il [pulsante Test delle impostazioni SAML](./debug-saml-sso-issues.md) disponibile in Single **Sign-On.** |
| **Accesso Single Sign-On basato su password** | Scaricare e installare [l'estensione per l'accesso sicuro di MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Questa estensione consente di avviare qualsiasi app cloud dell'organizzazione che richiede l'uso di un processo SSO. |

| **[Application Proxy](./application-proxy.md)** | Assicurarsi che il connettore sia in esecuzione e assegnato all'applicazione. Per ulteriore [assistenza, Application Proxy alla risoluzione dei](./application-proxy-troubleshoot.md) problemi. |

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi, vedere la guida alla risoluzione dei [problemi delle app](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) per ottenere assistenza. È anche possibile consultare gli articoli sulla risoluzione dei problemi. Vedere Problemi di accesso alle app configurate per l'accesso [Single Sign-On basato su SAML.](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

### <a name="plan-rollback"></a>Pianificare il rollback

Se la migrazione non riesce, la strategia migliore consiste nell'eseguire il rollback e il test. Ecco i passaggi che è possibile eseguire per attenuare i problemi di migrazione:

- **Acquisire screenshot della** configurazione esistente dell'app. È possibile tornare indietro se è necessario riconfigurare nuovamente l'app.

- È anche possibile fornire **collegamenti all'autenticazione legacy,** se si sono verificati problemi con l'autenticazione cloud.

- Prima di completare la migrazione, **non modificare la configurazione esistente con** il provider di identità precedente.

- Per iniziare, eseguire **la migrazione delle app che supportano più IdP.** In caso di problemi, è sempre possibile passare alla configurazione del provider di identità preferito.

- Assicurarsi che l'esperienza dell'app abbia un **pulsante Commenti** e suggerimenti o puntatori ai problemi **del supporto.**

### <a name="exit-criteria"></a>Criteri uscita

Questa fase ha esito positivo quando si dispone di:

- Determinare come verrà eseguita la migrazione di ogni app

- Sono stati esaminati gli strumenti di migrazione

- È stato pianificato il test, inclusi gruppi e ambienti di test

- Rollback pianificato

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: Pianificare la gestione e le informazioni dettagliate

Dopo aver eseguito la migrazione delle app, è necessario assicurarsi che:

- Gli utenti possono accedere e gestire in modo sicuro

- È possibile ottenere informazioni dettagliate appropriate sull'utilizzo e sull'integrità delle app

È consigliabile eseguire le azioni seguenti in base alle esigenze dell'organizzazione.

### <a name="manage-your-users-app-access"></a>Gestire l'accesso alle app degli utenti

Dopo aver eseguito la migrazione delle app, è possibile arricchire l'esperienza dell'utente in molti modi

**Rendere individuabili le app**

**Puntare l'utente** [all'esperienza del portale MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) In questo caso, possono accedere a tutte le app basate sul cloud, alle app che vengono rendete disponibili usando Azure AD Connect e alle app usando Application Proxy purché [abbia](./application-proxy.md) le autorizzazioni per accedere [a](../hybrid/whatis-azure-ad-connect.md)tali app.


È possibile guidare gli utenti su come individuare le app:

- Usare la [funzionalità Single Sign-On](./view-applications-portal.md) esistente per **collegare gli utenti a qualsiasi app**


- Abilitare [l'accesso alle applicazioni self-service](./manage-self-service-access.md)a un'app e consentire agli utenti di aggiungere **app curate**

- [Nascondere le applicazioni agli utenti finali](./hide-application-from-user-portal.md) (app  Microsoft predefinite o altre app) per rendere più individuabili le app necessarie

### <a name="make-apps-accessible"></a>Rendere accessibili le app

**Consentire agli utenti di accedere alle app dai propri dispositivi mobili.** Gli utenti possono accedere al portale MyApps con il browser gestito da Intune nei dispositivi [iOS](./hide-application-from-user-portal.md) 7.0 o versioni successive [o Android.](./hide-application-from-user-portal.md)

Gli utenti possono scaricare un **browser gestito da Intune:**

- **Per i dispositivi Android**, da [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Per i dispositivi Apple,** [da Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) o possono scaricare l'app App personali per dispositivi mobili per [iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Consentire agli utenti di aprire le app da un'estensione del browser.**

Gli utenti [possono scaricare l'estensione](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) per l'accesso sicuro di MyApps in [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) o [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e possono avviare le app direttamente dalla barra del browser a:

- **Cercare le app e visualizzare le app usate più di recente**

- **Convertire automaticamente gli URL** interni configurati [in](./application-proxy.md) Application Proxy negli URL esterni appropriati. Gli utenti possono ora usare i collegamenti con cui hanno familiarità, indipendentemente dalla posizione in cui si trova.

**Consentire agli utenti di aprire le app Office.com.**

Gli utenti possono passare  [Office.com](https://www.office.com/) per cercare le app e visualizzare le app usate più di recente direttamente da dove lavorano.

### <a name="secure-app-access"></a>Proteggere l'accesso alle app

Azure AD offre una posizione di accesso centralizzata per gestire le app migrate. Passare al [portale di Azure](https://portal.azure.com/) e abilitare le funzionalità seguenti:

- **Proteggere l'accesso utente alle app.** Abilitare [i criteri di accesso condizionale](../conditional-access/overview.md)o Identity [Protection](../identity-protection/overview-identity-protection.md)per proteggere l'accesso degli utenti alle applicazioni in base allo stato, alla posizione e altro ancora del dispositivo.

- **Provisioning automatico.** Configurare il [provisioning automatico degli utenti con](../app-provisioning/user-provisioning.md) varie app SaaS di terze parti a cui gli utenti devono accedere. Oltre a creare identità utente, include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano.

- **Delegare la gestione dell'accesso** **utente**. In base alle esigenze, abilitare l'accesso alle app self-service alle app e assegnare un responsabile approvazione aziendale per *approvare l'accesso a tali app.* Usare [la gestione dei gruppi self-service](../enterprise-users/groups-self-service-management.md)per i gruppi assegnati alle raccolte di app.

- **Delegare l'accesso amministratore.** uso **del ruolo della** directory per assegnare un ruolo di amministratore (ad esempio amministratore di applicazioni, amministratore di applicazioni cloud o sviluppatore di applicazioni) all'utente.

### <a name="audit-and-gain-insights-of-your-apps"></a>Controllare e ottenere informazioni dettagliate sulle app

È anche possibile usare il [portale di Azure](https://portal.azure.com/) per controllare tutte le app da una posizione centralizzata,

- **Controllare l'app** usando **Applicazioni aziendali, Controllare o accedere alle stesse informazioni [dall'API Azure AD Reporting](../reports-monitoring/concept-reporting-api.md) per l'integrazione negli strumenti preferiti.

- **Visualizzare le autorizzazioni per un'app** usando **Applicazioni aziendali, Autorizzazioni per** le app che usano OAuth/OpenID Connect.

- **Ottenere informazioni dettagliate sull'accesso** **usando applicazioni aziendali, sign-ins.** Accedere alle stesse informazioni [dall'API Azure AD Reporting.](../reports-monitoring/concept-reporting-api.md)

- **Visualizzare l'utilizzo dell'app** dal [pacchetto Azure AD Power BI app](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Criteri uscita

Questa fase ha esito positivo quando:

- Fornire l'accesso sicuro alle app agli utenti

- Gestire per controllare e ottenere informazioni dettagliate sulle app di cui è stata eseguita la migrazione

### <a name="do-even-more-with-deployment-plans"></a>Fare ancora di più con i piani di distribuzione

I piani di distribuzione illustrano il valore aziendale, la pianificazione, i passaggi di implementazione e la gestione Azure AD soluzioni, inclusi gli scenari di migrazione delle app. Riunisce tutti gli elementi necessari per iniziare a distribuire e ottenere valore Azure AD funzionalità. Le guide alla distribuzione includono contenuto come le procedure consigliate di Microsoft, le comunicazioni degli utenti finali, le guide alla pianificazione, i passaggi di implementazione, i test case e altro ancora.

Molti [piani di](../fundamentals/active-directory-deployment-plans.md) distribuzione sono disponibili per l'uso dell'utente e microsoft ne sta facendo sempre di più.

### <a name="contact-support"></a>Contattare il supporto tecnico

Visitare i collegamenti di supporto seguenti per creare o tenere traccia del ticket di supporto e monitorare l'integrità.

- **supporto di Azure:** È possibile chiamare [Supporto tecnico Microsoft](https://azure.microsoft.com/support) e aprire un ticket per qualsiasi azure

Problema di distribuzione delle identità a seconda dell'Contratto Enterprise con Microsoft.

- **FastTrack:** se sono state acquistate licenze Enterprise Mobility and Security (EMS) o Azure AD Premium, si è idonei a ricevere assistenza per la distribuzione dal [programma FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Coinvolgere il team di progettazione del prodotto:** Se si sta lavorando a una distribuzione importante da parte dei clienti con milioni di utenti, si ha diritto al supporto del team di account Microsoft o del cloud Architetto di soluzioni. In base alla complessità di distribuzione del progetto, è possibile collaborare direttamente con il team di progettazione del prodotto [per le identità di Azure.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **blog Azure AD Identity:** Sottoscrivere il [blog Azure AD Identity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) per rimanere aggiornati con tutti gli annunci di prodotto più recenti, le approfondite tuffi e le informazioni sulla roadmap fornite direttamente dal team di progettazione delle identità.
