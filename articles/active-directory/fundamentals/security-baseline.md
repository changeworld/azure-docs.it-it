---
title: Baseline della sicurezza di Azure per Azure Active Directory
description: La linea di base di sicurezza Azure Active Directory fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286018"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Baseline della sicurezza di Azure per Azure Active Directory

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza di Azure](../../security/benchmarks/overview.md) a Azure Active Directory. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure Active Directory. 

> [!NOTE]
> I **controlli** non applicabili a Azure Active Directory o per i quali la responsabilità è Microsoft, sono stati esclusi da questa baseline. Per informazioni su come Azure Active Directory viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Azure Active Directory sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato per le risorse Azure Active Directory. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio, ad esempio "AzureActiveDirectory" nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

- [Comprendere e usare i tag di servizio](../../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Linee guida**: usare Azure Active Directory (Azure ad) come servizio di gestione delle identità e degli accessi predefinito. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in: 
- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS. 
- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale. 
 

La protezione di Azure AD deve essere una priorità nella procedura di sicurezza del cloud dell'organizzazione. Azure AD offre un punteggio di sicurezza delle identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare in che modo la configurazione aderisce alle procedure consigliate e per apportare miglioramenti al comportamento di sicurezza. 

Azure AD supporta l'identità esterna che consente agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con la loro identità esterna. 

- [Tenancy in Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Come creare e configurare un'istanza di Azure AD](active-directory-access-create-new-tenant.md)

- [Usare i provider di identità esterne per l'applicazione](/azure/active-directory/b2b/identity-providers)

- [Che cos'è il punteggio di sicurezza delle identità in Azure Active Directory](identity-secure-score.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: usare l'identità gestita per le risorse di Azure per account non umani, ad esempio servizi o automazione, è consigliabile usare la funzionalità di identità gestita da Azure invece di creare un account umano più potente per accedere o eseguire le risorse. È possibile eseguire l'autenticazione in modalità nativa ai servizi o alle risorse di Azure che supportano l'autenticazione Azure Active Directory (Azure AD) tramite una regola di concessione dell'accesso predefinita senza usare le credenziali hardcoded nel codice sorgente o nei file di configurazione. Non è possibile assegnare identità gestite di Azure a risorse Azure AD ma Azure AD è il meccanismo di autenticazione con identità gestite assegnate ad altre risorse del servizio.

- [Identità gestita per le risorse di Azure](../managed-identities-azure-resources/overview.md)

- [Servizi che supportano l'identità gestita per le risorse di Azure](../managed-identities-azure-resources/services-support-managed-identities.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Linee guida**: usare Azure Active Directory per fornire la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Ciò consente all'accesso Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e maggior visibilità e controllo.

 
- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: usare Azure Active Directory per supportare i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.
- Autenticazione a più fattori: abilitare l'autenticazione a più fattori di Azure AD e seguire le raccomandazioni dell'identità Centro sicurezza di Azure e di Gestione degli accessi per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti gli utenti, agli utenti selezionati o a livello di singolo utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password: Windows Hello for business, app Microsoft Authenticator e metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

Azure AD supporta l'autenticazione Legacy basata su password, ad esempio account solo cloud (account utente creati direttamente in Azure AD) con criteri password di base o account ibridi (account utente provenienti da Active Directory locali) che seguiranno i criteri password locali. Quando si usa l'autenticazione basata su password, Azure AD fornisce una funzionalità di protezione delle password che impedisce agli utenti di impostare password facili da indovinare. Microsoft fornisce un elenco globale delle password escluse che vengono aggiornate in base ai dati di telemetria e i clienti possono ampliare l'elenco in base alle esigenze, ad esempio personalizzazione, riferimenti culturali e così via. Questa protezione con password può essere usata solo per gli account cloud e ibridi.

L'autenticazione basata solo sulle credenziali password è soggetta ai metodi di attacco più diffusi. Per una maggiore sicurezza, usare l'autenticazione avanzata, ad esempio l'autenticazione a più fattori e un criterio di password complessa. Per le applicazioni di terze parti e i servizi del Marketplace che possono avere password predefinite, è necessario modificarli durante l'installazione iniziale del servizio.

 
- [Come distribuire Azure AD multi-factor authentication](../authentication/howto-mfa-getstarted.md) 

 

 
- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Criteri password predefiniti di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminare le password non valide usando la funzionalità di protezione password di Azure AD](../authentication/concept-password-ban-bad.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Indicazioni**: Azure Active Directory fornisce le origini dati seguenti:

 
- Accessi: il report sugli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

 
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

 
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

 
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

 

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

 

 
Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione.

 

 
Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

 

 
- [Report sulle attività di controllo in Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md) 

 

 
- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../../security-center/alerts-reference.md) 

 

 
- [Come integrare i log attività di Azure in Monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: usare l'accesso condizionale Azure Active Directory (Azure ad) per un controllo di accesso più granulare in base alle condizioni definite dall'utente, ad esempio gli accessi degli utenti da determinati intervalli IP dovranno usare l'autenticazione a più fattori per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per casi d'uso diversi.

 
- [Panoramica dell'accesso condizionale Azure AD](../conditional-access/overview.md) 

 

 
- [Criteri comuni di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [È possibile configurare la gestione della sessione di autenticazione con l'Accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: proteggere l'accesso degli utenti alle applicazioni legacy

**Linee guida**: assicurarsi di disporre di controlli di accesso e monitoraggio della sessione moderni per le applicazioni legacy e i dati archiviati ed elaborati. Mentre le VPN sono comunemente usate per accedere alle applicazioni legacy, spesso hanno solo il controllo di accesso di base e il monitoraggio limitato della sessione.

 
Azure AD proxy di applicazione consente di pubblicare applicazioni locali legacy per gli utenti remoti con SSO, convalidando in modo esplicito l'attendibilità di utenti e dispositivi remoti con Azure AD l'accesso condizionale.

 

 
In alternativa, Microsoft Cloud App Security è un servizio CASB (cloud Access Security Broker) che può fornire controlli per il monitoraggio delle sessioni dell'applicazione dell'utente e delle azioni di blocco (per le applicazioni locali legacy e le applicazioni SaaS (cloud Software as a Service).

 

 
- [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Procedure consigliate Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteggere e limitare gli utenti con privilegi elevati

**Linee guida**: i ruoli predefiniti più critici sono Azure ad amministratore globale e amministratore del ruolo con privilegi, perché gli utenti assegnati a questi due ruoli possono delegare i ruoli di amministratore:

- Amministratore globale: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative di Azure AD, nonché ai servizi che usano Azure AD identità.

- Amministratore del ruolo con privilegi: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure AD, oltre che all'interno di Azure AD Privileged Identity Management (PIM). Inoltre, questo ruolo consente la gestione di tutti gli aspetti di PIM e delle unità amministrative.

Se si utilizzano ruoli personalizzati con determinate autorizzazioni con privilegi assegnati, è possibile che si disponga di altri ruoli critici che devono essere regolati. Inoltre, è possibile che si desideri applicare controlli simili all'account Administrator degli asset aziendali critici.

Azure AD dispone di account con privilegi elevati: gli utenti e le entità servizio direttamente o indirettamente assegnati o idonei ai ruoli amministratore globale o amministratore del ruolo con privilegi e altri ruoli con privilegi elevati in Azure AD e Azure.

Limitare il numero di account con privilegi elevati e proteggere questi account a un livello elevato, perché gli utenti con questo privilegio possono leggere e modificare direttamente o indirettamente tutte le risorse nell'ambiente Azure.

È possibile abilitare l'accesso con privilegi just-in-time (JIT) alle risorse di Azure e ad Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti ne hanno la necessità. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

- [Autorizzazioni per il ruolo di amministratore in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Linee guida**: usare Azure Active Directory Privileged Identity Management e l'autenticazione a più fattori per limitare l'accesso amministrativo ai sistemi aziendali critici.

- [Privileged Identity Management l'approvazione delle richieste di attivazione del ruolo](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Autenticazione a più fattori e accesso condizionale](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Linee guida**: esaminare regolarmente le assegnazioni di accesso all'account utente per assicurarsi che gli account e il loro accesso siano validi, soprattutto per i ruoli con privilegi elevati, tra cui amministratore globale e amministratore del ruolo con privilegi. È possibile usare le verifiche di accesso Azure Active Directory (Azure AD) per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo, sia per i ruoli di Azure AD che per i ruoli di Azure. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Inoltre, Azure Privileged Identity Management può anche essere configurato in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

- [Creare una verifica di accesso dei ruoli Azure AD in Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Linee guida**: per evitare che vengano accidentalmente bloccati dall'organizzazione Azure ad, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici.
Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Indicazioni**: usare le funzionalità di gestione dei diritti Azure ad per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.

- [Informazioni sulla gestione dei diritti Azure AD](../governance/entitlement-management-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare una configurazione sicura che include l'autenticazione avanzata, le baseline software e hardware, l'accesso logico e di rete limitato.

- [Protezione dei dispositivi come parte dell'accesso con privilegi](/security/compass/privileged-access-devices)

- [Implementazione dell'accesso con privilegi](/security/compass/privileged-access-deployment)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Indicazioni**: i clienti possono configurare la distribuzione di Azure Active Directory (Azure ad) per i privilegi minimi, assegnando gli utenti ai ruoli con le autorizzazioni minime necessarie per consentire agli utenti di completare le attività amministrative.

- [Autorizzazioni per il ruolo di amministratore in Azure AD](../roles/permissions-reference.md)

- [Assegnare ruoli amministrativi in Azure AD](../roles/manage-roles-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: scegliere il processo di approvazione per il supporto tecnico Microsoft  

**Linee guida**: Azure Active Directory non supporta l'archivio clienti. Microsoft può collaborare con i clienti tramite metodi non protetti dall'archivio per l'approvazione per accedere ai dati dei clienti.

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: considerare le linee guida seguenti per l'implementazione della protezione dei dati sensibili:

- **Isolamento:** Una directory è il limite di dati in base al quale i servizi di Azure Active Directory (Azure AD) archiviano ed elaborano i dati per un cliente. I clienti devono determinare dove si desidera che la maggior parte dei dati del cliente Azure AD risiedano impostando la proprietà Country nella propria directory.

- **Segmentazione:** Il ruolo dell'amministratore globale dispone del controllo completo di tutti i dati della directory e delle regole che regolano l'accesso e le istruzioni di elaborazione. Una directory può essere segmentata in unità amministrative e sottoposta a provisioning con utenti e gruppi che devono essere gestiti dagli amministratori di tali unità. gli amministratori globali possono delegare la responsabilità ad altri principi della propria organizzazione assegnando loro i ruoli predefiniti o i ruoli personalizzati che possono creare.

 
- **Accesso:** Le autorizzazioni possono essere applicate a un utente, un gruppo, un ruolo, un'applicazione o un dispositivo. L'assegnazione può essere permanente o temporale per ogni Privileged Identity Management configurazione. 
  
- **Crittografia:** Azure AD crittografa tutti i dati inattivi o in transito. L'offerta non consente ai clienti di crittografare i dati della directory con la propria chiave di crittografia. 

Per determinare come viene eseguito il mapping del paese selezionato alla posizione fisica della propria directory, vedere l'articolo "dove si trovano i dati".

- [Dove si trovano i dati articolo](https://www.microsoft.com/trust-center/privacy/data-location)

Poiché il cliente usa diversi strumenti Azure AD, funzionalità e applicazioni che interagiscono con la directory, usare l'articolo Azure Active Directory, in cui si trovano i dati?

- [Dove si trovano i dati nel dashboard](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Documentazione dei ruoli di Azure AD](/azure/active-directory/roles/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Linee guida**: per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

Azure AD supporta la crittografia dei dati in transito con TLS v 1.2 o versione successiva.

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure.

- [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem) 

- [Crittografia doppia per i dati di Azure in transito](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Potrebbero essere necessarie autorizzazioni aggiuntive per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../../governance/management-groups/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale Azure Active Directory (Azure ad) per limitare la capacità degli utenti di interagire con Azure ad tramite il portale di Azure configurando "blocca l'accesso" per l'App "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: usare la funzionalità di rilevamento delle minacce incorporata Azure Active Directory (Azure ad) per le risorse di Azure ad. 
 
 
 

 
Azure AD produce log attività spesso usati per il rilevamento delle minacce e la ricerca di minacce. Azure AD log di accesso forniscono un record delle attività di autenticazione e autorizzazione per utenti, servizi e app. Azure AD log di controllo tengono traccia delle modifiche apportate a un tenant Azure AD, incluse le modifiche che migliorano o diminuiscono il comportamento della sicurezza.

- [Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Connettere i dati Azure AD Identity Protection ad Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Connettere i dati Azure Active Directory ad Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log utente seguenti che possono essere visualizzati in Azure ad Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di Siem/monitoraggio per i casi d'uso più sofisticati di monitoraggio e analisi: 
- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti. 
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri. 
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 
- Utenti rischiosi: un utente rischioso è un indicatore per un account utente che potrebbe essere stato compromesso. 

I rilevamenti dei rischi di Identity Protection sono abilitati per impostazione predefinita e non richiedono alcun processo di onboarding. I dati di granularità o di rischio sono determinati dallo SKU di licenza. 

- [Report sulle attività di controllo in Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Abilitare Azure Identity Protection](../identity-protection/overview-identity-protection.md)  

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: Azure Active Directory (Azure ad) produce log attività. A differenza di alcuni servizi di Azure, Azure AD non fa distinzione tra i log delle attività e delle risorse. I log attività sono automaticamente disponibili nella sezione Azure AD della portale di Azure e possono essere esportati in monitoraggio di Azure, Hub eventi di Azure, archiviazione di Azure, SIEMs e altre posizioni.
 
- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.  
 
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.  
 
 

Azure AD fornisce anche log relativi alla sicurezza che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e analisi: 
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
- Utenti rischiosi: un utente rischioso è un indicatore per un account utente che potrebbe essere stato compromesso. 

I rilevamenti dei rischi di Identity Protection sono abilitati per impostazione predefinita e non richiedono alcun processo di onboarding. I dati di granularità o di rischio sono determinati dallo SKU di licenza. 

 
- [Report sulle attività e sulla sicurezza in Azure Active Directory](../reports-monitoring/overview-reports.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

Linee **Guida**: è consigliabile usare le linee guida seguenti quando i clienti vogliono centralizzare i log di sicurezza per semplificare la ricerca di minacce e l'analisi del comportamento di sicurezza:
 
- Centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log all'interno di Azure AD, assicurarsi di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.  
 
- Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.  
 
- Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.  
 

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.  
 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Linee guida**: assicurarsi che gli account di archiviazione o le aree di lavoro di log Analytics usati per archiviare Azure Active Directory log di accesso, i log di controllo e i log dei dati di rischio dispongano del periodo di conservazione dei log impostato in base alle normative di conformità dell'organizzazione.

In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)  

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: rilevamento e analisi: creare eventi imprevisti in base a avvisi di alta qualità 

**Linee guida**: assicurarsi di avere un processo per la creazione di avvisi di alta qualità e la misurazione della qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, a origini della community convalidate e a strumenti progettati per generare e pulire gli avvisi unendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Indicazioni**: assicurarsi che gli analisti possano eseguire query e usare origini dati diverse durante l'analisi di possibili eventi imprevisti, in modo da creare un quadro completo di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi.  Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Definire configurazioni sicure per i servizi di Azure 

**Linee guida**: le soluzioni Microsoft per la gestione delle identità e degli accessi consentono di proteggere l'accesso alle applicazioni e alle risorse in locale e nel cloud. È importante che le organizzazioni seguano le procedure di protezione consigliate per garantire che l'implementazione della gestione delle identità e dell'accesso sia sicura e più resiliente agli attacchi. 

In base alla strategia di implementazione della gestione delle identità e degli accessi, l'organizzazione deve seguire le procedure consigliate di Microsoft per proteggere l'infrastruttura di identità. 

Le organizzazioni che collaborano con partner esterni devono valutare e implementare le configurazioni di governance, sicurezza e conformità appropriate per ridurre i rischi per la sicurezza e proteggere le risorse sensibili.

- [Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure](../../security/fundamentals/identity-management-best-practices.md)

- [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)

- [Sicurezza della collaborazione esterna in Azure Active Directory e Microsoft 365](secure-external-access-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Supportare le configurazioni sicure per i servizi di Azure

**Linee guida**: Microsoft Secure Score fornisce alle organizzazioni una misura della loro postura di sicurezza e consigli che consentono di proteggere le organizzazioni dalle minacce. È consigliabile che le organizzazioni rivedano periodicamente il proprio punteggio sicuro per le azioni di miglioramento consigliate per migliorare la propria attitudine alla sicurezza delle identità. 

- [Qual è il Punteggio di sicurezza delle identità in Azure Active Directory?](identity-secure-score.md)

- [Punteggio di sicurezza Microsoft](/microsoft-365/security/mtp/microsoft-secure-score)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definire la strategia di gestione degli asset e di protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire la priorità di individuazione, valutazione, protezione e monitoraggio dei dati e dei sistemi business-critical. 
 

 
La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 
 
-   Standard di classificazione dei dati in base ai rischi aziendali
 
-   Visibilità dei rischi e dell'inventario degli asset dell'organizzazione della sicurezza 
 
-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure da usare 
 
-   Sicurezza degli asset attraverso il ciclo di vita
 
-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati dell'organizzazione
 
-   Uso delle funzionalità di protezione dei dati native di Azure e di terze parti
 
-   Requisiti di crittografia dei dati per i casi d'uso in transito e inattivi
 
-   Standard crittografici appropriati
 

 
Per altre informazioni, vedere i riferimenti seguenti: 
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Azure Security Benchmark - Gestione degli asset](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Azure Security Benchmark - Protezione dei dati](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso agli asset tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare le operazioni giornaliere dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definire la strategia di gestione del comportamento di sicurezza

**Linee guida**: misurare costantemente e attenuare i rischi per i singoli asset e per l'ambiente in cui sono ospitati. Assegnare la priorità agli asset di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e di uscita della rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Allineare i ruoli e le responsabilità dell'organizzazione

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità specificando una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per la protezione del cloud.

- [Procedura di sicurezza consigliata di Azure 1 - Utenti: informare i team sul percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura di sicurezza consigliata di Azure 2 - Utenti: informare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura di sicurezza consigliata di Azure 3 - Processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definire la strategia della sicurezza di rete

**Linee guida**: definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato alla strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Perimetro Internet e strategia di ingresso e uscita

-   Cloud ibrido e strategia di interconnettività locale

-   Artefatti di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Sicurezza di rete](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire gli approcci di identità e di accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Verifica dell'identità e dell'accesso utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Gestione delle identità](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Accesso con privilegi](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../../security/fundamentals/identity-management-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Linee guida**: definire una strategia di registrazione e di risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità offrendo agli analisti avvisi di alta qualità ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
