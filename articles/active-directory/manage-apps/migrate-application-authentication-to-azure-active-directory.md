---
title: Eseguire la migrazione dell'autenticazione dell'applicazione a Azure Active Directory
description: Questo white paper illustra in dettaglio la pianificazione e i vantaggi della migrazione dell'autenticazione dell'applicazione a Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952967"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Eseguire la migrazione dell'autenticazione dell'applicazione a Azure Active Directory

## <a name="about-this-paper"></a>Informazioni su questo documento

Questo white paper illustra in dettaglio la pianificazione e i vantaggi della migrazione dell'autenticazione dell'applicazione a Azure AD. È progettato per gli amministratori di Azure e i professionisti di identità.

Suddividendo il processo in quattro fasi, ognuna con criteri di pianificazione e uscita dettagliati, viene progettata per facilitare la pianificazione della strategia di migrazione e comprendere il modo in cui l'autenticazione Azure AD supporta gli obiettivi dell'organizzazione.

## <a name="introduction"></a>Introduzione

Attualmente, l'organizzazione richiede una serie di applicazioni (app) che consentono agli utenti di svolgere il proprio lavoro. È probabile che si continui ad aggiungere, sviluppare o ritirare le app ogni giorno. Gli utenti accedono a queste applicazioni da un'ampia gamma di dispositivi aziendali e personali e località. Aprono app in molti modi, tra cui:

- tramite una Home page o un portale aziendale

- tramite segnalibro nei browser

- tramite l'URL di un fornitore per le app SaaS (Software as a Service)

- collegamenti direttamente ai desktop o ai dispositivi mobili degli utenti tramite una soluzione di gestione di dispositivi mobili/applicazioni (MDM/MAM)

È probabile che le applicazioni utilizzino i tipi di autenticazione seguenti:

- Soluzioni di Federazione locali (ad esempio Active Directory Federation Services (ADFS) e ping)

- Active Directory (ad esempio autenticazione Kerberos e autenticazione Windows-Integrated)

- Altre soluzioni IAM (Identity and Access Management) basate sul cloud (ad esempio Okta o Oracle)

- Infrastruttura Web locale (ad esempio IIS e Apache)

- Infrastruttura ospitata nel cloud, ad esempio Azure e AWS

**Per garantire che gli utenti possano accedere in modo semplice e sicuro alle applicazioni, l'obiettivo è avere un unico set di controlli di accesso e criteri negli ambienti locali e cloud.**

[Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) offre una piattaforma di identità universale che fornisce a utenti, partner e clienti una singola identità per accedere alle applicazioni desiderate e collaborare da qualsiasi piattaforma e dispositivo.

![Diagramma della connettività Azure Active Directory](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD dispone [di una suite completa di funzionalità di gestione delle identità](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). La standardizzazione dell'autenticazione e dell'autorizzazione delle app per Azure AD ti permette di ottenere i vantaggi offerti da queste funzionalità.

Altre risorse di migrazione sono disponibili all'indirizzo [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Vantaggi della migrazione dell'autenticazione delle app a Azure AD

La possibilità di trasferire l'autenticazione delle app Azure AD consentirà di gestire i rischi e i costi, aumentare la produttività e soddisfare i requisiti di conformità e governance.

### <a name="manage-risk"></a>per gestire i rischi.

Per salvaguardare le app, è necessario disporre di una visualizzazione completa di tutti i fattori di rischio. La migrazione delle app a Azure AD consolida le soluzioni di sicurezza. È possibile:

- Migliorare l'accesso sicuro degli utenti alle applicazioni e ai dati aziendali associati usando i [criteri di accesso condizionale](../conditional-access/overview.md), [multi-factor authentication](../authentication/concept-mfa-howitworks.md)e le tecnologie di [protezione delle identità](../identity-protection/overview-identity-protection.md) basate sul rischio in tempo reale.

- Consente di proteggere l'accesso degli utenti con privilegi all'ambiente con accesso di amministratore [JIT](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) .

- Usa la [progettazione multi-tenant, con distribuzione geografica e disponibilità elevata di Azure ad](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)per le tue esigenze aziendali più importanti.

- Proteggi le applicazioni legacy con una delle nostre [integrazioni sicure dei partner di accesso ibrido](https://aka.ms/secure-hybrid-access) che potrebbero essere già state distribuite.

### <a name="manage-cost"></a>Gestione dei costi

È possibile che l'organizzazione disponga di più soluzioni IAM (Identity Access Management). La migrazione a un'infrastruttura Azure AD è un'opportunità per ridurre le dipendenze dalle licenze IAM (locale o nel cloud) e dai costi dell'infrastruttura. Nei casi in cui potrebbe essere già stato effettuato il pagamento per Azure AD tramite Microsoft 365 licenze, non esiste alcun motivo per pagare il costo aggiuntivo di un'altra soluzione IAM.

**Con Azure AD è possibile ridurre i costi dell'infrastruttura:**

- Fornire l'accesso remoto sicuro alle app locali usando [Azure ad proxy di applicazione](./application-proxy.md).

- Disaccoppiamento delle app dall'approccio delle credenziali locale nel tenant mediante la configurazione di [Azure ad come provider di identità universale attendibile](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Aumentare la produttività

I vantaggi di economia e sicurezza spingono le organizzazioni ad adottare Azure AD, ma l'adozione e la conformità complete sono più probabili se gli utenti ne traggono vantaggio. Con Azure AD è possibile:

- Migliorare l'esperienza di [Single Sign-On (SSO)](./what-is-single-sign-on.md) dell'utente finale tramite un accesso facile e sicuro a qualsiasi applicazione, da qualsiasi dispositivo e da qualsiasi posizione.

- Usare le funzionalità self-service IAM, ad esempio la [reimpostazione della password self-service](../authentication/concept-sspr-howitworks.md) e la [gestione dei gruppi selfservice](../enterprise-users/groups-self-service-management.md).

- Riduzione del sovraccarico amministrativo grazie alla gestione di una singola identità per ogni utente in ambienti cloud e locali:

  - [Automatizzare il provisioning](../app-provisioning/user-provisioning.md) degli account utente (in [Azure ad Gallery](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) in base alle identità di Azure ad
  - Accedere a tutte le app dal pannello app Web nel [portale di Azure ](https://portal.azure.com/)

- Consentire agli sviluppatori di proteggere l'accesso alle proprie app e migliorare l'esperienza dell'utente finale usando la [piattaforma Microsoft Identity](../develop/v2-overview.md) con Microsoft Authentication Library (MSAL).

- Consentire ai partner di accedere alle risorse cloud usando [Azure ad collaborazione B2B](../external-identities/what-is-b2b.md). Le risorse cloud eliminano il sovraccarico della configurazione della Federazione Point-to-Point con i partner.

### <a name="address-compliance-and-governance"></a>Conformità e governance degli indirizzi

Assicurare la conformità ai requisiti normativi applicando criteri di accesso aziendali e monitorando l'accesso degli utenti alle applicazioni e ai dati associati usando le API e gli strumenti di controllo integrati. Con Azure AD è possibile monitorare gli accessi alle applicazioni tramite i report che usano [gli strumenti per eventi imprevisti di sicurezza e monitoraggio eventi (Siem)](../reports-monitoring/plan-monitoring-and-reporting.md). È possibile accedere ai report dal portale o dalle API e controllare a livello di codice chi ha accesso alle applicazioni e rimuovere l'accesso agli utenti inattivi tramite le verifiche di accesso.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Pianificare le fasi di migrazione e la strategia di progetto

Quando i progetti tecnologici hanno esito negativo, spesso è dovuto a una mancata corrispondenza tra le aspettative, le parti interessate corrette non coinvolte o la mancanza di comunicazione. Verificare il successo pianificando il progetto.

### <a name="the-phases-of-migration"></a>Fasi della migrazione

Prima di iniziare a usare gli strumenti, è necessario comprendere come considerare il processo di migrazione. Grazie a diversi workshop diretti a clienti, è consigliabile eseguire le quattro fasi seguenti:

![Diagramma delle fasi della migrazione](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Assemblare il team del progetto

La migrazione delle applicazioni è un lavoro da parte del team ed è necessario assicurarsi che tutte le posizioni essenziali siano piene. Il supporto di leader aziendali senior è importante. Assicurarsi di coinvolgere il set di sponsor esecutivi, i decision maker aziendali e gli esperti in materia (PMI).

Durante il progetto di migrazione, una persona può soddisfare più ruoli o più persone soddisfano ogni ruolo, a seconda delle dimensioni e della struttura dell'organizzazione. È anche possibile che si disponga di una dipendenza da altri team che svolgono un ruolo chiave nell'orizzontale della sicurezza.

La tabella seguente include i ruoli principali e i relativi contributi:

| Ruolo          | Contributi                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Project Coach responsabile per guidare il progetto, tra cui:<br /> -Ottieni supporto tecnico Executive<br /> -importare le parti interessate<br /> -Gestisci pianificazioni, documentazione e comunicazioni |
| **Architetto identità/amministratore App Azure AD** | Sono responsabili dei seguenti elementi:<br /> -progettare la soluzione in collaborazione con gli stakeholder<br /> -documentare la progettazione della soluzione e le procedure operative per la consegna al team operativo<br /> -Gestisci gli ambienti di pre-produzione e di produzione |
| **Team operativo Active Directory locale** | Organizzazione che gestisce le diverse origini di identità locali, ad esempio foreste di Active Directory, directory LDAP, sistemi HR e così via.<br /> -eseguire le attività correttive necessarie prima della sincronizzazione<br /> -Specificare gli account di servizio necessari per la sincronizzazione<br /> -fornire l'accesso per configurare la Federazione per Azure AD |
| **Responsabile supporto IT** | Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. |
| **Proprietario della sicurezza**  | Rappresentante del team responsabile della sicurezza che può garantire che il piano soddisfi i requisiti di sicurezza dell'organizzazione. |
| **Proprietari tecnici delle applicazioni** | Include i proprietari tecnici delle app e dei servizi che si integreranno con Azure AD. Forniscono gli attributi di identità delle applicazioni che devono essere inclusi nel processo di sincronizzazione. In genere hanno una relazione con i rappresentanti di CSV. |
| **Proprietari aziendali dell'applicazione** | I colleghi rappresentanti che possono fornire l'input sull'esperienza utente e l'utilità di questa modifica dal punto di vista dell'utente e sono proprietari dell'aspetto aziendale generale dell'applicazione, che può includere la gestione dell'accesso. |
| **Gruppo pilota di utenti** | Utenti che eseguiranno test nell'ambito del lavoro giornaliero, dell'esperienza pilota e forniscono commenti e suggerimenti per guidare il resto delle distribuzioni. |

### <a name="plan-communications"></a>Pianificare le comunicazioni

Il coinvolgimento aziendale e la comunicazione efficaci sono la chiave per il successo. È importante fornire agli stakeholder e agli utenti finali un'Avenue per ottenere informazioni e tenersi informati degli aggiornamenti pianificati. Istruire tutti i valori della migrazione, le sequenze temporali previste e come pianificare eventuali problemi aziendali temporanei. Usare più vial, ad esempio brevi sessioni, messaggi di posta elettronica, riunioni uno-a-uno, banner e townhalls.

In base alla strategia di comunicazione scelta per l'app, è possibile che si voglia ricordare agli utenti il tempo di inattività in sospeso. È inoltre necessario verificare che non siano presenti modifiche recenti o un effetto aziendale che richiederebbero la posticipazione della distribuzione.

Nella tabella seguente sono riportate le comunicazioni minime consigliate per informare le parti interessate:

**Fasi del piano e strategia di progetto**:

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| Consapevolezza e valore aziendale/tecnico del progetto | Tutti tranne gli utenti finali |
| Richiesta per le app pilota | -Proprietari di app aziendali<br />-Proprietari tecnici delle app<br />-Architetti e team di identità |

**Fase 1: individuazione e ambito**:

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| -Richiesta di informazioni sull'applicazione<br />-Risultato dell'esercizio dell'ambito | -Proprietari tecnici delle app<br />-Proprietari di app aziendali |

**Fase 2: classificare le app e pianificare** il progetto pilota:

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| -Risultato delle classificazioni e cosa significa per la pianificazione della migrazione<br />-Pianificazione della migrazione preliminare | -Proprietari tecnici delle app<br /> -Proprietari di app aziendali |

**Fase 3-pianificazione della migrazione e del test**:

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| -Risultato del test della migrazione delle applicazioni | -Proprietari tecnici delle app<br />-Proprietari di app aziendali |
| -Notifica che la migrazione è imminente e spiega le esperienze degli utenti finali risultanti.<br />-Tempi di inattività in arrivo e completate comunicazioni, incluse le operazioni da eseguire, commenti e suggerimenti e come ottenere assistenza | -Utenti finali (e tutti gli altri) |

**Fase 4-gestire e ottenere informazioni dettagliate**:

| Comunicazione      | Destinatari                                          |
| ------------------ | ------------------------------------------------- |
| Analisi disponibili e modalità di accesso | -Proprietari tecnici delle app<br />-Proprietari di app aziendali |

### <a name="migration-states-communication-dashboard"></a>Dashboard di comunicazione degli Stati di migrazione

La comunicazione dello stato generale del progetto di migrazione è fondamentale, perché Mostra lo stato di avanzamento e aiuta i proprietari delle app le cui app sono in fase di migrazione per prepararsi allo spostamento. È possibile creare un dashboard semplice utilizzando Power BI o altri strumenti per la creazione di report per fornire visibilità sullo stato delle applicazioni durante la migrazione.

Gli Stati di migrazione che è possibile usare sono i seguenti:

| Stati di migrazione       | Piano di azione                                   |
| ---------------------- | --------------------------------------------- |
| **Richiesta iniziale** | Trovare l'app e contattare il proprietario per ulteriori informazioni |
| **Valutazione completata** | Il proprietario dell'app valuta i requisiti dell'app e restituisce il questionario dell'app</td>
| **Configurazione in corso** | Sviluppare le modifiche necessarie per gestire l'autenticazione rispetto a Azure AD |
| **Configurazione di test riuscita** | Valutare le modifiche e autenticare l'app nel tenant del Azure AD di test nell'ambiente di test |
| **Configurazione di produzione riuscita** | Modificare le configurazioni in modo che funzionino con il tenant di produzione di Active Directory e valutare l'autenticazione delle app nell'ambiente di test |
| **Completamento/disconnessione** | Distribuire le modifiche per l'app nell'ambiente di produzione ed eseguire il nel tenant di produzione Azure AD |

In questo modo, i proprietari dell'app sapranno quali sono le pianificazioni di migrazione e test delle app quando le app sono disponibili per la migrazione e quali sono i risultati provenienti da altre app di cui è già stata eseguita la migrazione. È anche possibile fornire collegamenti al database di bug tracker per consentire ai proprietari di visualizzare i problemi per le app di cui viene eseguita la migrazione.

### <a name="best-practices"></a>Procedure consigliate

Di seguito sono riportate le storie di successo di clienti e partner e le procedure consigliate:

- [Cinque suggerimenti per migliorare il processo di migrazione a Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) da parte di Patriot Consulting, un membro del nostro network partner che è incentrato sull'assistenza dei clienti nella distribuzione sicura di soluzioni cloud Microsoft.

- [Sviluppare una strategia di gestione dei rischi per la migrazione dell'applicazione Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) da Edgile, un partner incentrato sulle soluzioni IAM e di gestione dei rischi.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: individuare e definire l'ambito delle app

**L'individuazione e l'analisi delle applicazioni sono un'attività fondamentale per offrire un inizio valido.** È possibile che non si conoscano tutti gli elementi in modo da prepararsi alle app sconosciute.

### <a name="find-your-apps"></a>Trova le tue app

Il primo punto di decisione della migrazione di un'applicazione è costituito dalle app di cui eseguire la migrazione, che se ne deve rimanere e dalle app da deprecare. È sempre possibile deprecare le app che non verranno usate nell'organizzazione. Esistono diversi modi per trovare le app nell'organizzazione. **Durante l'individuazione delle app, assicurarsi di includere app pianificate e in fase di sviluppo. usare Azure AD per l'autenticazione in tutte le app future.**

**Uso di Active Directory Federation Services (AD FS) per raccogliere un inventario delle app corretto:**

- **Usare Azure AD Connect Health.** Se si dispone di una licenza di Azure AD Premium, è consigliabile distribuire [Azure ad Connect Health](../hybrid/how-to-connect-health-adfs.md) per analizzare l'utilizzo delle app nell'ambiente locale. È possibile utilizzare il [report dell'applicazione ADFS](./migrate-adfs-application-activity.md) (anteprima) per individuare le applicazioni ADFS di cui è possibile eseguire la migrazione e valutare la conformità dell'applicazione da migrare. Dopo aver completato la migrazione, distribuire [cloud Discovery](/cloud-app-security/set-up-cloud-discovery) che consente di monitorare continuamente Shadow it nell'organizzazione una volta che ci si trova nel cloud.

- **Ad FS l'analisi del log**. Se non si dispone di Azure AD Premium licenze, è consigliabile usare ADFS per Azure AD strumenti di migrazione delle app basati su [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Vedere la [Guida alla soluzione](./migrate-adfs-apps-to-azure.md):

[Migrazione di app da Active Directory Federation Services (AD FS) a Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Uso di altri provider di identità (IDP)

Per altri provider di identità, ad esempio Okta o ping, è possibile usare i relativi strumenti per esportare l'inventario delle applicazioni. È possibile considerare la possibilità di esaminare i principi di servizio registrati su Active Directory che corrispondono alle app Web all'interno dell'organizzazione.

### <a name="using-cloud-discovery-tools"></a>Uso degli strumenti di cloud Discovery

Nell'ambiente cloud è necessario avere una visibilità completa, controllare il viaggio dei dati e analisi sofisticate per trovare e combattere le minacce informatiche in tutti i servizi cloud. È possibile raccogliere l'inventario delle app cloud usando gli strumenti seguenti:

- **Broker di sicurezza cloud Access (CASB**): un [CASB](/cloud-app-security/) funziona in genere insieme al firewall per fornire visibilità sull'utilizzo delle applicazioni cloud dei dipendenti e consente di proteggere i dati aziendali da minacce Cybersecurity. Il report CASB può essere utile per determinare le app più usate nell'organizzazione e le destinazioni iniziali per eseguire la migrazione a Azure AD.

- **Cloud Discovery** : la configurazione di [cloud Discovery](/cloud-app-security/set-up-cloud-discovery)consente di ottenere visibilità sull'utilizzo delle app cloud e può individuare app it non approvate o shadow.

- **API** : per le app connesse all'infrastruttura cloud, è possibile usare le API e gli strumenti di questi sistemi per iniziare a eseguire un inventario delle app ospitate. Nell'ambiente Azure:

  - Usare il cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) per ottenere informazioni su siti Web di Azure.

  - Usare il cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) per ottenere informazioni sulle app Web di Azure.
D
  - È possibile trovare tutte le app in esecuzione su Microsoft IIS dalla riga di comando di Windows usando [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Usare [le applicazioni e le](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) [entità servizio](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) per ottenere informazioni su un'app e un'istanza di app in una directory in Azure ad.

### <a name="using-manual-processes"></a>Utilizzo di processi manuali

Una volta adottati gli approcci automatici descritti in precedenza, sarà possibile utilizzare le applicazioni. Tuttavia, è possibile prendere in considerazione le operazioni seguenti per assicurarsi di disporre di una corretta copertura in tutte le aree di accesso degli utenti:

- Per trovare le applicazioni in uso nell'organizzazione, contattare i vari proprietari aziendali dell'organizzazione.

- Eseguire uno strumento di ispezione HTTP nel server proxy oppure analizzare i log proxy per vedere dove il traffico viene indirizzato in genere.

- Esaminare i log dei siti Web del portale aziendale più diffusi per individuare i collegamenti più accessibili agli utenti.

- Contattare i dirigenti o altri membri aziendali chiave per assicurarsi di aver coperto le app cruciali per l'azienda.

### <a name="type-of-apps-to-migrate"></a>Tipo di app da migrare

Una volta trovate le app, si identificherà questi tipi di app nell'organizzazione:

- App che usano già i protocolli di autenticazione moderni

- App che usano i protocolli di autenticazione legacy che scegli di modernizzare

- App che usano protocolli di autenticazione legacy che scegli di non modernizzare

- Nuove app line-of-business (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>App che usano già l'autenticazione moderna

Le app già modernizzate sono le più probabilmente spostate in Azure AD. Queste app usano già i protocolli di autenticazione moderni, ad esempio SAML o OpenID Connect, e possono essere riconfigurati per l'autenticazione con Azure AD.

Oltre alle opzioni disponibili nella [raccolta Azure ad app,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) queste possono essere app già esistenti nell'organizzazione o qualsiasi app di terze parti da un fornitore che non fa parte della raccolta di Azure ad (applicazioni non incluse nella[raccolta)](./add-application-portal.md).

App legacy che scegli di modernizzare

Per le applicazioni legacy che si desidera modernizzare, il passaggio a Azure AD per l'autenticazione e l'autorizzazione principali sblocca tutta la potenza e la ricchezza di dati che i [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) e [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) sono in grado di offrire.

È consigliabile **aggiornare il codice dello stack di autenticazione** per queste applicazioni dal protocollo legacy, ad esempio l'autenticazione Windows-Integrated, la delega vincolata Kerberos, l'autenticazione basata su intestazioni HTTP, a un protocollo moderno, ad esempio SAML o OpenID Connect.

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>App legacy che scegli di non modernizzare

Per alcune app che usano protocolli di autenticazione legacy, a volte la modernizzazione dell'autenticazione non è la scelta ideale per motivi aziendali. Sono inclusi i tipi di app seguenti:

- App conservate in locale per motivi di conformità o controllo.

- App connesse a un provider di identità o di Federazione locale che non si vuole modificare.

- App sviluppate con standard di autenticazione locali che non si prevede di spostare

Azure AD può offrire notevoli vantaggi a queste app legacy, dal momento che è possibile abilitare le funzionalità moderne di Azure AD sicurezza e governance, ad esempio [multi-factor authentication](../authentication/concept-mfa-howitworks.md), [l'accesso condizionale](../conditional-access/overview.md), la [protezione delle identità](../identity-protection/index.yml), [l'accesso alle applicazioni delegate](./access-panel-manage-self-service-access.md)e le verifiche di [accesso](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) a queste app senza toccare l'app.

È possibile iniziare **estendendo queste app nel cloud** con Azure ad [proxy di applicazione](./application-proxy-configure-single-sign-on-password-vaulting.md) usando semplici metodi di autenticazione, ad esempio l'insieme di credenziali delle password, per velocizzare la migrazione degli utenti o tramite l'integrazione dei [partner](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) con i controller di distribuzione delle applicazioni che potrebbero essere già stati distribuiti.

### <a name="new-line-of-business-lob-apps"></a>Nuove app line-of-business (LoB)

In genere si sviluppano app LoB per l'uso interna dell'organizzazione. Se nella pipeline sono presenti nuove app, è consigliabile usare la [piattaforma Microsoft Identity](../develop/v2-overview.md) per implementare OpenID Connect.

### <a name="apps-to-deprecate"></a>App da deprecare

Le app senza proprietari non crittografati e la manutenzione e il monitoraggio chiari presentano un rischio per la sicurezza per l'organizzazione. Prendere in considerazione la deprecazione delle applicazioni nei casi seguenti:

- le **funzionalità sono altamente ridondanti** con altri sistemi • non esiste **alcun proprietario aziendale**

- non esiste ovviamente **alcun utilizzo**.

Si consiglia di **non deprecare le applicazioni critiche** per l'azienda con un elevato livello di influenza. In questi casi, collaborare con i proprietari aziendali per determinare la strategia corretta.

### <a name="exit-criteria"></a>Criteri uscita

Questa fase ha avuto esito positivo con:

- Una conoscenza approfondita dei sistemi nell'ambito della migrazione (che è possibile ritirare dopo lo spostamento in Azure AD)

- Un elenco di app che include:

  - A quali sistemi si connettono le app
  - Da dove e in quali dispositivi gli utenti accedono
  - Indica se verranno migrati, deprecati o connessi con [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> È possibile scaricare il foglio di documento di [individuazione dell'applicazione](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) per registrare le applicazioni di cui si desidera eseguire la migrazione Azure ad l'autenticazione e quelle che si desidera lasciare ma gestire utilizzando [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: classificare le app e pianificare il progetto pilota

La classificazione della migrazione delle app è un esercizio importante. Non è necessario eseguire la migrazione e la transizione di tutte le app nello stesso momento. Dopo aver raccolto le informazioni su ogni app, è possibile razionalizzare le app da migrare per prime e che potrebbero richiedere più tempo.

### <a name="classify-in-scope-apps"></a>Classificare le app in ambito

Un modo per considerare questo problema è lungo gli assi della criticità aziendale, dell'utilizzo e della durata, ciascuno dei quali dipende da più fattori.

### <a name="business-criticality"></a>Criticità aziendale

La criticità aziendale avrà dimensioni diverse per ogni azienda, ma le due misure che è opportuno considerare sono **caratteristiche, funzionalità** e **profili utente**. Assegnare le app con funzionalità univoche a un valore del punto più elevato rispetto a quelle con funzionalità ridondanti o obsolete.

![Diagramma degli spettri di funzionalità & funzionalità e profili utente](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Utilizzo

Le applicazioni con **numeri di utilizzo elevato** dovrebbero ricevere un valore più elevato rispetto alle app con utilizzo ridotto. Assegnare un valore superiore alle app con utenti del team esterno, dirigente o di sicurezza. Per ogni app nel portfolio di migrazione, completare le valutazioni.

![Diagramma degli spettri di volume utente e larghezza utente](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Una volta determinati i valori per la criticità aziendale e l'utilizzo, è possibile determinare la **durata dell'applicazione** e creare una matrice di priorità. Vedere una matrice di questo tipo:

![Diagramma A triangolo che mostra le relazioni tra l'utilizzo, la durata prevista e la criticità aziendale](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Assegnare priorità alle app per la migrazione

È possibile scegliere di iniziare la migrazione dell'app con le app con priorità più bassa o con la priorità più alta in base alle esigenze dell'organizzazione.

In uno scenario in cui è possibile che non si abbia esperienza nell'uso di Azure AD e dei servizi di identità, provare a trasferire prima le **app con priorità più bassa** a Azure ad. Questo consente di ridurre al minimo l'effetto aziendale ed è possibile creare un momento. Dopo aver spostato queste app e aver acquisito la confidenza degli stakeholder, è possibile continuare a eseguire la migrazione delle altre app.

Se non è presente alcuna priorità chiara, è consigliabile trasferire prima le app presenti nella [raccolta Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) e supportare più provider di identità (ADFS o Okta) perché sono più facili da integrare. È probabile che queste app siano le **app con priorità più elevata** nell'organizzazione. Per semplificare l'integrazione delle applicazioni SaaS con Azure AD, è stata sviluppata una raccolta di [esercitazioni](../saas-apps/tutorial-list.md) che illustrano la configurazione.

Quando si ha una scadenza per la migrazione delle app, il bucket delle app con priorità più elevata prenderà il carico di lavoro principale. A questo punto è possibile selezionare le app con priorità inferiore perché non modificheranno il costo anche se la scadenza è stata spostata. Anche se è necessario rinnovare la licenza, sarà per un importo ridotto.

Oltre a questa classificazione e a seconda dell'urgenza della migrazione, è anche possibile prendere in considerazione la possibilità di inserire una **pianificazione della migrazione** all'interno della quale i proprietari delle app devono impegnarsi per eseguire la migrazione delle app. Al termine di questo processo, è necessario disporre di un elenco di tutte le applicazioni in bucket con priorità per la migrazione.

### <a name="document-your-apps"></a>Documentare le app

Per prima cosa, iniziare raccogliendo i dettagli principali sulle applicazioni. Il [foglio](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)di lavoro di individuazione delle applicazioni ti aiuterà a prendere rapidamente le decisioni di migrazione e a ricevere una raccomandazione per il tuo gruppo aziendale in pochissimo tempo.

Le informazioni importanti per la decisione di migrazione includono:

- **Nome dell'app** : qual è l'app nota come l'azienda?

- **Tipo di app** : si tratta di un'app SaaS di terze parti? Un'app Web line-of-business personalizzata? Un'API?

- **Criticità aziendale** : è la sua criticità elevata? Basso? O in un punto compreso tra?

- **Volume di accesso utente** : tutti gli utenti accedono a questa app o a poche persone?

- **Durata pianificata** : per quanto tempo l'app sarà in circolazione? Meno di sei mesi? Più di due anni?

- **Provider di identità corrente** : qual è l'IDP primario per questa app? O si basa sull'archiviazione locale?

- **Metodo di autenticazione** : l'app esegue l'autenticazione usando gli standard aperti?

- **Se si prevede di aggiornare il codice dell'app** , l'app è in fase di sviluppo pianificato o attivo?

- **Se si prevede di proteggere l'app in locale** , si vuole lasciare l'app nel Data Center a lungo termine?

- **Se l'app dipende da altre app o API** , l'app chiama attualmente in altre app o API?

- **Se l'app si trova nella raccolta di Azure ad** , l'app è attualmente già integrata con la [raccolta di Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Altri dati che aiuteranno in seguito, ma che non è necessario prendere una decisione di migrazione immediata includono:

- **URL app** : dove gli utenti accedono all'app?

- **Descrizione dell'app** : che cos'è una breve descrizione dell'applicazione?

- **Proprietario dell'app** : chi nell'azienda è il POC principale per l'app?

- **Commenti o note generali** : qualsiasi altra informazione generale relativa all'app o alla proprietà dell'azienda

Dopo aver classificato l'applicazione e aver documentato i dettagli, assicurarsi di ottenere l'acquisto del proprietario aziendale per la strategia di migrazione pianificata.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

Le app selezionate per il progetto pilota devono rappresentare i requisiti di sicurezza e identità chiave dell'organizzazione ed è necessario avere un chiaro acquisto dai proprietari dell'applicazione. I progetti pilota vengono in genere eseguiti in un ambiente di test separato. Vedere le [procedure consigliate per i piloti](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) nella pagina piani di distribuzione.

**Non dimenticare i partner esterni.** Assicurarsi di partecipare alle pianificazioni e ai test di migrazione. Infine, assicurarsi di disporre di un modo per accedere al supporto tecnico in caso di problemi di rilievo.

### <a name="plan-for-limitations"></a>Pianificare le limitazioni

Sebbene alcune app siano facili da migrare, altre potrebbero richiedere più tempo a causa di più server o istanze. Ad esempio, la migrazione di SharePoint potrebbe richiedere più tempo a causa di pagine di accesso personalizzate.

Molti fornitori di app SaaS addebitano la modifica della connessione SSO. Verificarli e pianificare questo problema.

Azure AD dispone anche di [limiti e restrizioni del servizio](../enterprise-users/directory-service-limits-restrictions.md) di cui è necessario essere a conoscenza.

### <a name="app-owner-sign-off"></a>Approvazione del proprietario dell'app

Le applicazioni aziendali critiche e usate universalmente potrebbero avere bisogno di un gruppo di utenti pilota per testare l'app nella fase pilota. Dopo aver testato un'app nell'ambiente di pre-produzione o pilota, assicurarsi che i proprietari aziendali dell'app si disconnettano sulle prestazioni prima della migrazione dell'app e di tutti gli utenti all'uso di Azure AD per l'autenticazione.

### <a name="plan-the-security-posture"></a>Pianificare il comportamento di sicurezza

Prima di avviare il processo di migrazione, è necessario prendere in considerazione il comportamento di sicurezza che si vuole sviluppare per il sistema di identità aziendale. Questo è basato sulla raccolta di questi importanti set di informazioni: **identità, dispositivi e posizioni che accedono ai dati.**

### <a name="identities-and-data"></a>Identità e dati

La maggior parte delle organizzazioni ha requisiti specifici relativi alle identità e alla protezione dei dati che variano in base al segmento di settore e alle funzioni di lavoro all'interno delle organizzazioni Vedere le [configurazioni di identità e accesso ai dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations) per le raccomandazioni, incluso un set di [criteri di accesso condizionale](../conditional-access/overview.md) e funzionalità correlate.

È possibile usare queste informazioni per proteggere l'accesso a tutti i servizi integrati con Azure AD. Queste raccomandazioni sono allineate con Microsoft Secure score e il [Punteggio Identity in Azure ad](../fundamentals/identity-secure-score.md). Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità

- Pianificare miglioramenti per la sicurezza delle identità

- Verificare il successo dei miglioramenti apportati

Ciò consentirà anche di implementare i [cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md). Usare le linee guida come punto di partenza per l'organizzazione e modificare i criteri per soddisfare i requisiti specifici dell'organizzazione.

### <a name="who-is-accessing-your-data"></a>Chi sta accedendo ai dati?

Esistono due categorie principali di utenti delle app e delle risorse che Azure AD supporta:

- **Interno:** Dipendenti, appaltatori e fornitori con account all'interno del provider di identità. Questa operazione potrebbe richiedere ulteriori pivot con regole diverse per i dirigenti o la leadership rispetto ad altri dipendenti.

- **Esterno:** Fornitori, fornitori, distributori o altri partner commerciali che interagiscono con l'organizzazione nel corso di attività regolari con [Azure ad collaborazione B2B.](../external-identities/what-is-b2b.md)

È possibile definire gruppi per questi utenti e popolare tali gruppi in modi diversi. È possibile scegliere se un amministratore deve aggiungere manualmente membri a un gruppo oppure abilitare l'appartenenza al gruppo selfservice. È possibile stabilire regole che aggiungono automaticamente membri in gruppi in base ai criteri specificati usando i [gruppi dinamici](../enterprise-users/groups-dynamic-membership.md).

Gli utenti esterni possono anche fare riferimento ai clienti. [Azure ad B2C](../../active-directory-b2c/overview.md), un prodotto distinto supporta l'autenticazione del cliente. Ma esula dall'ambito di questo documento.

### <a name="devicelocation-used-to-access-data"></a>Dispositivo/percorso usato per accedere ai dati

È importante anche il dispositivo e la posizione usati da un utente per accedere a un'app. I dispositivi connessi fisicamente alla rete aziendale sono più sicuri. Le connessioni dall'esterno della rete tramite VPN potrebbero richiedere il controllo.

![Diagramma che mostra la relazione tra la posizione utente e l'accesso ai dati](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Con questi aspetti di risorse, utenti e dispositivi, è possibile scegliere di usare Azure AD funzionalità di [accesso condizionale](../conditional-access/overview.md) . L'accesso condizionale va oltre le autorizzazioni utente: si basa su una combinazione di fattori, ad esempio l'identità di un utente o un gruppo, la rete a cui l'utente è connesso, il dispositivo e l'applicazione in uso e il tipo di dati a cui si sta tentando di accedere. L'accesso concesso all'utente si adatta a questo più ampio set di condizioni.

### <a name="exit-criteria"></a>Criteri uscita

L'operazione ha avuto esito positivo in questa fase quando si:

- Informazioni sulle app
  - Sono state documentate in modo completo le app di cui si intende eseguire la migrazione
  - App con priorità in base alla criticità aziendale, al volume di utilizzo e alla durata

- Hanno selezionato le app che rappresentano i requisiti per un progetto pilota

- Acquisto da un proprietario aziendale alla strategia e alla priorità

- Informazioni sulle esigenze di sicurezza e su come implementarle

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: pianificare la migrazione e i test

Una volta ottenuto il buy-in aziendale, il passaggio successivo consiste nell'iniziare a migrare queste app per Azure AD l'autenticazione.

### <a name="migration-tools-and-guidance"></a>Strumenti e linee guida per la migrazione

Usare gli strumenti e le linee guida seguenti per seguire i passaggi esatti necessari per eseguire la migrazione delle applicazioni a Azure AD:

- **Indicazioni generali** per la migrazione: usare il modulo white paper, gli strumenti, i modelli di posta elettronica e le applicazioni nel [Azure ad Apps Migration Toolkit](./migration-resources.md) per individuare, classificare ed eseguire la migrazione delle app.

- **Applicazioni SaaS** : vedere l'elenco di [centinaia di esercitazioni di app Saas](../saas-apps/tutorial-list.md) e il [piano di distribuzione completo Azure ad SSO](https://aka.ms/ssodeploymentplan) per esaminare il processo end-to-end.

- **Applicazioni in esecuzione in locale** : informazioni sul [proxy di applicazione Azure ad](./application-proxy.md) e uso del piano di [distribuzione completo Azure ad proxy di applicazione](https://aka.ms/AppProxyDPDownload) per iniziare rapidamente.

- **App** in fase di sviluppo: leggi le istruzioni dettagliate per l' [integrazione](../develop/quickstart-register-app.md) e la [registrazione](../develop/quickstart-register-app.md) .

Dopo la migrazione, è possibile scegliere di inviare la comunicazione per informare gli utenti della distribuzione riuscita e ricordare i nuovi passaggi da eseguire.

### <a name="plan-testing"></a>Panificare i test

Durante il processo di migrazione, l'app potrebbe avere già un ambiente di test usato durante le distribuzioni regolari. È possibile continuare a usare questo ambiente per i test di migrazione. Se un ambiente di testing non è attualmente disponibile, potrebbe essere possibile configurarne uno usando app Azure servizio o macchine virtuali di Azure, a seconda dell'architettura dell'applicazione. È possibile scegliere di configurare un tenant di test Azure AD separato da usare durante lo sviluppo delle configurazioni dell'app. Questo tenant verrà avviato in uno stato pulito e non verrà configurato per la sincronizzazione con alcun sistema.

È possibile testare ogni app effettuando l'accesso con un utente di test e assicurarsi che tutte le funzionalità siano uguali a quelle prima della migrazione. Se durante il test si stabilisce che gli utenti dovranno [aggiornare le impostazioni](/azure/active-directory/authentication/howto-mfa-userstates) di autenticazione a più fattori o [SSPR](../authentication/tutorial-enable-sspr.md)o se si aggiunge questa funzionalità durante la migrazione, assicurarsi di aggiungerla al piano di comunicazione dell'utente finale. Vedere [modelli](https://aka.ms/mfatemplates) di comunicazione dell'utente finale di multi-factor authentication e [SSPR](https://aka.ms/ssprtemplates) .

Dopo aver eseguito la migrazione delle app, passare alla [portale di Azure](https://aad.portal.azure.com/) per verificare se la migrazione ha avuto esito positivo. Seguire le istruzioni riportate di seguito:

- Selezionare **applicazioni aziendali &gt; tutte le applicazioni** e trovare l'app nell'elenco.

- Selezionare **Gestisci &gt; utenti e gruppi** per assegnare almeno un utente o un gruppo all'app.

- Selezionare **Gestisci &gt; accesso condizionale**. Esaminare l'elenco dei criteri e assicurarsi che non si stia bloccando l'accesso all'applicazione con [criteri di accesso condizionale](../conditional-access/overview.md).

A seconda di come si configura l'app, verificare che SSO funzioni correttamente.

| Tipo di autenticazione      | Test                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Selezionare **&gt; autorizzazioni per le applicazioni aziendali** e assicurarsi di avere acconsentito all'applicazione da usare nell'organizzazione nelle impostazioni utente per l'app. |
| **SSO basato su SAML** | Usare il pulsante [test impostazioni SAML](./debug-saml-sso-issues.md) disponibile in **Single Sign-on.** |
| **SSO basato su password** | Scaricare e installare l' [estensione per l'accesso sicuro di app](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Questa estensione consente di avviare tutte le app cloud dell'organizzazione che richiedono l'uso di un processo SSO. |

| **[Proxy applicazione](./application-proxy.md)** | Verificare che il connettore sia in esecuzione e che sia stato assegnato all'applicazione. Per ulteriore assistenza, vedere la [Guida alla risoluzione dei problemi del proxy di applicazione](./application-proxy-troubleshoot.md) . |

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi, consultare la [Guida alla risoluzione dei](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) problemi delle app per ottenere assistenza. È anche possibile consultare gli articoli sulla risoluzione dei problemi, vedere [problemi di accesso a app configurate Single Sign-on basate su SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps).

### <a name="plan-rollback"></a>Pianificare il rollback

Se la migrazione ha esito negativo, la strategia migliore consiste nell'eseguire il rollback e il test. Ecco i passaggi che è possibile eseguire per attenuare i problemi di migrazione:

- **Eseguire screenshot** della configurazione esistente dell'app. È possibile tornare indietro se è necessario riconfigurare l'app ancora una volta.

- È anche possibile **fornire collegamenti all'autenticazione legacy**, se si sono verificati problemi con l'autenticazione cloud.

- Prima di completare la migrazione, non **modificare la configurazione esistente** con il provider di identità precedente.

- Per iniziare, eseguire **la migrazione delle app che supportano più IDP**. Se si verifica un problema, è sempre possibile passare alla configurazione di IdP preferita.

- Assicurarsi che l'esperienza dell'app disponga di un **pulsante di feedback** o di puntatori ai problemi del **supporto tecnico** .

### <a name="exit-criteria"></a>Criteri uscita

L'operazione è riuscita in questa fase quando si dispone di:

- Determinazione del modo in cui verrà eseguita la migrazione di ogni app

- Revisione degli strumenti di migrazione

- Pianificazione dei test, inclusi i gruppi e gli ambienti di test

- Rollback pianificato

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: pianificare la gestione e le informazioni dettagliate

Una volta migrate le app, è necessario assicurarsi che:

- Gli utenti possono accedere e gestire in modo sicuro

- È possibile ottenere informazioni dettagliate appropriate sull'utilizzo e sull'integrità delle app

È consigliabile eseguire le azioni seguenti in modo appropriato per l'organizzazione.

### <a name="manage-your-users-app-access"></a>Gestire l'accesso alle app degli utenti

Dopo aver eseguito la migrazione delle app, è possibile migliorare l'esperienza utente in molti modi

**Rendi individuabili le app**

**Puntare l'utente** all'esperienza del portale [app](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Qui possono accedere a tutte le app basate sul cloud, le app che si rendono disponibili usando [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md)e le app che usano il [proxy di applicazione](./application-proxy.md) purché dispongano delle autorizzazioni per accedere a tali app.


È possibile guidare gli utenti su come individuare le app:

- Usare la funzionalità [Single Sign-on esistente](./view-applications-portal.md) per **collegare gli utenti a qualsiasi app**


- Abilitare [l'accesso alle applicazioni self-service](./manage-self-service-access.md)a un'app e **consentire agli utenti di aggiungere app curate**

- [Nascondi le applicazioni degli utenti finali](./hide-application-from-user-portal.md) (app Microsoft predefinite o altre app) per **rendere le app necessarie per essere più individuabili**

### <a name="make-apps-accessible"></a>Rendere accessibili le app

**Consentire agli utenti di accedere alle app dai propri dispositivi mobili**. Gli utenti possono accedere al portale app personali con il browser gestito da Intune nei dispositivi [iOS](./hide-application-from-user-portal.md) 7,0 o versione successiva o [Android](./hide-application-from-user-portal.md) .

Gli utenti possono scaricare un **browser gestito da Intune**:

- **Per i dispositivi Android**, da [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Per i dispositivi Apple**, dall' [App Store di Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) oppure è possibile scaricare l' [app per dispositivi mobili My Apps per iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Consentire agli utenti di aprire le app da un'estensione del browser.**

Gli utenti possono [scaricare l'estensione app Secure Sign-in](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) in [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) o [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e possono avviare le app direttamente dalla barra del browser per:

- **Cercare le app e visualizzare le app usate più di recente**

- **Converte automaticamente gli URL interni** configurati nel [proxy di applicazione](./application-proxy.md) agli URL esterni appropriati. Gli utenti possono ora lavorare con i collegamenti con cui hanno familiarità, indipendentemente da dove si trovano.

**Consentire agli utenti di aprire le app da Office.com.**

Gli utenti possono passare a [Office.com](https://www.office.com/) per **cercare le proprie app e fare in modo che le app usate più di recente vengano visualizzate** direttamente da dove funzionano.

### <a name="secure-app-access"></a>Proteggere l'accesso alle app

Azure AD fornisce un percorso di accesso centralizzato per la gestione delle app migrate. Passare alla [portale di Azure](https://portal.azure.com/) e abilitare le funzionalità seguenti:

- **Proteggere l'accesso degli utenti alle app.** Abilitare i [criteri di accesso condizionale](../conditional-access/overview.md)o [Identity Protection](../identity-protection/overview-identity-protection.md)per proteggere l'accesso degli utenti alle applicazioni in base allo stato del dispositivo, alla posizione e molto altro.

- **Provisioning automatico.** Configurare il [provisioning automatico degli utenti](../app-provisioning/user-provisioning.md) con diverse app SaaS di terze parti a cui gli utenti devono accedere. Oltre a creare le identità utente, include la manutenzione e la rimozione delle identità utente durante la modifica dello stato o dei ruoli.

- **Delega gestione accesso utenti** . Se necessario, abilitare l'accesso alle applicazioni self-service alle app e *assegnare un responsabile approvazione aziendale per approvare l'accesso a tali app*. Usare la [gestione dei gruppi self-service](../enterprise-users/groups-self-service-management.md)per i gruppi assegnati a raccolte di app.

- **Delega accesso amministratore.** usare il **ruolo della directory** per assegnare un ruolo di amministratore (ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud o sviluppatore di applicazioni) all'utente.

### <a name="audit-and-gain-insights-of-your-apps"></a>Controlla e Ottieni informazioni dettagliate sulle tue app

È anche possibile usare la [portale di Azure](https://portal.azure.com/) per controllare tutte le app da una posizione centralizzata,

- **Controlla la tua app** usando * * applicazioni aziendali, controlla o accedi alle stesse informazioni dalla [Azure ad API](../reports-monitoring/concept-reporting-api.md) per la creazione di report per integrarti negli strumenti preferiti.

- **Visualizzare le autorizzazioni per un'app** usando **le applicazioni aziendali, le autorizzazioni per le** app che usano OAuth/OpenID Connect.

- **Ottenere informazioni dettagliate sull'accesso** usando **le applicazioni aziendali, accessi**. Accedere alle stesse informazioni dall'API per la [creazione di report Azure ad.](../reports-monitoring/concept-reporting-api.md)

- **Visualizza l'utilizzo dell'app** dal pacchetto di [contenuto Azure ad Power bi](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Criteri uscita

L'operazione ha avuto esito positivo in questa fase quando si:

- Fornire agli utenti l'accesso sicuro alle app

- Gestisci per controllare e ottenere informazioni dettagliate sulle app migrate

### <a name="do-even-more-with-deployment-plans"></a>Eseguire ancora di più con i piani di distribuzione

I piani di distribuzione illustrano i valori aziendali, la pianificazione, i passaggi di implementazione e la gestione di soluzioni Azure AD, inclusi gli scenari di migrazione delle app. Uniscono tutti gli elementi necessari per iniziare a distribuire e ottenere valore da Azure AD funzionalità. Le guide alla distribuzione includono contenuto come le procedure consigliate di Microsoft, le comunicazioni degli utenti finali, le guide alla pianificazione, i passaggi di implementazione, i test case e altro ancora.

Molti [piani di distribuzione](../fundamentals/active-directory-deployment-plans.md) sono disponibili per l'uso e stiamo sempre facendo altro.

### <a name="contact-support"></a>Contattare il supporto tecnico

Visitare i seguenti collegamenti di supporto per creare o tenere traccia del ticket di supporto e monitorare l'integrità.

- **Supporto tecnico di Azure:** È possibile chiamare [supporto tecnico Microsoft](https://azure.microsoft.com/support) e aprire un ticket per qualsiasi Azure

Problemi di distribuzione delle identità a seconda del Contratto Enterprise con Microsoft.

- **FastTrack**: se è stato acquistato Enterprise Mobility and Security (EMS) o Azure ad Premium licenze, l'utente è idoneo a ricevere assistenza per la distribuzione dal [programma FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Coinvolgere il team di progettazione del prodotto:** Se si sta lavorando a una distribuzione principale dei clienti con milioni di utenti, si ha diritto a supportare dal team account Microsoft o dal progettista di soluzioni cloud. In base alla complessità di distribuzione del progetto, è possibile collaborare direttamente con il [team di progettazione del prodotto Azure Identity.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog sull'identità Azure ad:** Sottoscrivere il [Blog sull'identità del Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) per rimanere sempre aggiornati con tutti gli annunci più recenti, le immersioni approfondite e le informazioni di roadmap fornite direttamente dal team di progettazione delle identità.
