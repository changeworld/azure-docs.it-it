---
title: Baseline di sicurezza di Azure per le istanze del contenitore
description: La baseline di sicurezza delle istanze di contenitori fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598789"
---
# <a name="azure-security-baseline-for-container-instances"></a>Baseline di sicurezza di Azure per le istanze del contenitore

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) alle istanze del contenitore. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili alle istanze del contenitore. **I** controlli non applicabili alle istanze del contenitore o per i quali la responsabilità è di Microsoft sono stati esclusi.

Per informazioni sul mapping completo di Istanze contenitore a Azure Security Benchmark, vedere il file di mapping completo della baseline di sicurezza delle istanze del [contenitore](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** integrare i gruppi di contenitori in Istanze di Azure Container con una rete virtuale di Azure.  Le reti virtuali di Azure consentono di inserire molte delle risorse di Azure, ad esempio i gruppi di contenitori, in una rete instradabile non Internet.

Controllare l'accesso alla rete in uscita da una subnet Istanze di Azure Container tramite Firewall di Azure. 

- [Distribuire le istanze di contenitore in una rete virtuale di Azure](/azure/container-instances/container-instances-vnet)

- [Come distribuire e configurare Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione di rete per proteggere le risorse di rete in Azure. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate. 

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md) 

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: Non applicabile. Benchmark è destinato alle risorse Servizio app di Azure o di calcolo che ospitano applicazioni Web.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare la protezione DDoS Standard nelle reti virtuali per la protezione da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.  Distribuire Firewall di Azure in ogni limite di rete dell'organizzazione con Intelligence per le minacce abilitata e configurata su "Avvisa e nega" per il traffico di rete dannoso.

È possibile usare Centro sicurezza di Azure'accesso JUST-In-Time alla rete per configurare i NSG per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo limitato. Usare anche la protezione Centro sicurezza di Azure rete adattiva per consigliare le configurazioni del gruppo di sicurezza di rete che limitano le porte e gli IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

- [Come configurare la protezione DDoS](/azure/virtual-network/manage-ddos-protection)

- [Come distribuire i Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Informazioni Centro sicurezza di Azure protezione avanzata adattiva della rete](../security-center/security-center-adaptive-network-hardening.md)

- [Centro sicurezza di Azure controllo di accesso alla rete 2D (Just-In-Time)](../security-center/security-center-just-in-time.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, è possibile abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) per il gruppo di sicurezza di rete collegato alla subnet usata per proteggere il Registro Azure Container. È possibile registrare i log del flusso del gruppo di Archiviazione di Azure in un account per generare i record del flusso. Se necessario per analizzare l'attività anomarla, abilitare l'acquisizione Network Watcher pacchetti di Azure.

- [Come abilitare i log dei flussi dei NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** selezionare un'offerta dal Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload. Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall preferita in ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

- [Distribuire in una rete virtuale - Istanze di Azure Container](container-instances-vnet.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: Non applicabile. Benchmark è destinato alle applicazioni Web in esecuzione Servizio app di Azure risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

Linee **guida:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, per le risorse che devono accedere al registro contenitori, usare i tag del servizio di rete virtuale per il servizio Registro Azure Container per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio "AzureContainerRegistry" nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Consentire l'accesso in base ai tag del servizio](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** quando si usa Registro Azure Container con Istanze di Azure Container, è consigliabile definire e implementare configurazioni di sicurezza standard per le risorse di rete associate al Registro Azure Container.

Usare Criteri di Azure alias negli spazi dei nomi **Microsoft.ContainerRegistry** e **Microsoft.Network** per creare criteri personalizzati per controllare o applicare la configurazione di rete dei registri contenitori.

È possibile usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala confezionando elementi chiave dell'ambiente, ad esempio modelli Azure Resource Manager, controlli controllo degli accessi in base al ruolo di Azure e definizioni di criteri in un'unica definizione di progetto. Applicare facilmente il progetto alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** il cliente può usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli del controllo degli accessi in base al ruolo di Azure e criteri, in un'unica definizione di progetto. Applicare facilmente il progetto alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai registri contenitori. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire i log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da un'istanza di contenitore di Azure. In Monitoraggio di Azure usare un'area di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

- [Registrazione di gruppi di contenitori e istanze con Monitoraggio di Azure contenitori](container-instances-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** Monitoraggio di Azure raccoglie i log delle risorse (in precedenza denominati log di diagnostica) per gli eventi guidati dall'utente. Raccogliere e utilizzare questi dati per controllare gli eventi di autenticazione dei contenitori e fornire una traccia completa delle attività sugli elementi, ad esempio gli eventi pull e push, in modo da poter diagnosticare i problemi di sicurezza con il gruppo di contenitori.

- [Registrazione di gruppi di contenitori e istanze con Monitoraggio di Azure contenitori](container-instances-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** all'Monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i Istanze di Azure Container per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Monitoraggio di Azure'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

- [Informazioni nell'area di lavoro Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Come eseguire query personalizzate in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

- [Come creare un gruppo di contenitori abilitato per il log ed eseguire query nei log](container-instances-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

Linee **guida:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, usare l'area di lavoro Azure Log Analytics per il monitoraggio e l'avviso sulle attività anomae nei log di sicurezza e negli eventi correlati al Registro Azure Container.

- [Registro Azure Container log per la valutazione diagnostica e il controllo](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Come avvisare i dati del log di Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Indicazioni**: Non applicabile. Se si usa un registro privato basato sul cloud come registro Azure Container con Istanze di Azure Container, Registro Azure Container non elabora né produce log correlati al malware.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni**: Non applicabile. Se si usa un registro privato basato sul cloud come registro Azure Container con Istanze di Azure Container, Registro Azure Container è un endpoint e non avvia la comunicazione e il servizio non esegue query su DNS.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee** guida: Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono queryable. Usare il Azure AD di PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

Se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, per ogni registro azure container tenere traccia se l'account amministratore predefinito è abilitato o disabilitato. Disabilitare l'account quando non è in uso.

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Registro Azure Container account amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che variano a seconda del servizio. L'utente è responsabile delle applicazioni di terze parti e dei servizi del Marketplace che possono usare password predefinite.

Se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, se l'account amministratore predefinito di un Registro Azure Container è abilitato, le password complesse vengono create automaticamente e devono essere ruotate. Disabilitare l'account quando non è in uso.

- [Registro Azure Container account amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, creare procedure per abilitare l'account amministratore predefinito di un registro contenitori. Disabilitare l'account quando non è in uso.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Registro Azure Container account amministratore](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare Azure Active Directory (Azure AD) SSO invece di configurare singole credenziali autonome per ogni servizio. Usare le Centro sicurezza di Azure di gestione delle identità e degli accessi.

Se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, per l'accesso individuale al registro contenitori, usare l'accesso individuale inintegrato con Azure AD.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Accesso individuale a un registro contenitori](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato su autenticazione

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee** guida: usare paw (workstation di accesso con privilegi) con autenticazione a più fattori configurata per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare anche Verifiche di accesso alle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni Azure AD report](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** è possibile accedere Azure Active Directory (Azure AD) alle origini dei log attività, di controllo e di rischio, che consentono di eseguire l'integrazione con qualsiasi strumento di monitoraggio/Security Information and Event Management (SIEM).

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente Azure AD e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare Azure Active Directory funzionalità di rischio (Azure AD) e Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente.

- [Come visualizzare gli Azure AD rischiosi](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** non disponibile; Customer Lockbox non è attualmente supportato per Istanze di Azure Container.

- [Elenco dei Customer Lockbox supportati](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag delle risorse per tenere traccia dei registri contenitori di Azure che archiviano o elaborano informazioni riservate.

Assegnare tag alle immagini del contenitore e alla versione o ad altri artefatti in un registro e bloccare immagini o repository per facilitare il rilevamento delle immagini che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Raccomandazioni per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)

- [Bloccare un'immagine del contenitore in un Registro Azure Container](../container-registry/container-registry-image-lock.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare registri contenitori, sottoscrizioni e/o gruppi di gestione separati per sviluppo, test e produzione. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate.

Le risorse devono essere separate dalla rete virtuale o dalla subnet, contrassegnate in modo appropriato e protette da un gruppo di sicurezza di rete (NSG) o da Firewall di Azure.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Limitare l'accesso a un Registro Azure Container usando una rete virtuale di Azure o regole del firewall](../container-registry/container-registry-vnet.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare un avviso o un avviso e negarlo con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: distribuire uno strumento automatizzato nei perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti inviando avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i dati dei clienti sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** assicurarsi che tutti i client che si connettono Registro Azure Container siano in grado di negoziare TLS 1.2 o versione successiva. Microsoft Azure le risorse negoziano TLS 1.2 per impostazione predefinita.

Seguire Centro sicurezza di Azure raccomandazioni per la crittografia in stato di inquieto e la crittografia in transito, se applicabile.

- [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** se si usa un registro privato basato sul cloud come registro Azure Container con Istanze di Azure Container, le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per Registro Azure Container. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i dati dei clienti sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Crittografare i dati di distribuzione con Istanze di Azure Container](container-instances-encrypt-data.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso ai dati e alle risorse in un registro azure container.

- [Come configurare il controllo degli accessi in base al ruolo di Azure in Azure](../role-based-access-control/role-assignments-portal.md)

- [Ruoli e autorizzazioni di Registro Azure Container](../container-registry/container-registry-roles.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni:** se necessario per la conformità alle risorse di calcolo, implementare uno strumento di terze parti, ad esempio una soluzione automatizzata di prevenzione della perdita dei dati basata su host, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati da un sistema.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i dati dei clienti come sensibili e va a grandi lunghezze per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** usare la crittografia dei dati in pausa in tutte le risorse di Azure. Se si usa un registro privato basato sul cloud, ad esempio Registro Azure Container con Istanze di Azure Container, per impostazione predefinita, tutti i dati in un Registro Azure Container vengono crittografati quando sono in pausa usando chiavi gestite da Microsoft.

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

- [Chiavi gestite dal cliente in Registro Azure Container](https://aka.ms/acr/cmk)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** le aree di lavoro Log Analytics offrono una posizione centralizzata per l'archiviazione e l'esecuzione di query sui dati di log non solo dalle risorse di Azure, ma anche da risorse e risorse locali in altri cloud. Il servizio Istanze di Azure Container supporta per impostazione predefinita l'invio di log e dati sugli eventi ai log di Monitoraggio di Azure.

- [Registrazione di gruppi di contenitori e istanze con Monitoraggio di Azure contenitori](container-instances-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** sfruttare le soluzioni per analizzare le immagini dei contenitori in un registro privato e identificare le potenziali vulnerabilità. È importante comprendere la profondità del rilevamento delle minacce fornito dalle diverse soluzioni. Seguire le raccomandazioni Centro sicurezza di Azure sull'esecuzione di valutazioni della vulnerabilità nelle immagini del contenitore. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace per eseguire valutazioni della vulnerabilità delle immagini.

- [Raccomandazioni sulla sicurezza per il monitoraggio e l'analisi dei contenitori per Istanze di Azure Container](container-instances-image-security.md)

- [Registro Azure Container'integrazione con il Centro sicurezza](/azure/security-center/azure-container-registry-integration)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni:** Microsoft esegue la gestione delle patch nei sistemi sottostanti che supportano l'esecuzione di contenitori.

Automatizzare gli aggiornamenti delle immagini del contenitore quando vengono rilevati aggiornamenti alle immagini di base dal sistema operativo e da altre patch.

- [Informazioni sugli aggiornamenti delle immagini di base per Registro Azure Container attività](../container-registry/container-registry-tasks-base-images.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuire una soluzione di gestione automatica delle patch per titoli software di terze parti

**Linee** guida: è possibile usare una soluzione di terze parti per applicare patch alle immagini dell'applicazione. Inoltre, se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, è possibile eseguire attività Registro Azure Container per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini di base.

- [Informazioni sugli aggiornamenti delle immagini di base per le attività di ACR](../container-registry/container-registry-tasks-base-images.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, integrare Registro Azure Container con Centro sicurezza di Azure per abilitare l'analisi periodica delle immagini dei contenitori per le vulnerabilità. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace per eseguire analisi periodiche della vulnerabilità delle immagini.

- [Registro Azure Container'integrazione con il Centro sicurezza](../security-center/defender-for-container-registries-introduction.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container con Istanze di Azure Container, integrare Registro Azure Container (ACR) con Centro sicurezza di Azure per abilitare l'analisi periodica delle immagini dei contenitori per le vulnerabilità e per classificare i rischi. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace eseguire analisi periodiche della vulnerabilità delle immagini e classificazione dei rischi.

- [Registro Azure Container'integrazione con il Centro sicurezza](../security-center/defender-for-container-registries-introduction.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, Registro Controllo di accesso mantiene i metadati inclusi tag e manifesti per le immagini in un registro. Seguire le procedure consigliate per l'assegnazione di tag agli artefatti.

- [Informazioni su registri, repository e immagini](../container-registry/container-registry-concepts.md)

- [Raccomandazioni per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, Registro Controllo di accesso mantiene i metadati inclusi tag e manifesti per le immagini in un registro. Seguire le procedure consigliate per l'assegnazione di tag agli artefatti.

- [Informazioni su registri, repository e immagini](../container-registry/container-registry-concepts.md)

- [Raccomandazioni per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni:** è necessario creare un inventario delle risorse di Azure approvate in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, analizzare e monitorare i log di Registro Azure Container per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Monitoraggio di Azure'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

- [Registro Azure Container log per la valutazione diagnostica e il controllo](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Informazioni sull'area di lavoro Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Come eseguire query personalizzate in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee** guida: Automazione di Azure controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. È possibile implementare una soluzione personalizzata per rimuovere risorse di Azure non autorizzate. 

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: Non applicabile. Benchmark è progettato per le risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee** guida: sfruttare Criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

**Indicazioni**: Non applicabile. Benchmark è progettato per le risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo di Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni:** usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo di Azure.

- [Ad esempio, come controllare l'esecuzione di script di PowerShell in ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni:** il software necessario per le operazioni aziendali, ma che può causare rischi più elevati per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o rete virtuale e sufficientemente protetto con un Firewall di Azure o un gruppo di sicurezza di rete.

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure o Centro sicurezza di Azure per mantenere le configurazioni di sicurezza per tutte le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni:** usare Centro sicurezza di Azure raccomandazione "Correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali" per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.

- [Come monitorare le Centro sicurezza di Azure raccomandazioni](../security-center/security-center-recommendations.md)

- [Come correggere i Centro sicurezza di Azure raccomandazioni](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** usare gli effetti criteri di Azure [nega] e [distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

Se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, controllare la conformità dei registri contenitori di Azure usando Criteri di Azure:.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni Criteri di Azure effetti](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni:** non applicabile; Questo controllo è destinato alle risorse di calcolo.

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate, Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida:** non applicabile; questo controllo si applica solo alle risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: usare Criteri di Azure per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, controllare la conformità dei registri contenitori di Azure usando Criteri di Azure:.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni Criteri di Azure effetti](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: Non applicabile. Benchmark si applica alle risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Linee** guida: usare Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure. 

Applicare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, controllare la conformità dei registri contenitori di Azure usando Criteri di Azure:.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee** guida: usare Centro sicurezza di Azure per eseguire analisi di base per il sistema operativo e le impostazioni Docker per i contenitori. 

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare l'identità del servizio gestita in combinazione Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare un Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come eseguire l'autenticazione Key Vault](container-instances-managed-identity.md)

- [Come assegnare un criterio Key Vault di accesso](../key-vault/general/assign-access-policy-portal.md)

- [Come usare identità gestite con Istanze di Azure Container](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Come crittografare i dati con Istanze di Container](container-instances-encrypt-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice.

Se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, controllare la conformità dei registri contenitori di Azure usando Criteri di Azure:.

- [Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure](../container-registry/container-registry-azure-policy.md)

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Come usare identità gestite con Istanze di Azure Container](container-instances-managed-identity.md)

- [Usare un'identità gestita di Azure per eseguire l'autenticazione a un Registro Azure Container](../container-registry/container-registry-authentication-managed-identity.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usare software antimalware gestito centralmente

**Indicazioni:** usare Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali per monitorare e proteggere continuamente le risorse. Per Linux, usare una soluzione antimalware di terze parti.

- [Come configurare i Microsoft Antimalware per Servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee** guida: Microsoft Antimalware è abilitata nell'host sottostante che supporta i servizi di Azure (ad esempio, Istanze di Azure Container), ma non viene eseguita sui dati dei clienti.

Eseguire la pre-analisi di tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** se si usa un registro privato basato sul cloud come Registro Azure Container (ACR) con Istanze di Azure Container, i dati nel registro contenitori Microsoft Azure vengono sempre replicati automaticamente per garantire durabilità e disponibilità elevata. Registro Azure Container copia i dati in modo che sia protetto da eventi pianificati e non pianificati.

Facoltativamente, eseguire la replica geografica di un registro contenitori per mantenere le repliche del Registro di sistema in più aree di Azure.

- [Replica geografica nel servizio Registro Azure Container](../container-registry/container-registry-geo-replication.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** eseguire facoltativamente il backup delle immagini del contenitore importando da un registro a un altro.

Eseguire il backup delle chiavi gestite dal cliente Azure Key Vault strumenti da riga di comando o SDK di Azure.

- [Importare immagini del contenitore in un registro contenitori](../container-registry/container-registry-import-images.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Crittografia dei dati di distribuzione con istanze del contenitore](container-instances-encrypt-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee** guida: testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup Azure Key Vault strumenti da riga di comando o SDK di Azure.

- [Come ripristinare Azure Key Vault chiavi in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** è possibile abilitare le Soft-Delete in Azure Key Vault proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni:** Centro sicurezza di Azure assegna una gravità a ogni avviso per consentire di stabilire in ordine di priorità gli avvisi da ricercare per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto. 

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni:** eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
