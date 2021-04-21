---
title: Baseline di sicurezza di Azure per Automazione
description: La baseline di sicurezza di Automazione fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834499"
---
# <a name="azure-security-baseline-for-automation"></a>Baseline di sicurezza di Azure per Automazione

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) a Automazione di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Automazione di Azure. **I** controlli non applicabili Automazione di Azure sono stati esclusi.

 
Per informazioni su come Automazione di Azure completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Automazione di Azure [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Linee** guida: Automazione di Azure'account non supporta ancora collegamento privato di Azure per limitare l'accesso al servizio tramite endpoint privati. I runbook che eseguono l'autenticazione e l'esecuzione su risorse in Azure vengono eseguiti in una sandbox di Azure e sfruttano le risorse back-end condivise, che Microsoft è responsabile dell'isolamento reciproco. la rete è senza restrizioni e può accedere alle risorse pubbliche. Automazione di Azure non dispone attualmente dell'integrazione della rete virtuale per la rete privata oltre al supporto per i runbook worker ibridi. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i runbook worker ibridi.

Per ottenere un ulteriore isolamento per i runbook, è possibile usare i processi di lavoro ibridi per runbook in esecuzione nelle macchine virtuali di Azure. Quando si crea una macchina virtuale di Azure, è necessario creare una rete virtuale o usare una rete virtuale esistente e configurare le macchine virtuali con una subnet. Assicurarsi che a tutte le subnet distribuite sia applicato un gruppo di sicurezza di rete con controlli di accesso di rete specifici per le porte e le origini attendibili delle applicazioni. Per i requisiti specifici del servizio, fare riferimento alla raccomandazione sulla sicurezza per tale servizio specifico. In alternativa, se si ha un requisito specifico, Firewall di Azure può essere usato anche per soddisfarlo.

- [Reti virtuali e macchine virtuali in Azure](../virtual-machines/network-overview.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Ambiente di esecuzione dei runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni:** Automazione di Azure attualmente non dispone dell'integrazione della rete virtuale per la rete privata oltre al supporto per i computer di lavoro ibridi per runbook. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i computer di lavoro ibridi per runbook.

Se si usano ruoli di lavoro ibridi per runbook supportati da macchine virtuali di Azure, assicurarsi che la subnet contenente tali ruoli di lavoro sia abilitata con un gruppo di sicurezza di rete (NSG) e configurare i log dei flussi per inoltrare i log a un account di archiviazione per il controllo del traffico. È anche possibile inoltrare i log dei flussi del gruppo di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Anche se le regole del gruppo di sicurezza di rete e le route definite dall'utente non si applicano all'endpoint privato, i log dei flussi dei gruppi di sicurezza di rete e le informazioni di monitoraggio per le connessioni in uscita sono ancora supportati e possono essere usati.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** Automazione di Azure non dispone attualmente dell'integrazione della rete virtuale per la rete privata oltre al supporto per i computer di lavoro ibridi per runbook. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i computer di lavoro ibridi per runbook.

Se si usano i computer di lavoro ibridi per runbook supportati da macchine virtuali di Azure, abilitare la protezione DDoS (Distributed Denial of Service) Standard nelle reti virtuali che ospitano i computer di lavoro ibridi per runbook per proteggersi dagli attacchi DDoS. Usando l Centro sicurezza di Azure intelligence integrata sulle minacce, è possibile negare le comunicazioni con indirizzi IP dannosi noti.  Configurare Firewall di Azure in ogni segmento di rete virtuale, con Intelligence per le minacce abilitata, e configurare su Avvisa e nega per **il** traffico di rete dannoso.

È possibile usare l'Centro sicurezza di Azure just-in-time della rete per limitare l'esposizione delle macchine virtuali Windows agli indirizzi IP approvati per un periodo di tempo limitato.  Usare anche le raccomandazioni Centro sicurezza di Azure protezione avanzata della rete adattiva per le configurazioni del gruppo di sicurezza di rete per limitare le porte e gli IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Informazioni Centro sicurezza di Azure protezione avanzata della rete adattiva](../security-center/security-center-adaptive-network-hardening.md)

- [Informazioni Centro sicurezza di Azure controllo degli accessi di rete JUST-In-Time](../security-center/security-center-just-in-time.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Linee** guida: Automazione di Azure non dispone attualmente dell'integrazione della rete virtuale per la rete privata oltre al supporto per i dipendenti ibridi per runbook. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i dipendenti ibridi.

Se si usano i ruolo di lavoro ibridi per runbook supportati da macchine virtuali di Azure, è possibile registrare i log dei flussi del gruppo di sicurezza di rete in un account di archiviazione per generare record di flusso per le macchine virtuali di Azure che agiscono come ruolo di lavoro per runbook. Quando si analizzano attività anoma le, è possibile abilitare Network Watcher di pacchetti in modo che il traffico di rete possa essere esaminato per individuare attività insolite e impreviste.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee** guida: Automazione di Azure non dispone attualmente dell'integrazione della rete virtuale per la rete privata oltre al supporto per i runbook worker ibridi. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i runbook worker ibridi.

Se si usano i thread di lavoro ibridi per runbook ospitati in macchine virtuali di Azure, è possibile combinare le acquisizioni di pacchetti fornite dagli strumenti Network Watcher e IDS di open source per eseguire il rilevamento delle intrusioni di rete per un'ampia gamma di minacce a tali computer di lavoro. È anche possibile distribuire i Firewall di Azure ai segmenti di rete virtuale in base alle esigenze, con Intelligence per le minacce abilitata e configurata su "Avviso e rifiuto" per il traffico di rete dannoso.

- [Eseguire il rilevamento di intrusioni di rete con Network Watcher e strumenti open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o Firewall di Azure configurati in Azure che richiedono l'accesso alle risorse di Automazione. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio GuestAndHybridManagement) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di rete usate Automazione di Azure con Criteri di Azure.

È anche possibile usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di artefatti chiave dell'ambiente, ad esempio modelli di Azure Resources Manager, controlli del controllo degli accessi in base al ruolo di Azure e criteri, in una singola definizione di progetto. È possibile applicare il progetto alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure esempi per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di rete. Creare avvisi all'interno Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inoltrare i dati di log Monitoraggio di Azure log per aggregare i dati di sicurezza generati Automazione di Azure risorse. In Monitoraggio di Azure, usare query di log per cercare ed eseguire analisi e usare Archiviazione di Azure account per l'archiviazione a lungo termine/di archiviazione. Automazione di Azure possibile inviare lo stato del processo del runbook, i flussi di processo, i dati di configurazione dello stato di Automazione, la gestione degli aggiornamenti e i log di inventario o di rilevamento delle modifiche all'area di lavoro Log Analytics. Queste informazioni sono visibili dall'API portale di Azure, Azure PowerShell e Monitoraggio di Azure Logs, che consente di eseguire semplici indagini.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come raccogliere log e metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md)

- [Integrare DSC con Monitoraggio di Azure log](automation-dsc-diagnostics.md)

- [Aree supportate per l'area di lavoro Log Analytics collegata](how-to/region-mappings.md)

- [Eseguire query Gestione aggiornamenti log](update-management/query-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare Monitoraggio di Azure per l'accesso ai log di controllo e attività, che includono l'origine evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili. 

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 

- [Visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** all'Monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Dettagli di conservazione dei dati per gli account di Automazione](./automation-managing-data.md#data-retention)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Linee** guida: analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Monitoraggio di Azure query di log per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informazioni sulle query di log Monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare Centro sicurezza di Azure con Monitoraggio di Azure per il monitoraggio e l'avviso sulle attività anoma le trovate nei log e negli eventi di sicurezza.

In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come avvisare i dati Monitoraggio di Azure log](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee** guida: implementare una soluzione di terze parti Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze delle organizzazioni.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) predefiniti, che possono essere assegnati in modo esplicito e su cui è possibile eseguire query. Usare il Azure AD di PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi. Ogni volta che si usano account RunAs dell'account di Automazione per i runbook, assicurarsi che anche queste entità servizio siano rilevate nell'inventario perché spesso dispongono di autorizzazioni elevate. Eliminare eventuali account RunAs inutilizzati per ridurre al minimo la superficie di attacco esposta.

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** Automazione di Azure'account non ha il concetto di password predefinite.  I clienti sono responsabili delle applicazioni di terze parti e dei servizi del marketplace che possono usare password predefinite eseguite sul servizio o sui relativi runbook worker ibridi.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. Ogni volta che si usano account RunAs dell'account di Automazione per i runbook, assicurarsi che anche queste entità servizio siano rilevate nell'inventario perché spesso dispongono di autorizzazioni elevate. Impostare come ambito queste identità con le autorizzazioni con privilegi minimi necessarie per consentire ai runbook di eseguire correttamente il processo automatizzato. Eliminare eventuali account RunAs inutilizzati per ridurre al minimo la superficie di attacco esposta.

È anche possibile abilitare un accesso JUST-In-Time/Just-Enough-Access usando Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services e Azure Resource Manager.

- [Altre informazioni sulle Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare l'accesso SSO con Azure Active Directory (Azure AD) anziché configurare singole credenziali autonome per servizio. Usare Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Single Sign-On alle applicazioni in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Usare Azure AD per l'autenticazione in Azure](automation-use-azure-ad.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare le paw con autenticazione a più fattori configurate per accedere e configurare le risorse Automazione di Azure account negli ambienti di produzione. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare in caso di attività sospette da account amministrativi 

**Indicazioni:** usare i Azure Active Directory (Azure AD) per visualizzare avvisi e report sul comportamento rischioso degli utenti. Facoltativamente, il cliente può inoltrare Centro sicurezza di Azure di rilevamento dei rischi per Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Informazioni Centro sicurezza di Azure dei rischi di sicurezza (attività sospetta)](../active-directory/identity-protection/overview-identity-protection.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate 

**Indicazioni:** è consigliabile usare le località denominate per l'accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. Se si usano i thread di lavoro ibridi per runbook, è possibile sfruttare le identità gestite anziché gli account RunAs per abilitare autorizzazioni sicure più semplici.

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Usare l'autenticazione runbook con identità gestite](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. L'accesso utente può essere rivisto a intervalli regolari per assicurarsi che solo gli utenti autorizzati possano continuare ad accedere. Ogni volta che si usano account RunAs dell'account di Automazione per i runbook, assicurarsi che anche queste entità servizio siano rilevate nell'inventario perché spesso hanno autorizzazioni elevate. Eliminare eventuali account RunAs inutilizzati per ridurre al minimo la superficie di attacco esposta.

- [Informazioni Azure AD report](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** è possibile accedere alle origini Azure Active Directory (Azure AD) attività di accesso, controllo e registro eventi di rischio, che consentono di eseguire l'integrazione con qualsiasi strumento SIEM/Monitoraggio.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Linee** guida: usare Azure Active Directory funzionalità di rischio (Azure AD) e Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente per la risorsa di rete. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** per gli Automazione di Azure, il supporto Tecnico Microsoft può accedere ai metadati delle risorse della piattaforma durante un caso di supporto aperto senza l'uso di un altro strumento.

Tuttavia, quando si usano i runbook worker ibridi supportati da macchine virtuali di Azure e una terza parte deve accedere ai dati dei clienti (ad esempio durante una richiesta di supporto), usare Customer Lockbox (anteprima) per le macchine virtuali di Azure per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Linee** guida: usare i tag per tenere traccia Automazione di Azure risorse che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Isolare gli ambienti usando risorse account di Automazione separate. Le risorse come i ruoli di lavoro ibridi per runbook devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette all'interno di un gruppo di sicurezza di rete (NSG) o Firewall di Azure. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare criteri e procedure per disattivarli quando non sono in uso.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare avvisi o avvisi e rifiutare con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** quando si usa la funzionalità ruolo di lavoro ibrido per runbook, sfruttare una soluzione di terze parti da Azure Marketplace su perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti inviando avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure nelle reti virtuali di Azure siano in grado di negoziare TLS 1.2 o versione successiva. Automazione di Azure supporta e applica completamente transport layer (TLS) 1.2 e tutte le chiamate client o versioni successive per tutti gli endpoint HTPS esterni (tramite webhook, nodi DSC, ruolo di lavoro ibrido per runbook).

Seguire Centro sicurezza di Azure per la crittografia dei dati in pausa e la crittografia in transito, se applicabile.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Automazione di Azure'imposizione di TLS 1.2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** usare uno strumento di individuazione attivo di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, incluse quelle presenti in sede o presso un provider di servizi remoto e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse di Automazione di Azure usando le definizioni di ruolo incorporate, assegnare l'accesso agli utenti che accedono alle risorse di automazione seguendo un modello di accesso con privilegi minimi o sufficientemente "just-enough". Quando si usano ruoli di lavoro ibridi per runbook, sfruttare le identità gestite per tali macchine virtuali per evitare l'uso di entità servizio, quando si usano entrambi i ruoli di lavoro multi-tenant o ibrido per runbook assicurarsi di applicare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure con ambito corretto sull'identità dei ruoli di lavoro per runbook.

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

- [Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Gestire le autorizzazioni e la sicurezza dei ruoli](automation-role-based-access-control.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee** guida: Automazione di Azure non espone attualmente le macchine virtuali del ruolo di lavoro multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio out-of-the-box senza i runbook worker ibridi.

Se si usano i processi di lavoro ibridi per runbook supportati da macchine virtuali di Azure, è necessario usare una soluzione di prevenzione della perdita dei dati basata su host di terze parti per applicare i controlli di accesso alle macchine virtuali del ruolo di lavoro ibrido per runbook ospitate.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** usare chiavi gestite dal cliente con Automazione di Azure. Automazione di Azure supporta l'uso di chiavi gestite dal cliente per crittografare tutti gli "asset sicuri" usati, ad esempio credenziali, certificati, connessioni e variabili crittografate. Sfruttare le variabili crittografate con i runbook per tutte le esigenze di ricerca delle variabili persistenti per evitare un'esposizione non intenzionale.

Quando si usano ruoli di lavoro ibridi per runbook, i dischi virtuali nelle macchine virtuali vengono crittografati in stato di inquieto usando la crittografia lato server o Crittografia dischi di Azure. Crittografia dischi di Azure sfrutta la funzionalità BitLocker di Windows per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

- [Crittografia lato server dei dischi gestiti di Azure](../virtual-machines/disk-encryption.md)

- [Crittografia dischi di Azure per macchine virtuali Windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Variabili gestite in Automazione di Azure](shared-resources/variables.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle risorse critiche di Azure, ad esempio i componenti di rete, gli account Automazione di Azure e i runbook. 

- [Registrazione diagnostica per un gruppo di sicurezza di rete](../private-link/private-link-overview.md#logging-and-monitoring)

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** seguire le raccomandazioni Centro sicurezza di Azure sull'esecuzione di valutazioni della vulnerabilità nelle risorse di Azure

- [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-recommendations.md)

- [Informazioni di riferimento per le raccomandazioni del Centro sicurezza](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni:** esportare i risultati dell'analisi a intervalli coerenti e confrontarli per verificare che le vulnerabilità siano state corretti. Quando si usa la raccomandazione di gestione delle vulnerabilità suggerita da Centro sicurezza di Azure, il cliente può eseguire il pivot nel portale della soluzione selezionata per visualizzare i dati di analisi cronologici.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni di rischio predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

- [Informazioni Centro sicurezza di Azure Secure Score](../security-center/secure-score-security-controls.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare le Azure Resource Graph per eseguire query e individuare tutte Automazione di Azure risorse all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia Automazione di Azure risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. Eliminare eventuali account RunAs inutilizzati per ridurre al minimo la superficie di attacco esposta.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni:** è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti: 

- Tipi di risorse non consentiti 
- Tipi di risorse consentiti 

È anche possibile usare il Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Ciò può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Criteri di Azure predefiniti di esempio per Automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni:** il cliente può impedire la creazione o l'utilizzo di risorse Criteri di Azure in base alle linee guida aziendali del cliente. È possibile implementare un processo personalizzato per rimuovere risorse non autorizzate. Nell'Automazione di Azure è possibile installare, rimuovere e gestire i moduli di PowerShell o Python a cui i runbook possono accedere tramite il portale o i cmdlet. Il modulo non approvato o precedente deve essere rimosso o aggiornato per i runbook.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Gestire il modulo in Automazione di Azure](shared-resources/modules.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare i criteri di accesso condizionale di Azure per limitare la possibilità degli utenti di interagire con Azure Resource Manager configurando "Blocca l'accesso" per l'app "gestione Microsoft Azure" da posizioni o dispositivi non protetti o non approvati. 

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias personalizzati per creare criteri personalizzati per controllare o applicare la configurazione del Automazione di Azure e delle risorse correlate. È anche possibile usare definizioni Criteri di Azure predefiniti.

Inoltre, Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare raccomandazioni di Centro sicurezza di Azure come baseline di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure di esempio predefiniti per Automazione di Azure](policy-reference.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee** guida: usare Azure Resource Manager modelli e Criteri di Azure per configurare in modo sicuro le risorse di Azure associate Automazione di Azure. Azure Resource Manager modelli sono file basati su JSON usati per distribuire le risorse di Azure e tutti i modelli personalizzati dovranno essere archiviati e gestiti in modo sicuro in un repository di codice. Usare la funzionalità di integrazione del controllo del codice sorgente per mantenere aggiornati i runbook nell'account di Automazione con gli script nel repository del controllo del codice sorgente. Usare Criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

- [Informazioni sulla creazione Azure Resource Manager modelli](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Distribuire un account di Automazione usando un modello Azure Resource Manager di automazione](/azure/automation/quickstart-create-automation-account-template)

- [Criteri di Azure di esempio predefiniti per Automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati, modelli Azure Resource Manager e script Desired State Configuration personalizzati. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrato con Azure DevOps o Active Directory se integrato con TFS. Usare la funzionalità di integrazione del controllo del codice sorgente per mantenere aggiornati i runbook nell'account di Automazione con gli script nel repository del controllo del codice sorgente.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando Criteri di Azure. Usare Criteri di Azure alias per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare definizioni di criteri incorporate correlate alle risorse specifiche. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Criteri di Azure predefiniti di esempio per Automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure avvisi e controllare le configurazioni delle risorse di Azure. I criteri possono essere usati per rilevare determinate risorse non configurate con un endpoint privato.

Quando si usa la funzionalità ruolo di lavoro ibrido per runbook, sfruttare Centro sicurezza di Azure per eseguire analisi di base per le macchine virtuali di Azure.  Altri metodi per la configurazione automatizzata includono: Automazione di Azure State Configuration.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure predefiniti di esempio per Automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** usare Azure Resource Manager per distribuire Automazione di Azure account e risorse correlate. Azure Resource Manager consente di esportare modelli che possono essere usati come backup per ripristinare Automazione di Azure account e risorse correlate. Usare Automazione di Azure per chiamare l'API Azure Resource Manager di esportazione del modello a intervalli regolari.

Usare la funzionalità di integrazione del controllo del codice sorgente per mantenere aggiornati i runbook nell'account di Automazione con gli script nel repository del controllo del codice sorgente.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager riferimento al modello per Automazione di Azure risorse](/azure/templates/microsoft.automation/allversions)

- [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse - Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione alla Automazione di Azure](automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** usare Azure Resource Manager per distribuire Automazione di Azure account e risorse correlate. Azure Resource Manager consente di esportare modelli che possono essere usati come backup per ripristinare Automazione di Azure account e risorse correlate. Usare Automazione di Azure per chiamare l'API Azure Resource Manager di esportazione del modello a intervalli regolari. Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault. È possibile esportare i runbook in file script usando portale di Azure o PowerShell.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager riferimento al modello per Automazione di Azure risorse](/azure/templates/microsoft.automation/allversions)

- [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse - Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione al Automazione di Azure](automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Backup dei dati di Azure per gli account di Automazione](./automation-managing-data.md#data-backup)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** assicurarsi di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari in una sottoscrizione isolata, se necessario. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Distribuire le risorse con modelli arm e portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio Azure Resource Manager modelli. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrato con Azure DevOps o Active Directory se integrato con TFS.

Usare la funzionalità di integrazione del controllo del codice sorgente per mantenere aggiornati i runbook nell'account di Automazione con gli script nel repository del controllo del codice sorgente.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Il cliente può anche sfruttare la Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o nell'analisi usata per l'emissione dell'avviso, nonché sul livello di attendibilità che si è verificato un intento dannoso dietro l'attività che ha generato l'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni:** eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee** guida: usare la funzionalità Automazione flusso di lavoro Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" sugli avvisi di sicurezza e sulle raccomandazioni per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza 

**Linee** guida: seguire le regole microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
