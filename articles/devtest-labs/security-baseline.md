---
title: Baseline della sicurezza di Azure per Azure DevTest Labs
description: La linea di base di sicurezza Azure DevTest Labs fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562835"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Baseline della sicurezza di Azure per Azure DevTest Labs

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a Azure DevTest Labs. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure DevTest Labs. I **controlli** non applicabili ai Azure DevTest Labs sono stati esclusi.

Per informazioni su come Azure DevTest Labs viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Azure DevTest Labs sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: quando si distribuiscono Azure DevTest Labs risorse è necessario creare o usare una rete virtuale esistente. Assicurarsi che la rete virtuale scelta disponga di un gruppo di sicurezza di rete applicato alle subnet e ai controlli di accesso alla rete configurati in base alle porte e alle origini attendibili dell'applicazione. Quando le risorse di un Lab sono configurate con una rete virtuale, non sono indirizzabili pubblicamente ed è possibile accedervi solo dall'interno della rete virtuale. È anche possibile scegliere di creare un Lab con isolamento rete, in cui gli ambienti Lab, le risorse Lab, ad esempio gli account di archiviazione e gli insiemi di credenziali delle chiavi, saranno completamente isolati e accessibili solo tramite endpoint specifici. 

A seconda delle esigenze dell'organizzazione, usare il servizio firewall di Azure per creare, applicare e registrare in modo centralizzato i criteri di connettività di rete e di applicazione tra sottoscrizioni e reti virtuali.

- [Come configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Come creare un'istanza di DevTest Labs di rete isolata](network-isolation.md)

- [Come creare un gruppo di sicurezza di rete con le regole di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: distribuire un gruppo di sicurezza di rete nella rete in cui vengono distribuite le risorse Azure DevTest Labs. Abilitare i log di flusso del gruppo di sicurezza di rete nei gruppi di sicurezza di rete per il controllo del traffico.

È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: distribuire Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche distribuite usando modelli di Azure Resource Manager per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: la distribuzione di una rete virtuale di Azure (VNet) per un Lab migliora la sicurezza e l'isolamento per il Lab. Le subnet, i criteri di controllo degli accessi e altre funzionalità facilitano anche la limitazione dell'accesso. Quando viene distribuito in una VNet, Azure DevTest Labs non è indirizzabile pubblicamente ed è possibile accedervi solo da macchine virtuali e applicazioni all'interno di VNet.

Abilitare la protezione standard DDoS nelle reti virtuali di Azure per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso. Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato. Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

- [Come configurare una rete virtuale per Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../security-center/security-center-adaptive-network-hardening.md)

- [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: abilitare l'acquisizione di pacchetti Network Watcher nella rete virtuale Lab per analizzare le attività anomale. 

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: usare un firewall di Azure distribuito nella rete virtuale con l'Intelligence per le minacce abilitata. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. Se l'organizzazione richiede funzionalità aggiuntive oltre a quelle disponibili in Azure firewall, selezionare un'offerta appropriata da Azure Marketplace che supporta la funzionalità di ID/IP con funzionalità di ispezione del payload.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: quando si lavora con DevTest Labs Azure Resource Manager ambienti basati che includono applicazioni Web, distribuire un gateway applicazione Azure con https/TLS abilitato per i certificati attendibili.

- [Come distribuire il gateway applicazione](../application-gateway/quick-create-portal.md)

- [Come configurare il gateway applicazione per l'uso di HTTPS](../application-gateway/create-ssl-portal.md)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../application-gateway/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato per le risorse di DevTest Labs. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

- [Comprendere e usare i gruppi di sicurezza delle applicazioni](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le risorse di rete associate alla distribuzione Azure DevTest Labs per organizzarli in modo logico in una tassonomia. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure. Tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

- [Sincronizzazione dell'ora per macchine virtuali Windows in Azure](../virtual-machines/windows/time-sync.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

- [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

- [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: Azure DevTest Labs macchine virtuali (VM) vengono create e di proprietà del cliente. Quindi, è responsabilità dell'organizzazione monitorarlo. È possibile usare il Centro sicurezza di Azure per monitorare il sistema operativo di calcolo. I dati raccolti dal centro sicurezza dal sistema operativo includono il tipo di sistema operativo e la versione, il sistema operativo (registri eventi di Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. L'agente di Log Analytics raccoglie anche i file di dump di arresto anomalo del sistema.

Per altre informazioni, vedere gli articoli seguenti:

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/vm/quick-collect-azurevm.md)

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione del log per le aree di lavoro log Analytics associate alle istanze di Azure DevTest Labs in base alle normative di conformità dell'organizzazione.

- [Per ulteriori informazioni, vedere l'articolo seguente.](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività che potrebbero essere stati raccolti per Azure DevTest Labs.

Per altre informazioni, vedere gli articoli seguenti:

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare l'area di lavoro di Azure log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale nei registri di sicurezza e negli eventi correlati al Azure DevTest Labs.

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Ruoli di Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory (Azure ad) non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che variano a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.

DevTest Labs non ha il concetto di password predefinite.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Ruoli di Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: DevTest Labs usa il servizio Azure Active Directory (Azure ad) per la gestione delle identità. Considerare questi due aspetti chiave quando si concede agli utenti l'accesso a un ambiente basato su DevTest Labs:
- Gestione delle risorse: consente di accedere al portale di Azure per gestire le risorse (creare VM, creare ambienti, avviare, arrestare, riavviare, eliminare e applicare elementi e così via). La gestione delle risorse viene eseguita in Azure usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Assegnare i ruoli agli utenti e impostare le autorizzazioni a livello di risorsa e di accesso.
- Macchine virtuali (a livello di rete): nella configurazione predefinita, le VM usano un account amministratore locale. Se è disponibile un dominio (Azure Active Directory Domain Services (Azure AD DS), un dominio locale o un dominio basato sul cloud, i computer possono essere aggiunti al dominio. Gli utenti possono quindi usare le proprie identità basate su dominio usando l'elemento di aggiunta al dominio per connettersi ai computer.

- [Architettura di riferimento per DevTest Labs](./devtest-lab-reference-architecture.md#architecture)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare le località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/overview-reports.md)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini del registro eventi di attività, controllo e rischio per l'accesso Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con qualsiasi strumento di gestione di informazioni ed eventi di sicurezza (Siem)/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory (Azure ad) funzionalità di protezione delle identità e di rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso ai lab in Azure DevTest Labs.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Informazioni sui ruoli in DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di DevTest Labs e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Come creare avvisi per gli eventi del log attività di DevTest Labs](create-alerts.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, incluse le risorse di DevTest Labs, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant ed è possibile enumerare tutte le sottoscrizioni e le risorse di Azure all'interno delle sottoscrizioni.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in base a una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Configurare i tag per DevTest Labs](devtest-lab-add-tag.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate e separare i Lab laddove appropriato per organizzare e tenere traccia dei Lab e delle risorse correlate al Lab. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate rapidamente dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare un Lab con DevTest Labs](devtest-lab-create-lab.md)

- [Come creare e usare i tag](devtest-lab-add-tag.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze organizzative. In qualità di amministratore della sottoscrizione, è anche possibile usare i controlli delle applicazioni adattivi, una funzionalità del Centro sicurezza di Azure che consente di definire un set di applicazioni consentite per l'esecuzione su gruppi configurati di computer lab. Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

- [Come abilitare il controllo delle applicazioni adattivo](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questa funzione può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. In qualità di amministratore della sottoscrizione, è possibile usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle VM DevTest Labs nella sottoscrizione. Le proprietà nome software, versione, server di pubblicazione e ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, il cliente deve abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

Oltre a usare Rilevamento modifiche per il monitoraggio di applicazioni software, i controlli delle applicazioni adattivi nel centro sicurezza di Azure usano Machine Learning per analizzare le applicazioni in esecuzione nei computer e creare un elenco di Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di evitare l'utilizzo di software indesiderato nell'ambiente. È possibile configurare la modalità di controllo o l'imposizione. La modalità di controllo controlla solo l'attività nelle macchine virtuali protette. La modalità di imposizione applica le regole e garantisce che le applicazioni che non sono consentite per l'esecuzione siano bloccate. 

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Informazioni sui controlli applicazione adattivi](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. Come amministratore della sottoscrizione, è possibile usare Rilevamento modifiche per identificare tutto il software installato nelle macchine virtuali ospitate in DevTest Labs. È possibile implementare un processo personalizzato o usare la configurazione dello stato di automazione di Azure per la rimozione di software non autorizzato.

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

- [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/change-tracking/overview.md)

- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: come amministratore della sottoscrizione, è possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure ospitate in DevTest Labs.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Materiale di riferimento:

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: il controllo delle applicazioni adattivo è una soluzione end-to-end intelligente e automatizzata del Centro sicurezza di Azure, che consente di controllare quali applicazioni possono essere eseguite nei computer Azure e non Azure (Windows e Linux), ospitati in DevTest Labs. Si noti che è necessario essere un amministratore della sottoscrizione per configurare questa impostazione per le risorse di calcolo sottostanti ospitate in DevTest Labs. Implementare una soluzione di terze parti se questa impostazione non soddisfa i requisiti dell'organizzazione.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando l' **accesso ai blocchi** per l'app di **gestione Microsoft Azure** .

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle macchine virtuali ospitate in DevTest Labs. È anche possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata nelle VM di Azure sottostanti.

- [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le applicazioni ad alto rischio distribuite nell'ambiente Azure possono essere isolate mediante rete virtuale, subnet, sottoscrizioni, gruppi di gestione e così via. e sufficientemente protetti con un firewall di Azure, un Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG).

- [Configurare la rete virtuale per DevTest Labs](devtest-lab-configure-vnet.md)

- [Panoramica del Firewall di Azure](../web-application-firewall/overview.md)

- [Panoramica di Web Application Firewall](../virtual-network/network-security-groups-overview.md)

- [Panoramica della sicurezza di rete](security-baseline.md)

- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)

- [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure create come parte di DevTest Labs. È anche possibile usare le definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: usare le raccomandazioni del Centro sicurezza di Azure per gestire le configurazioni di sicurezza in tutte le risorse di calcolo sottostanti create come parte di DevTest Labs. Inoltre, è possibile usare immagini personalizzate del sistema operativo o gli artefatti di configurazione dello stato di automazione di Azure o DevTest Labs per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

- [Panoramica di State Configuration di Automazione di Azure](../automation/automation-dsc-overview.md)

- [Caricare un disco rigido virtuale e usarlo per creare nuove macchine virtuali Windows in Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure](../virtual-machines/linux/upload-vhd.md)

- [Creare e distribuire immagini personalizzate in più laboratori DevTest](image-factory-save-distribute-custom-images.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare **Nega** e Distribuisci criteri di Azure **se non esistono** regole per applicare impostazioni sicure tra le risorse di Azure create come parte di DevTest Labs. Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità sulle risorse di calcolo di Azure sottostanti create come parte di un Lab. Inoltre, è possibile usare modelli di Azure Resource Manager, immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione. È anche possibile usare la soluzione Image Factory, una soluzione di configurazione come codice che compila e distribuisce automaticamente le immagini a intervalli regolari con tutte le configurazioni desiderate.

Inoltre, le immagini di macchine virtuali di Azure Marketplace pubblicate da Microsoft vengono gestite e gestite da Microsoft.

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md)

- [Panoramica di State Configuration di Automazione di Azure](../automation/automation-dsc-overview.md)

- [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Come creare una factory di immagini in DevTest Labs](image-factory-create.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di azure, Azure Resource Manager modelli e gli script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps.

- [Esercitazione su git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Integrazione tra Azure DevTest Labs e il flusso di lavoro Azure DevOps](devtest-lab-dev-ops.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: se si usano immagini personalizzate, usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per garantire che solo gli utenti autorizzati possano accedere alle immagini. Usando una raccolta di immagini condivise, è possibile condividere le immagini con laboratori specifici che lo richiedono. Per le immagini del contenitore, archiviarle in Azure Container Registry e usare il controllo degli accessi in base al ruolo di Azure per garantire che solo gli utenti autorizzati possano accedere

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Come configurare RBAC di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Configurare una raccolta di immagini condivise per un Lab di DevTest](configure-shared-image-gallery.md)

- [Informazioni su RBAC di Azure per Container Registry](../container-registry/container-registry-roles.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure create in DevTest Labs. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche. Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: la configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi data center cloud o locale. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. È anche possibile scrivere un artefatto personalizzato che può essere installato in ogni computer lab per assicurarsi che seguano i criteri dell'organizzazione. 

- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../automation/automation-dsc-onboarding.md)

- [Creazione di elementi personalizzati per le macchine virtuali DevTest Labs](devtest-lab-artifact-author.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure create in DevTest Labs. Usare inoltre i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse di Azure.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le impostazioni del sistema operativo e Docker per i contenitori che vengono eseguiti nel Lab.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

- [Configurare l'identità gestita per distribuire ambienti Azure Resource Manager in DevTest Labs](use-managed-identities-environments.md)

- [Configurare l'identità gestita per la distribuzione di macchine virtuali in DevTest Labs](enable-managed-identities-lab-vms.md)

- [Come creare un insieme di credenziali delle chiavi](../key-vault/general/quick-create-portal.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/authentication.md)

- [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Configurare l'identità gestita per distribuire ambienti Azure Resource Manager in DevTest Labs](use-managed-identities-environments.md)

- [Configurare l'identità gestita per la distribuzione di macchine virtuali in DevTest Labs](enable-managed-identities-lab-vms.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: usare Microsoft antimalware per Azure per monitorare e difendere continuamente le risorse. Per Linux, usare la soluzione antimalware di terze parti.  Usare anche il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione. 

- [Come configurare Microsoft antimalware per Azure](../security/fundamentals/antimalware.md) 

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, app Azure servizio ospitato in un Lab), tuttavia, non viene eseguito sul contenuto. 

Pre-analizzare i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via. 

Usare il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione. 

- [Informazioni su Microsoft antimalware per Azure](../security/fundamentals/antimalware.md) 

- [Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme antimalware siano aggiornati

**Indicazioni**: durante la distribuzione, Microsoft antimalware per Azure installerà automaticamente gli aggiornamenti più recenti per la firma, la piattaforma e il motore per impostazione predefinita. Seguire le raccomandazioni nel centro sicurezza di Azure: " &amp; app di calcolo" per assicurarsi che tutti gli endpoint per le risorse di calcolo sottostanti DevTest Labs siano aggiornati con le firme più recenti. Il sistema operativo Windows può essere ulteriormente protetto con sicurezza aggiuntiva per limitare il rischio di attacchi basati su virus o malware con il servizio Microsoft Defender Advanced Threat Protection che si integra con il Centro sicurezza di Azure.

- [Come distribuire Microsoft antimalware per Azure](../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: attualmente, Azure DevTest Labs non supporta i backup e gli snapshot delle macchine virtuali. Tuttavia, è possibile abilitare e configurare backup di Azure nelle VM di Azure sottostanti ospitate in DevTest Labs. Inoltre, è possibile configurare la frequenza e il periodo di memorizzazione desiderati per i backup automatici purché si disponga dell'accesso appropriato alle risorse di calcolo sottostanti. 

- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

- [Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale](../backup/backup-azure-vms-first-look-arm.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: attualmente, Azure DevTest Labs non supporta i backup e gli snapshot delle macchine virtuali. Tuttavia, è possibile creare snapshot delle VM di Azure sottostanti ospitate in DevTest Labs o i dischi gestiti collegati a tali istanze usando PowerShell o le API REST, purché si disponga dell'accesso appropriato alle risorse di calcolo sottostanti. È anche possibile eseguire il backup di tutte le chiavi gestite dal cliente all'interno Azure Key Vault.

Abilitare backup di Azure nelle VM di Azure di destinazione e la frequenza e i periodi di conservazione desiderati. Include il backup completo dello stato del sistema. Se si usa crittografia dischi di Azure, il backup delle macchine virtuali di Azure gestisce automaticamente il backup delle chiavi gestite dal cliente.

- [Eseguire il backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)

- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

- [Come eseguire il backup di chiavi di Key Vault in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Se necessario, testare il ripristino del contenuto a una sottoscrizione o a una rete virtuale isolata. Inoltre, testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

Se si usa crittografia dischi di Azure, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco. Quando si usa la crittografia del disco, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco.

- [Eseguire il backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)

- [Come ripristinare i file dal backup delle macchine virtuali di Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Come eseguire il backup e il ripristino di una macchina virtuale crittografata](../backup/backup-azure-vms-encryption.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: quando si esegue il backup di dischi gestiti con backup di Azure, le macchine virtuali vengono crittografate a riposo con crittografia del servizio di archiviazione (SSE). Backup di Azure può anche eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure. Crittografia dischi di Azure si integra con le chiavi di crittografia di BitLocker (BEKs), che vengono protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra inoltre con Azure Key Vault chiavi di crittografia della chiave (KEKs). Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Eliminazione temporanea per le macchine virtuali](../backup/soft-delete-virtual-machines.md)

- [Panoramica dell'eliminazione temporanea di Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida**: sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto. 

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare punti deboli e Gap, quindi rivedere il piano di risposta in base alle esigenze. 

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

- [Come configurare l'esportazione continua](../security-center/continuous-export.md) 

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare l'automazione del flusso di lavoro funzionalità del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure. 

- [Come configurare l'automazione del flusso di lavoro nel centro sicurezza](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
