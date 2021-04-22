---
title: Baseline di sicurezza di Azure per Gestione firewall di Azure
description: La Gestione firewall di Azure di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7cb37de7c5f101ea5f72ff87ccdf94e5925a95d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864416"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Baseline di sicurezza di Azure per Gestione firewall di Azure

Questa baseline di sicurezza applica le linee guida di [Azure Security Benchmark versione 2.0](../security/benchmarks/overview.md) a Gestione firewall di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Gestione firewall di Azure. **I** controlli non applicabili Gestione firewall di Azure sono stati esclusi.

Per informazioni su come Gestione firewall di Azure completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Gestione firewall di Azure baseline [di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Linee** guida: Gestione firewall di Azure usa Azure Active Directory (Azure AD) come servizio di gestione delle identità e degli accessi predefinito. È consigliabile standardizzare Azure AD gestione delle identità e degli accessi dell'organizzazione in:

- Risorse cloud Microsoft, ad esempio le applicazioni portale di Azure, Archiviazione di Azure, Macchina virtuale di Azure (Linux e Windows), Azure Key Vault, PaaS e SaaS.

- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità nella procedura di sicurezza del cloud dell'organizzazione. Azure AD offre un punteggio di sicurezza delle identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare in che modo la configurazione aderisce alle procedure consigliate e per apportare miglioramenti al comportamento di sicurezza.

Azure AD supporta l'identità esterna che consente agli utenti senza account Microsoft accedere alle applicazioni e alle risorse con la propria identità esterna.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Usare i provider di identità esterne per l'applicazione](../active-directory/external-identities/identity-providers.md)

- [Che cos'è il punteggio di sicurezza delle identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Linee** guida: Gestione firewall di Azure usa Azure Active Directory per fornire la gestione delle identità e degli accessi alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Ciò consente all'accesso Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e maggior visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee** guida: Gestione firewall di Azure usa Azure Active Directory che supporta controlli di autenticazione avanzata tramite l'autenticazione a più fattori (MFA) e metodi senza password avanzata.
- Autenticazione a più fattori: abilitare l'autenticazione a più fattori di Azure AD e seguire le raccomandazioni dell'identità Centro sicurezza di Azure e di Gestione degli accessi per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti gli utenti, agli utenti selezionati o a livello di singolo utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione senza password: sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Per gli utenti con privilegi e amministratore, assicurarsi che sia usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Criteri password predefiniti di Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminare le password non valide usando la funzionalità di protezione password di Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Indicazioni:** Gestione firewall di Azure è integrato con Azure Active Directory in cui fornisce le origini dati seguenti:
- Accessi: il report sugli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con Monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Attualmente le azioni che circondano i gruppi di raccolta regole dei criteri firewall non sono supportate dal log attività. Si tratta di un problema noto che verrà risolto negli aggiornamenti futuri.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Gestione firewall di Azure problemi noti](overview.md#known-issues)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Indicazioni:** Gestione firewall di Azure supporta l'accesso condizionale Azure Active Directory (Azure AD) per un controllo di accesso più granulare in base a condizioni definite dall'utente, ad esempio gli accessi utente da determinati intervalli IP che devono usare MFA per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per casi d'uso diversi.

- [Panoramica dell'accesso condizionale di Azure](../active-directory/conditional-access/overview.md)

- [Criteri di accesso condizionale comuni](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteggere e limitare gli utenti con privilegi elevati

**Indicazioni:** Gestione firewall di Azure usa Azure Active Directory (Azure AD) per l'identità e l'accesso. I ruoli predefiniti più critici sono Azure AD amministratore globale e l'amministratore dei ruoli con privilegi come utenti assegnati a questi due ruoli possono delegare i ruoli di amministratore:
- Amministratore globale: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure AD, nonché ai servizi che usano Azure AD identità.
- Amministratore dei ruoli con privilegi: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure AD e all'interno di Azure AD Privileged Identity Management (PIM). Inoltre, questo ruolo consente la gestione di tutti gli aspetti di PIM e delle unità amministrative.

È possibile che siano disponibili altri ruoli critici che devono essere regolamentati se si usano ruoli personalizzati con determinate autorizzazioni con privilegi assegnati. È anche possibile applicare controlli simili all'account amministratore degli asset aziendali critici.

È possibile abilitare l'accesso con privilegi just-in-time (JIT) alle risorse di Azure e ad Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti ne hanno la necessità. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

- [Autorizzazioni per il ruolo di amministratore in Azure AD](../active-directory/roles/permissions-reference.md)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/roles/security-planning.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Indicazioni:** usare ruoli personalizzati di Controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai gruppi di raccolte di regole Firewall di Azure criteri. Usare una definizione di ruolo personalizzata di Azure per impedire la rimozione accidentale dei criteri di base e fornire l'accesso selettivo ai gruppi di raccolte di regole all'interno di una sottoscrizione o di un gruppo di risorse.

Assicurarsi anche di limitare l'accesso ai sistemi di gestione, identità e sicurezza con accesso amministrativo ai sistemi business critical. Gli utenti malintenzionati che comprometteno questi sistemi di gestione e sicurezza possono immediatamente sfruttarli per compromettere gli asset business critical.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Usare Firewall di Azure criteri per definire un gerarchia delle regole](rule-hierarchy.md)

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access)

- [Amministratori delle sottoscrizioni di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Linee** guida: Gestione firewall di Azure usa Azure Active Directory (Azure AD) per gestire le risorse. Esaminare regolarmente gli account utente e l'assegnazione di accesso per assicurarsi che gli account e il relativo accesso siano validi. È possibile usare le Azure AD di accesso per esaminare le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD la creazione di report può fornire log per individuare gli account non obsoleti. È anche possibile usare le Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso per semplificare il processo di revisione.

È anche possibile configurare Azure Privileged Identity Management per avvisare quando viene creato un numero eccessivo di account amministratore e per identificare gli account amministratore non obsoleti o configurati in modo non corretto.

Alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. È necessario gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management(PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Linee** guida: Gestione firewall di Azure usa Azure Active Directory per autenticare gli utenti che gestiscono il servizio. Per evitare il blocco accidentale dell'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare normali account amministrativi. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatizzare la gestione degli entitlement 

**Indicazioni:** Gestione firewall di Azure è integrato con Azure Active Directory per gestire le risorse. Usare Azure AD gestione entitlement per automatizzare i flussi di lavoro delle richieste di accesso, tra cui assegnazioni di accesso, verifiche e scadenza. È supportata anche l'approvazione doppia o in più fasi.

- [Cosa sono le Azure AD di accesso](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulla Azure AD entitlement](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente protette per eseguire attività di gestione amministrativa con le risorse Gestione firewall di Azure negli ambienti di produzione. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare una configurazione protetta, tra cui autenticazione avanzata, baseline software e hardware e accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Distribuire una workstation con accesso con privilegi](/security/compass/privileged-access-deployment)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Indicazioni:** Gestione firewall di Azure è integrato con il controllo degli accessi in base al ruolo di Azure per gestire le risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, entità servizio dei gruppi e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati ai requisiti dei ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Usare i ruoli predefiniti per assegnare le autorizzazioni e creare ruoli personalizzati solo quando necessario.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) 

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Indicazioni:** proteggere i dati sensibili come i dati di configurazione per i criteri di Firewall di Azure limitando l'accesso tramite il controllo degli accessi in base al ruolo di Azure, i controlli degli accessi in base alla rete e controlli specifici nei servizi di Azure.

Per garantire un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere in linea con la strategia di segmentazione aziendale. Questa strategia deve inoltre tenere conto della posizione dei dati e dei sistemi sensibili o business critical.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e li protegge dalla perdita e dall'esposizione. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorare il trasferimento non autorizzato di dati sensibili

**Indicazioni:** Firewall di Azure le risorse dei criteri sono accessibili solo agli utenti autenticati. I clienti dovranno assicurarsi che solo gli utenti autorizzati hanno accesso ai dati.

- [Creare ruoli personalizzati per accedere ai gruppi di raccolte regole](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Indicazioni:** per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi "fuori banda" (ad esempio, l'acquisizione del traffico) usando la crittografia per garantire che gli utenti malintenzionati non siano in grado di leggere o modificare facilmente i dati.

Gestione firewall di Azure supporta la crittografia dei dati in transito con TLS v1.2 o versione successiva.

Anche se questo è facoltativo per il traffico sulle reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) anziché un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografia deboli devono essere disabilitate.

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra data center di Azure.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem) 

- [Doppia crittografia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Potrebbero essere necessarie autorizzazioni aggiuntive per ottenere visibilità su carichi di lavoro e servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Indicazioni:** assicurarsi che i team di sicurezza accertano l'accesso a un inventario aggiornato continuamente Gestione firewall di Azure asset in Azure. Possono usare i Azure Resource Graph per eseguire query e individuare tutte Firewall di Azure risorse nelle sottoscrizioni, inclusi servizi di Azure, applicazioni e risorse di rete.

Applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Centro sicurezza di Azure gestione inventario asset](../security-center/asset-inventory.md) 

- [Per altre informazioni sull'assegnazione di tag alle risorse, vedere la guida alle decisioni relative alla denominazione delle risorse e all'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente, inclusa la possibilità di consentire o negare distribuzioni di Firewall di Azure risorse. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Linee** guida: rimuovere Gestione firewall di Azure risorse quando non sono più necessarie per ridurre al minimo la superficie di attacco. Gli utenti possono gestire le risorse Gestione firewall di Azure tramite l'interfaccia portale di Azure, l'interfaccia della riga di comando o le API REST.

- [interfaccia della riga di comando di Firewall di Azure Policy](/cli/azure/network/firewall/policy)

- [Interfaccia della riga di comando di rete di Azure](/powershell/module/az.network/?preserve-view=true&view=azps-5.1.0#networking)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee** guida: Gestione firewall di Azure è integrato con Azure Active Directory (Azure AD) per l'identità e l'autenticazione. È possibile usare l'accesso condizionale di Azure per limitare la possibilità degli utenti di interagire con Azure Resources Manager configurando "Blocca accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Abilitare il rilevamento delle minacce per le risorse di Azure

**Indicazioni:** inoltrare i log attività prodotti da o correlati ai criteri firewall al sistema SIEM, che può essere usato per configurare rilevamenti di minacce personalizzati. Assicurarsi di monitorare diversi tipi di asset di Azure per individuare potenziali minacce e anomalie. Concentrarsi su come ricevere avvisi di alta qualità per ridurre i falsi positivi che gli analisti possono ordinare. Gli avvisi possono essere generati da dati di log, agenti o altri dati.

- [Creare regole di analisi personalizzate per rilevare le minacce](../sentinel/tutorial-detect-threats-custom.md) 

- [Intelligence per le minacce informatiche con Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Log e metriche di Firewall di Azure](../firewall/firewall-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>2\. Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Indicazioni**: Azure AD fornisce i log utente seguenti che possono essere visualizzati nei report di Azure AD o integrati con Monitoraggio di Azure, Azure Sentinel o altri strumenti SIEM o di monitoraggio per casi d'uso di monitoraggio e analisi più avanzati:
- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Oltre al monitoraggio della protezione della sicurezza di base, il modulo Protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), dalle risorse dati (database SQL e archiviazione) e dai livelli di servizio di Azure. Questa funzionalità offre visibilità sulle anomalie degli account all'interno delle singole risorse.

Attualmente le azioni che circondano i gruppi di raccolta regole dei criteri firewall non sono supportate dal log attività. Si tratta di un problema noto che verrà risolto negli aggiornamenti futuri.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Indicazioni:** i log attività, disponibili automaticamente, contengono tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse dei criteri del firewall, ad eccezione delle operazioni di lettura (GET). I log attività possono essere usati per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Attualmente le azioni che circondano i gruppi di raccolta regole dei criteri firewall non sono supportate dal log attività. Si tratta di un problema noto che verrà risolto negli aggiornamenti futuri.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Informazioni sulla registrazione e i diversi tipi di log in Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Indicazioni:** centralizzare l'archiviazione e l'analisi della registrazione per abilitare la correlazione. Per ogni origine log, assicurarsi di avere assegnato un proprietario dei dati, indicazioni di accesso, posizione di archiviazione, quali strumenti vengono usati per elaborare e accedere ai dati e requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare Archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e eseguire l'onboardmento dei dati di log Azure Sentinel o un SIEM di terze parti.

Molte organizzazioni scelgono di usare Azure Sentinel per i dati "ad accesso frequente" usati di frequente e Archiviazione di Azure per i dati "a freddo" usati meno frequentemente.

- [Come raccogliere log e metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Indicazioni:** assicurarsi che per tutti gli account di archiviazione o le aree di lavoro Log Analytics usate per l'archiviazione dei log dei criteri firewall sia impostato il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

In Monitoraggio di Azure, è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare Archiviazione di Azure, Data Lake o account dell'area di lavoro Log Analytics per l'archiviazione a lungo termine e di archiviazione.

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

- [Archiviazione dei log delle risorse in un account Archiviazione di Azure locale](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Rilevamento e analisi: creare eventi imprevisti basati su avvisi di alta qualità

**Linee guida**: assicurarsi di avere un processo per la creazione di avvisi di alta qualità e la misurazione della qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, a origini della community convalidate e a strumenti progettati per generare e pulire gli avvisi unendo e correlando diverse origini dei segnali. 

Centro sicurezza di Azure fornisce avvisi di alta qualità in molti asset di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Indicazioni**: assicurarsi che gli analisti possano eseguire query e usare origini dati diverse durante l'analisi di possibili eventi imprevisti, in modo da creare un quadro completo di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi.  Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o nell'analisi usata per rilasciare l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Definire configurazioni sicure per i servizi di Azure 

**Linee** guida: automatizzare e proteggere la distribuzione e la configurazione delle risorse Gestione firewall di Azure negli ambienti, usando meccanismi come: modelli di Azure Resource Manager, controlli del controllo degli accessi in base al ruolo di Azure e Criteri di Azure. Definire configurazioni sicure per le risorse Gestione firewall di Azure in fase di distribuzione, controllare e applicare tali configurazioni definendo definizioni di Criteri di Azure personalizzate usando alias nello spazio dei nomi 'Azure.Network'.

- [Firewall di Azure riferimento al modello di criteri](/azure/templates/microsoft.network/firewallpolicies)

- [interfaccia della riga di comando di Firewall di Azure Policy](/cli/azure/network/firewall/policy)

- [Illustrazione dell'implementazione di guardrails nella zona di destinazione su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Supportare le configurazioni sicure per i servizi di Azure

**Indicazioni:** Gestione firewall di Azure supporta modelli Azure Resource Manager e l'applicazione di impostazioni di configurazione tramite Criteri di Azure. Definire definizioni Criteri di Azure personalizzate per controllare e applicare Gestione firewall di Azure delle risorse usando alias nello spazio dei nomi 'Azure.Network'.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Firewall di Azure riferimento al modello di criteri di sicurezza](/azure/templates/microsoft.network/firewallpolicies)

- [Illustrazione dell'implementazione delle protezioni nella zona di destinazione su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Stabilire configurazioni sicure per le risorse di calcolo

**Indicazioni:** non applicabile; Gestione firewall di Azure è un servizio di gestione del piano di controllo del firewall e non espone l'infrastruttura di calcolo del servizio sottostante che i clienti possono configurare.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [Azure Security Benchmark: Backup e ripristino](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantire backup automatizzati regolari

**Indicazioni:** Gestione firewall di Azure non ha il concetto di backup di sistema per i clienti, l'infrastruttura sottostante viene gestita da Microsoft.

Per i backup di configurazione delle risorse, usare Azure Resource Manager per esportare i criteri del firewall e le risorse correlate in un modello JavaScript Object Notation (JSON) che può essere usato come backup della configurazione. È anche possibile esportare le configurazioni dei criteri del firewall usando la funzionalità di esportazione dei modelli Firewall di Azure da portale di Azure. Usare Automazione di Azure eseguire script di backup personalizzati per acquisire automaticamente le configurazioni delle risorse Gestione firewall di Azure risorse.

- [Distribuire un hub virtuale sicuro usando un modello](quick-secure-virtual-hub.md)

- [Informazioni di riferimento sui modelli di criteri di Microsoft Firewall](/azure/templates/microsoft.network/firewallpolicies)

- [Informazioni su Automazione di Azure](../automation/automation-intro.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Convalidare tutti i backup che includono chiavi gestite dal cliente

**Indicazioni:** Gestione firewall di Azure non ha il concetto di backup del sistema rivolti ai clienti. Per tutti i modelli Gestione firewall di Azure risorse esportati eseguono periodicamente il ripristino usando Azure Resource Manager file modello.

- [Distribuire l'hub virtuale sicuro usando Azure Resource Manager personalizzati](quick-secure-virtual-hub.md)

- [Informazioni di riferimento sui modelli di criteri di Microsoft Firewall](/azure/templates/microsoft.network/firewallpolicies)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - Gestione degli asset](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso agli asset tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare le operazioni giornaliere dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definire la strategia di gestione del comportamento di sicurezza

**Linee guida**: misurare costantemente e attenuare i rischi per i singoli asset e per l'ambiente in cui sono ospitati. Assegnare la priorità agli asset di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e di uscita della rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Allineare i ruoli e le responsabilità dell'organizzazione

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità specificando una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per la protezione del cloud.

- [Procedura di sicurezza consigliata di Azure 1 - Utenti: informare i team sul percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura di sicurezza consigliata di Azure 2 - Utenti: informare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura di sicurezza consigliata di Azure 3 - Processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

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

- [Azure Security Benchmark - Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire gli approcci di identità e di accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Verifica dell'identità e dell'accesso utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)