---
title: Baseline di sicurezza di Azure per i set di scalabilità di macchine virtuali
description: La baseline di sicurezza dei set di scalabilità di macchine virtuali fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867819"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Baseline di sicurezza di Azure per i set di scalabilità di macchine virtuali

Questa baseline di sicurezza applica le linee guida di [Azure Security Benchmark versione 1.0 ai](../security/benchmarks/overview-v1.md) set di scalabilità di macchine virtuali. Azure Security Benchmark offre raccomandazioni su come proteggere le soluzioni cloud in  Azure. Il contenuto è raggruppato in base ai controlli di sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili ai set di scalabilità di macchine virtuali.

> [!NOTE]
> **I** controlli non applicabili ai set di scalabilità di macchine virtuali o per i quali la responsabilità è di Microsoft sono stati esclusi. Per informazioni sul mapping completo dei set di scalabilità di macchine virtuali ad Azure Security Benchmark, vedere il file di mapping completo della baseline di sicurezza dei set di **[scalabilità di macchine virtuali.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** quando si crea una macchina virtuale (VM) di Azure, è necessario creare una rete virtuale o usare una rete virtuale esistente e configurare la macchina virtuale con una subnet. Assicurarsi che a tutte le subnet distribuite sia applicato un gruppo di sicurezza di rete con controlli di accesso alla rete specifici per le porte e le origini attendibili delle applicazioni.

In alternativa, se si dispone di un caso d'uso specifico per un firewall centralizzato, Firewall di Azure può essere usato anche per soddisfare tali requisiti.

- [Rete per i set di scalabilità di macchine virtuali di Azure](virtual-machine-scale-sets-networking.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare il Centro sicurezza di Azure per identificare e seguire le raccomandazioni sulla protezione della rete per proteggere le risorse della macchina virtuale (VM) di Azure in Azure. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico per le macchine virtuali per attività insolite.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni:** se si usa il set di scalabilità di macchine virtuali per ospitare applicazioni Web, usare un gruppo di sicurezza di rete (NSG) nella subnet del set di scalabilità di macchine virtuali per limitare il traffico di rete, le porte e i protocolli autorizzati a comunicare. Quando si configurano i NSG per consentire solo il traffico richiesto all'applicazione, seguire un approccio di rete con privilegi minimi.

È anche possibile distribuire Web application firewall di Azure (WAF) davanti alle applicazioni Web critiche per un'ispezione aggiuntiva del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

- [Rete per i set di scalabilità di macchine virtuali di Azure](virtual-machine-scale-sets-networking.md)

- [Creare un gateway applicazione con un web application firewall tramite il portale di Azure](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP noti dannosi

**Linee** guida: abilitare la protezione DDoS (Distributed Denial of Service) Standard nelle reti virtuali per la protezione dagli attacchi DDoS. Usando Centro sicurezza di Azure Integrated Threat Intelligence, è possibile monitorare le comunicazioni con indirizzi IP dannosi noti.  Configurare Firewall di Azure in ogni segmento di rete virtuale, con Intelligence per le minacce abilitata e configurata su "Avviso e rifiuto" per il traffico di rete dannoso.

È possibile usare l'Centro sicurezza di Azure just-in-time della rete per limitare l'esposizione dei macchine virtuali Windows agli indirizzi IP approvati per un periodo limitato.  Usare anche la protezione Centro sicurezza di Azure adattiva per consigliare le configurazioni dei NSG che limitano le porte e gli IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Informazioni Centro sicurezza di Azure protezione avanzata adattiva della rete](../security-center/security-center-adaptive-network-hardening.md)

- [Informazioni Centro sicurezza di Azure controllo di accesso alla rete 2D (Just-In-Time)](../security-center/security-center-just-in-time.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** è possibile registrare i log dei flussi dei gruppi di sicurezza di rete in un account di archiviazione per generare record di flusso per le macchine virtuali di Azure. Quando si analizzano attività anoma le, è possibile abilitare Network Watcher di pacchetti in modo che il traffico di rete possa essere esaminato per individuare attività insolite e impreviste.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** combinando le acquisizioni di pacchetti fornite da Network Watcher e uno strumento IDS open source, è possibile eseguire il rilevamento delle intrusioni di rete per un'ampia gamma di minacce. È anche possibile distribuire i Firewall di Azure nei segmenti di rete virtuale in base alle esigenze, con Intelligence per le minacce abilitata e configurata su "Avvisa e nega" per il traffico di rete dannoso.

- [Eseguire il rilevamento delle intrusioni di rete con Network Watcher e strumenti open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni:** se si usa il set di scalabilità di macchine virtuali per ospitare applicazioni Web, è possibile distribuire gateway applicazione di Azure per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili. Con gateway applicazione di Azure, il traffico Web dell'applicazione viene diretto a risorse specifiche assegnando listener alle porte, creando regole e aggiungendo risorse a un pool back-end come il set di scalabilità di macchine virtuali e così via.

- [Come distribuire il gateway applicazione](../application-gateway/quick-create-portal.md)

- [Come configurare il gateway applicazione per l'uso di HTTPS](../application-gateway/create-ssl-portal.md)

- [Creare un set di scalabilità che fa riferimento a un gateway applicazione](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../application-gateway/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o Firewall di Azure configurati per le macchine virtuali di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee** guida: definire e implementare configurazioni di sicurezza standard per i set di scalabilità di macchine virtuali di Azure usando Criteri di Azure. È anche possibile usare Azure Blueprints per semplificare le distribuzioni di macchine virtuali di Azure su larga scala con la creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli Azure Resource Manager, assegnazioni di ruolo e assegnazioni di Criteri di Azure, in una singola definizione di progetto. È possibile applicare il progetto alle sottoscrizioni e abilitare la gestione delle risorse tramite il controllo delle versioni del progetto.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sui modelli di set di scalabilità di macchine virtuali](virtual-machine-scale-sets-mvss-start.md) 

- [Criteri di Azure esempi per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee** guida: è possibile usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso di traffico configurati per le macchine virtuali Windows. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata per tutte le regole che consentono il traffico da e verso una rete.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le modifiche alle configurazioni delle risorse di rete correlate al set di scalabilità di macchine virtuali di Azure. Creare avvisi all'interno Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni di rete o alle risorse critiche.

Usare Criteri di Azure per convalidare (e/o correggere) le configurazioni per la risorsa di rete correlata al set di scalabilità di macchine virtuali.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure esempi per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni:** Microsoft gestisce le origini ora per le risorse di Azure, ma è possibile gestire le impostazioni di sincronizzazione dell'ora per le macchine virtuali.

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo windows di Azure](../virtual-machines/windows/time-sync.md)

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo Linux di Azure](../virtual-machines/linux/time-sync.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** i log attività possono essere usati per controllare le operazioni e le azioni eseguite sulle risorse del set di scalabilità di macchine virtuali. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse, ad eccezione delle operazioni di lettura (GET). I log attività possono essere usati per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

È possibile abilitare e eseguire l'on board dei dati di log generati dai log attività di Azure o dalle risorse delle macchine virtuali in Azure Sentinel o in un sistema SIEM di terze parti per la gestione centralizzata dei log di sicurezza.

Usare Centro sicurezza di Azure per fornire il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Dato il volume di dati generato dal registro eventi di sicurezza, non viene archiviato per impostazione predefinita. 

Se l'organizzazione desidera conservare i dati del log eventi di sicurezza dalla macchina virtuale, può essere archiviata all'interno di un'area di lavoro Log Analytics nel livello di raccolta dati desiderato configurato all'interno di Centro sicurezza di Azure.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure ](../azure-monitor/essentials/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Come monitorare macchine virtuali in Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** i log attività possono essere usati per controllare le operazioni e le azioni eseguite sulle risorse del set di scalabilità di macchine virtuali. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse, ad eccezione delle operazioni di lettura (GET). I log attività possono essere usati per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Abilitare la raccolta di dati di diagnostica del sistema operativo guest distribuendo l'estensione di diagnostica nelle macchine virtuali. È possibile usare l'estensione di diagnostica per raccogliere dati di diagnostica come i log delle applicazioni o i contatori delle prestazioni da una macchina virtuale di Azure.

Per una visibilità avanzata delle applicazioni e dei servizi supportati dal set di scalabilità di macchine virtuali di Azure, è possibile abilitare sia Monitoraggio di Azure per le macchine virtuali che Application Insights. Con Application Insights, è possibile monitorare l'applicazione e acquisire dati di telemetria, ad esempio richieste HTTP, eccezioni e così via, in modo da correlare i problemi tra le macchine virtuali e l'applicazione.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come monitorare macchine virtuali in Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Panoramica di Application Insights](../azure-monitor/app/app-insights-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni:** usare Centro sicurezza di Azure per fornire il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Dato il volume di dati generato dal registro eventi di sicurezza, non viene archiviato per impostazione predefinita. 

Se l'organizzazione vuole conservare i dati del registro eventi di sicurezza dalla macchina virtuale, è possibile archiviare i dati all'interno di un'area di lavoro Log Analytics al livello di raccolta dati desiderato configurato all'interno di Centro sicurezza di Azure.

- [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Come monitorare macchine virtuali in Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** assicurarsi che per gli account di archiviazione o le aree di lavoro Log Analytics usati per archiviare i log delle macchine virtuali sia impostato il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

- [Come monitorare macchine virtuali in Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'on board dei dati Azure Sentinel o un siEM di terze parti per monitorare ed esaminare i log.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informazioni sull'area di lavoro Log Analytics](/azure/azure-monitor/logs/get-started-queries)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare Centro sicurezza di Azure configurata con un'area di lavoro Log Analytics per il monitoraggio e l'avviso sulle attività anomae presenti nei log di sicurezza e negli eventi per le macchine virtuali di Azure.  

In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel o un SIEM di terze parti per configurare gli avvisi per le attività anoma le.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come avvisare i dati del log di Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni:** è possibile usare Microsoft Anti-malware per Servizi cloud di Azure e macchine virtuali e configurare le macchine virtuali Windows per registrare gli eventi in un account Archiviazione di Azure locale. Configurare un'area di lavoro Log Analytics per inserire gli eventi dagli account di archiviazione e creare avvisi, se necessario. Seguire le raccomandazioni Centro sicurezza di Azure: "App &amp; di calcolo".  Per le macchine virtuali Linux, è necessario uno strumento di terze parti per il rilevamento delle vulnerabilità antimalware. 

- [Come configurare Microsoft Anti-malware per Servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md)

- [Come abilitare il monitoraggio a livello di guest per le macchine virtuali](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Istruzioni per l'onboarding di server Linux nel Centro sicurezza di Azure](../security-center/quickstart-onboard-machines.md) 

- [Il collegamento seguente fornisce le linee guida per la sicurezza consigliate da Microsoft, che possono fungere da elenco di criteri per il software di vulnerabilità selezionato](../virtual-machines/security-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni:** implementare una soluzione di terze parti Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni:** il Centro sicurezza di Azure il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Il Centro sicurezza effettua il Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove create se è abilitato il provisioning automatico OPPURE è possibile installare l'agente manualmente.  L'agente abilita l'evento di creazione di processi 4688 e il campo CommandLine all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza.

Per le macchine virtuali Linux, è possibile configurare manualmente la registrazione della console per ogni nodo e usare syslog per archiviare i dati.  Usare anche l Monitoraggio di Azure'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati syslog dalle macchine virtuali di Azure.

- [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

- [Origini dati Syslog in Monitoraggio di Azure](../azure-monitor/agents/data-sources-syslog.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** anche se Azure Active Directory (Azure AD) è il metodo consigliato per l'amministrazione dell'accesso utente, le macchine virtuali di Azure possono avere account locali. Gli account locali e di dominio devono essere esaminati e gestiti, in genere con un footprint minimo. Inoltre, sfruttare azure Privileged Identity Management per gli account amministrativi usati per accedere alle risorse delle macchine virtuali.

- [Informazioni per gli account locali](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Informazioni su Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** il set di scalabilità di macchine virtuali di Azure Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Cliente responsabile di applicazioni di terze parti e servizi del marketplace che possono usare password predefinite.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso alle macchine virtuali. Usare Centro sicurezza di Azure gestione delle identità e degli accessi per monitorare il numero di account amministrativi. Qualsiasi account amministratore usato per accedere alle risorse della macchina virtuale di Azure può essere gestito anche da Azure Privileged Identity Management (PIM). Azure Privileged Identity Management offre diverse opzioni, ad esempio l'elevazione just-in-time, che richiedono l'autenticazione a più fattori prima di assumere un ruolo e le opzioni di delega in modo che le autorizzazioni siano disponibili solo per determinati intervallo di tempo e richiedano un responsabile approvazione.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Informazioni su Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare l'accesso SSO con Azure Active Directory (Azure AD) anziché configurare singole credenziali autonome per servizio. Usare Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Single Sign-On alle applicazioni in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usare workstation protette gestite da Azure per attività amministrative

**Linee** guida: usare LEW (workstation di accesso con privilegi) con l'autenticazione a più fattori configurata per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare in caso di attività sospette da account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare i rilevamenti dei rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso. Facoltativamente, il cliente può inserire Centro sicurezza di Azure avvisi di rilevamento dei rischi Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni Centro sicurezza di Azure dei rischi di sicurezza (attività sospetta)](../active-directory/identity-protection/overview-identity-protection.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** usare Azure Active Directory di accesso condizionale (Azure AD) e località denominate per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.  È possibile usare le identità gestite per eseguire l'autenticazione a qualsiasi servizio che supporti l Azure AD Key Vault, senza credenziali nel codice. Il codice in esecuzione in una macchina virtuale può usare l'identità gestita per richiedere token di accesso per i servizi che supportano l Azure AD autenticazione.

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Panoramica delle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. È anche possibile usare le Azure AD di accesso alle identità per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere rivisto a intervalli regolari per assicurarsi che solo gli utenti con diritti di accesso continuino. Quando si usano macchine virtuali di Azure, è necessario esaminare i gruppi di sicurezza e gli utenti locali per assicurarsi che non siano presenti account imprevisti che potrebbero compromettere il sistema.

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Linee** guida: configurare le impostazioni di diagnostica per Azure Active Directory (Azure AD) per inviare i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Usare anche Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log dalle macchine virtuali di Azure.

- [Informazioni sull'area di lavoro Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

- [Come monitorare macchine virtuali in Azure](/azure/azure-monitor/vm/monitor-vm-azure)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Linee** guida: usare Azure Active Directory funzionalità Di rischio e Identity Protection di Azure Active Directory (Azure AD) per configurare risposte automatiche alle azioni sospette rilevate correlate alle risorse dell'account di archiviazione. È consigliabile abilitare le risposte automatizzate tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti (ad esempio durante una richiesta di supporto), usare Customer Lockbox per le macchine virtuali di Azure per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag per tenere traccia delle macchine virtuali di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette all'interno di un gruppo di sicurezza di rete (NSG) o da un Firewall di Azure. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare criteri e procedure per disattivarli quando non sono in uso.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare un avviso o un avviso e negarlo con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: implementare soluzioni di terze parti nei perimetri di rete che monitorano il trasferimento non autorizzato di informazioni riservate e bloccano tali trasferimenti inviando avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti sensibili per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** i dati in transito da, verso e tra macchine virtuali (VM) che eseguono Windows vengono crittografati in diversi modi, a seconda della natura della connessione, ad esempio quando ci si connette a una macchina virtuale in una sessione RDP o SSH.

Microsoft usa il protocollo Transport Layer Security (TLS) per proteggere i dati durante il viaggio tra i servizi cloud e i clienti.

- [Crittografia dei dati in transito nelle VM](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** usare uno strumento di individuazione attivo di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, incluse quelle presenti in sede o presso un provider di servizi remoto e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Indicazioni:** usando il controllo degli accessi in base al ruolo di Azure, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso alla macchina virtuale (VM) necessaria per svolgere il proprio lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la macchina virtuale, è possibile consentire solo determinate azioni. È possibile configurare il controllo di accesso per la macchina virtuale nel portale di Azure, usando l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Ruoli predefiniti di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni:** implementare uno strumento di terze parti, ad esempio una soluzione automatizzata di prevenzione della perdita dei dati basata su host, per applicare i controlli di accesso per ridurre il rischio di violazioni dei dati.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** i dischi virtuali nelle macchine virtuali (VM) vengono crittografati quando sono in pausa usando la crittografia lato server o Crittografia dischi di Azure. Crittografia dischi di Azure sfrutta la funzionalità DM-Crypt di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

- [Crittografia dischi di Azure set di scalabilità di macchine virtuali](disk-encryption-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee** guida: usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche ai set di scalabilità di macchine virtuali e alle risorse correlate.  

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Registrazione di Analisi archiviazione di Azure](../storage/common/storage-analytics-logging.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee** guida: seguire le raccomandazioni Centro sicurezza di Azure sull'esecuzione di valutazioni della vulnerabilità nelle macchine virtuali di Azure.  Usare la soluzione di sicurezza di Azure consigliata o di terze parti per eseguire valutazioni delle vulnerabilità per le macchine virtuali.

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni:** abilitare gli aggiornamenti automatici del sistema operativo per le versioni del sistema operativo supportate o per le immagini personalizzate archiviate in una raccolta di immagini condivise.

- [Aggiornamenti automatici del sistema operativo per i set di scalabilità di macchine virtuali in Azure](virtual-machine-scale-sets-automatic-upgrade.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuire una soluzione automatizzata di gestione delle patch per i titoli software di terze parti

**Indicazioni:** i set di scalabilità di macchine virtuali di Azure possono usare gli aggiornamenti automatici delle immagini del sistema operativo. È possibile usare l'estensione Desired State Configuration (DSC) di Azure per le macchine virtuali sottostanti nel set di scalabilità di macchine virtuali. DSC viene usato per configurare le macchine virtuali non appena vengono online in modo da eseguire il software desiderato.

- [Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure](virtual-machine-scale-sets-dsc.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni:** esportare i risultati dell'analisi a intervalli coerenti e confrontarli per verificare che le vulnerabilità siano state corretti. Quando si usa la raccomandazione di gestione delle vulnerabilità suggerita da Centro sicurezza di Azure, il cliente può eseguire il pivot nel portale della soluzione selezionata per visualizzare i dati di analisi cronologici.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni di rischio predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure.

- [Informazioni Centro sicurezza di Azure Secure Score](../security-center/secure-score-security-controls.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Linee** guida: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le macchine virtuali) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee** guida: applicare tag alle risorse di Azure che danno metadati per organizzarli in modo logico in base a una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee** guida: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se necessario, per organizzare e tenere traccia dei set di scalabilità di macchine virtuali e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Linee** guida: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare la presenza di risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Ciò può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee** guida: Automazione di Azure controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse.  Sfruttare Inventario macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Nota: nome software, versione, server di pubblicazione e ora di aggiornamento sono disponibili nel portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, il cliente deve abilitare la diagnostica a livello di guest e portare i log eventi di Windows in un'area di lavoro Log Analytics.

Attualmente i controlli delle applicazioni adattive non sono disponibili per i set di scalabilità di macchine virtuali.

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee** guida: Automazione di Azure controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse.  È possibile usare Rilevamento modifiche per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato o usare Automazione di Azure State Configuration per rimuovere software non autorizzato.

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

- [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/change-tracking/overview.md)

- [Automazione di Azure State Configuration panoramica](../automation/automation-dsc-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni:** attualmente i controlli applicazioni adattivi non sono disponibili per i set di scalabilità di macchine virtuali. Usare software di terze parti per controllare l'utilizzo solo per le applicazioni approvate.

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

**Indicazioni:** attualmente i controlli applicazioni adattivi non sono disponibili per i set di scalabilità di macchine virtuali. Implementare una soluzione di terze parti se non soddisfa i requisiti dell'organizzazione.

- [Come usare i controlli Centro sicurezza di Azure applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni:** a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la possibilità degli utenti di eseguire script all'interno delle risorse di calcolo di Azure.

- [Come controllare l'esecuzione di script di PowerShell in ambienti Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee** guida: le applicazioni ad alto rischio distribuite nell'ambiente azure possono essere isolate tramite rete virtuale, subnet, sottoscrizioni, gruppi di gestione e così via e sufficientemente protette con un Firewall di Azure, Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG). 

- [Reti virtuali e macchine virtuali in Azure](/azure/virtual-machines/windows/network-overview)

- [Panoramica del Firewall di Azure](../firewall/overview.md)

- [Panoramica di Web Application Firewall](../web-application-firewall/overview.md)

- [Panoramica della sicurezza di rete](../virtual-network/network-security-groups-overview.md)

- [Panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)

- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)

- [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: usare Criteri di Azure o Centro sicurezza di Azure per mantenere le configurazioni di sicurezza per tutte le risorse di Azure. Inoltre, Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza per l'azienda.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni su come scaricare il modello di macchina virtuale](/previous-versions/azure/virtual-machines/windows/download-template)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee** guida: Centro sicurezza di Azure raccomandazione Correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.

- [Come monitorare le Centro sicurezza di Azure raccomandazioni](../security-center/security-center-recommendations.md)

- [Come correggere le Centro sicurezza di Azure raccomandazioni](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** usare i Azure Resource Manager e Criteri di Azure per configurare in modo sicuro le risorse di Azure associate ai set di scalabilità di macchine virtuali.  Azure Resource Manager modelli sono file basati su JSON usati per distribuire la macchina virtuale insieme alle risorse di Azure e sarà necessario mantenere un modello personalizzato.  Microsoft esegue la manutenzione sui modelli di base.  Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Informazioni sulla creazione di Azure Resource Manager modelli](../virtual-machines/windows/ps-template.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni:** sono disponibili diverse opzioni per mantenere una configurazione sicura per le macchine virtuali di Azure per la distribuzione:

1. Azure Resource Manager modelli: si tratta di file basati su JSON usati per distribuire una macchina virtuale dal portale di Azure e sarà necessario mantenere un modello personalizzato. Microsoft esegue la manutenzione sui modelli di base.

2. Disco rigido virtuale (VHD) personalizzato: in alcune circostanze potrebbe essere necessario usare file VHD personalizzati, ad esempio quando si gestiscono ambienti complessi che non possono essere gestiti con altri mezzi.

3. Automazione di Azure State Configuration: dopo aver distribuito il sistema operativo di base, questo può essere usato per un controllo più granulare delle impostazioni e applicato tramite il framework di automazione.

Per la maggior parte degli scenari, i modelli di macchina virtuale di base Microsoft combinati Automazione di Azure Desired State Configuration possono essere utili per soddisfare e gestire i requisiti di sicurezza.

- [Informazioni su come scaricare il modello di macchina virtuale](/previous-versions/azure/virtual-machines/windows/download-template)

- [Informazioni sulla creazione di modelli arm](../virtual-machines/windows/ps-template.md)

- [Come caricare un disco rigido virtuale della macchina virtuale personalizzato in Azure](../virtual-machines/windows/upload-generalized-managed.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee** guida: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati, modelli Azure Resource Manager, Desired State Configuration script e così via.  Per accedere alle risorse gestite in Azure DevOps codice, compilazioni e rilevamento del lavoro, è necessario disporre delle autorizzazioni per tali risorse specifiche. La maggior parte delle autorizzazioni viene concessa tramite gruppi di sicurezza predefiniti, come descritto in Autorizzazioni e accesso. È possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps o Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni:** se si usano immagini personalizzate (ad esempio disco rigido virtuale), usare i controlli di accesso in base al ruolo di Azure per assicurarsi che solo gli utenti autorizzati possano accedere alle immagini.  

- [Informazioni sul controllo degli accessi in base al ruolo in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: sfruttare Criteri di Azure per avvisare, controllare e applicare configurazioni di sistema per le macchine virtuali. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee** guida: Automazione di Azure State Configuration è un servizio di gestione della configurazione per i nodi Desired State Configuration (DSC) in qualsiasi data center cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile eseguire facilmente l'onboard di computer, assegnarle configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer allo stato desiderato specificato. 

- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../automation/automation-dsc-onboarding.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee** guida: sfruttare Centro sicurezza di Azure eseguire analisi di base per le macchine virtuali di Azure.  Altri metodi per la configurazione automatica includono l'uso di Automazione di Azure State Configuration.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Introduzione a Configurazione stato di Automazione di Azure](../automation/automation-dsc-getting-started.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni:** Automazione di Azure State Configuration è un servizio di gestione della configurazione per i nodi Desired State Configuration (DSC) in qualsiasi data center cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile eseguire facilmente l'onboard delle macchine virtuali, assegnare loro configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../automation/automation-dsc-onboarding.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare l'identità del servizio gestita insieme Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare un Key Vault](../key-vault/general/quick-create-portal.md)

- [Come eseguire l'autenticazione Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio Key Vault di accesso](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Indicazioni:** usare Microsoft Antimalware macchine virtuali Windows di Azure per monitorare e proteggere continuamente le risorse.  Sarà necessario uno strumento di terze parti per la protezione antimalware nella macchina virtuale Linux di Azure. 

- [Come configurare i Microsoft Antimalware per servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee** guida: quando viene distribuito per le macchine virtuali Windows, Microsoft Antimalware per Azure installerà automaticamente gli aggiornamenti più recenti della firma, della piattaforma e del motore per impostazione predefinita. Seguire le raccomandazioni Centro sicurezza di Azure: "App di calcolo" per assicurarsi che tutti gli endpoint siano &amp; aggiornati con le firme più recenti. Il sistema operativo Windows può essere ulteriormente protetto con sicurezza aggiuntiva per limitare il rischio di attacchi basati su virus o malware con il servizio Microsoft Defender Advanced Threat Protection che si integra con Centro sicurezza di Azure.

Sarà necessario uno strumento di terze parti per la protezione antimalware nella macchina virtuale Linux di Azure. 

- [Come distribuire Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [Come configurare i Microsoft Antimalware per servizi cloud e macchine virtuali](../virtual-machines/security-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** creare uno snapshot dell'istanza del set di scalabilità di macchine virtuali di Azure o del disco gestito collegato all'istanza usando PowerShell o le API REST.  È anche possibile usare Automazione di Azure per eseguire gli script di backup a intervalli regolari.

- [Come creare uno snapshot di un'istanza del set di scalabilità di macchine virtuali e di un disco gestito](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Introduzione al Automazione di Azure](../automation/automation-intro.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** creare snapshot delle macchine virtuali di Azure o dei dischi gestiti collegati a tali istanze usando PowerShell o le API REST. Eseguire il backup di tutte le chiavi gestite dal cliente all'interno Azure Key Vault.

Abilitare Backup di Azure e le macchine virtuali di Azure di destinazione, nonché la frequenza e i periodi di conservazione desiderati. È incluso il backup completo dello stato del sistema. Se si usa Crittografia dischi di Azure, il backup delle macchine virtuali di Azure gestisce automaticamente il backup delle chiavi gestite dal cliente.

- [Backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)

- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

- [Come creare uno snapshot di un'istanza del set di scalabilità di macchine virtuali e di un disco gestito](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** garantire la possibilità di eseguire periodicamente il ripristino dei dati del disco gestito all'interno Backup di Azure. Se necessario, testare il contenuto di ripristino in una rete virtuale isolata o in una sottoscrizione. Cliente per testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

Se si usa Crittografia dischi di Azure, è possibile ripristinare i set di scalabilità di macchine virtuali con le chiavi di crittografia del disco. Quando si usa la crittografia del disco, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco.

- [Backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)

- [Ripristinare un disco e creare una macchina virtuale ripristinata in Azure](../backup/tutorial-restore-disk.md)

- [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Come abilitare la crittografia del disco per i set di scalabilità di macchine virtuali di Azure](disk-encryption-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** abilitare la protezione dall'eliminazione per il disco gestito usando i blocchi. Abilitare Soft-Delete e ripulire la protezione in Key Vault proteggere le chiavi da eliminazioni accidentali o dannose.  

- [Bloccare le risorse per impedire modifiche impreviste](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault panoramica della protezione da eliminazione e ripulitura](../key-vault/general/soft-delete-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.  

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sulla metrica usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità Automazione flusso di lavoro in Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" per gli avvisi di sicurezza e le raccomandazioni per proteggere le risorse di Azure. 

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di microsoft engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft per il Red Teaming e i test di penetrazione del sito in tempo reale per l'infrastruttura cloud gestita da Microsoft, i servizi e le applicazioni.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
