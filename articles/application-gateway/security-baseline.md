---
title: Baseline della sicurezza di Azure per il gateway applicazione
description: Il Baseline della sicurezza del gateway applicazione fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 75d219a207fef4f1f122799af5f4f4ef2765bca4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723861"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Baseline della sicurezza di Azure per il gateway applicazione

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) al gateway applicazione. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al gateway applicazione. I **controlli** non applicabili al gateway applicazione sono stati esclusi.

 
Per informazioni sul modo in cui il gateway applicazione esegue completamente il mapping al benchmark di sicurezza di Azure, vedere il [file di mapping di base di sicurezza del gateway applicazione completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: assicurarsi che tutte le distribuzioni di rete virtuale applicazione Azure subnet del gateway dispongano di un gruppo di sicurezza di rete (NSG) applicato con controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Sebbene i gruppi di sicurezza di rete siano supportati nel gateway applicazione Azure, esistono alcune restrizioni e requisiti che devono essere rispettati per consentire al gateway NSG e applicazione Azure di funzionare come previsto.

- [Informazioni sulle restrizioni e sui requisiti per l'uso di gruppi con applicazione Azure gateway](configuration-overview.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: per i gruppi di sicurezza di rete (gruppi) associati alle subnet del gateway applicazione Azure, abilitare i log di flusso di NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Nota: in alcuni casi i log di flusso NSG associati alle subnet del gateway applicazione Azure non visualizzeranno il traffico consentito. Se la configurazione corrisponde allo scenario seguente, non verrà visualizzato il traffico consentito nei log del flusso del gruppo di sicurezza di rete:

- È stato distribuito il gateway applicazione v2
- È presente un gruppo di sicurezza di rete nella subnet del gateway applicazione
- Sono stati abilitati i log del flusso del gruppo di sicurezza di rete nel gruppo di sicurezza

Per ulteriori informazioni, vedere i riferimenti seguenti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Domande frequenti per la diagnostica e la registrazione per applicazione Azure gateway](/azure/application-gateway/application-gateway-faq#what-types-of-logs-does-application-gateway-provide)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: distribuire Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Web Application Firewall (WAF) è un servizio (funzionalità del gateway applicazione Azure) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Azure WAF consente di proteggere le app Web del servizio app Azure controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS. WAF si basa su regole dei set di regole principali OWASP (Open Web Application Security Project) 3.1 (solo WAF_v2), 3.0 e 2.2.9. 

- [Informazioni sulle funzionalità del gateway applicazione Azure](features.md)

- [Informazioni su Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione standard DDoS nelle reti virtuali di Azure associate alle istanze di produzione di applicazione Azure gateway per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: per i gruppi di sicurezza di rete (gruppi) associati alle subnet del gateway applicazione Azure, abilitare i log di flusso di NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Nota: in alcuni casi i log di flusso NSG associati alle subnet del gateway applicazione Azure non visualizzeranno il traffico consentito. Se la configurazione corrisponde allo scenario seguente, non verrà visualizzato il traffico consentito nei log del flusso del gruppo di sicurezza di rete:

- È stato distribuito il gateway applicazione v2
- È presente un gruppo di sicurezza di rete nella subnet del gateway applicazione
- Sono stati abilitati i log del flusso del gruppo di sicurezza di rete nel gruppo di sicurezza

Per ulteriori informazioni, vedere i riferimenti seguenti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Domande frequenti per la diagnostica e la registrazione per applicazione Azure gateway](/azure/application-gateway/application-gateway-faq#what-types-of-logs-does-application-gateway-provide)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: distribuire Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Web Application Firewall (WAF) è un servizio (funzionalità del gateway applicazione Azure) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Azure WAF consente di proteggere le app Web del servizio app Azure controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS. WAF si basa su regole dei set di regole principali OWASP (Open Web Application Security Project) 3.1 (solo WAF_v2), 3.0 e 2.2.9. 

In alternativa, sono disponibili più opzioni di Marketplace come Barracuda WAF per Azure disponibili in Azure Marketplace, che include le funzionalità di ID/IP.

- [Informazioni sulle funzionalità del gateway applicazione Azure](features.md)

- [Informazioni su Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Informazioni su Barracuda WAF Cloud Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili.

- [Come distribuire il gateway applicazione](quick-create-portal.md)

- [Come configurare il gateway applicazione per l'uso di HTTPS](create-ssl-portal.md)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, GatewayManager) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Per i gruppi di sicurezza di rete (gruppi) associati alle subnet del gateway applicazione Azure, è necessario consentire il traffico Internet in ingresso sulle porte TCP 65503-65534 per lo SKU del gateway applicazione V1 e le porte TCP 65200-65535 per lo SKU V2 con la subnet di destinazione come qualsiasi e come origine come tag del servizio GatewayManager. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Le entità esterne, inclusi i clienti di tali gateway, non possono comunicare su questi endpoint.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

- [Panoramica della configurazione del gateway applicazione Azure](configuration-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni del gateway applicazione Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei gateway applicazione Azure, delle reti virtuali di Azure e dei gruppi di sicurezza di rete. È anche possibile usare la definizione di criteri incorporata.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per i gruppi di sicurezza di rete (gruppi) associati alla subnet del gateway applicazione Azure, nonché qualsiasi altra risorsa correlata alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni del gateway applicazione Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni o alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o a un account di archiviazione Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per il gateway applicazione Azure e le risorse correlate, ad esempio i gruppi di sicurezza di rete (gruppi), da usare per proteggere la subnet del gateway di applicazione Azure.

Oltre ai log attività, è possibile configurare le impostazioni di diagnostica per le distribuzioni del gateway applicazione Azure. le impostazioni di diagnostica vengono usate per configurare il flusso di esportazione dei log e delle metriche della piattaforma per una risorsa nella destinazione scelta (account di archiviazione, Hub eventi e Log Analytics).

Applicazione Azure Gateway offre anche l'integrazione incorporata con applicazione Azure Insights. Application Insights raccoglie i dati di log, delle prestazioni e degli errori. Application Insights rileva automaticamente le anomalie delle prestazioni e include potenti strumenti di analisi che consentono di diagnosticare i problemi e di comprendere come vengono usate le app Web. È possibile abilitare l'esportazione continua per esportare i dati di telemetria da Application Insights in una posizione centralizzata per mantenere i dati per un periodo di tempo superiore al periodo di conservazione standard.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

- [Come abilitare le impostazioni di diagnostica per applicazione Azure gateway](application-gateway-diagnostics.md)

- [Come abilitare Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Come configurare l'esportazione continua](../azure-monitor/app/export-telemetry.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o a un account di archiviazione Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per il gateway applicazione Azure e le risorse correlate, ad esempio i gruppi di sicurezza di rete (gruppi), da usare per proteggere la subnet del gateway di applicazione Azure.

Oltre ai log attività, è possibile configurare le impostazioni di diagnostica per le distribuzioni del gateway applicazione Azure. le impostazioni di diagnostica vengono usate per configurare il flusso di esportazione dei log e delle metriche della piattaforma per una risorsa nella destinazione scelta (account di archiviazione, Hub eventi e Log Analytics).

Applicazione Azure Gateway offre anche l'integrazione incorporata con applicazione Azure Insights. Application Insights raccoglie i dati di log, delle prestazioni e degli errori. Application Insights rileva automaticamente le anomalie delle prestazioni e include potenti strumenti di analisi che consentono di diagnosticare i problemi e di comprendere come vengono usate le app Web. È possibile abilitare l'esportazione continua per esportare i dati di telemetria da Application Insights in una posizione centralizzata per mantenere i dati per un periodo di tempo superiore al periodo di conservazione standard.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come abilitare le impostazioni di diagnostica per applicazione Azure gateway](application-gateway-diagnostics.md)

- [Come abilitare Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Come configurare l'esportazione continua](../azure-monitor/app/export-telemetry.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: In monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per il Gateway di applicazione Azure e inviare i log a un'area di lavoro di log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti.

Usare monitoraggio di Azure per le reti per una panoramica completa dell'integrità e delle metriche per tutte le risorse di rete distribuite, inclusi i gateway applicazione Azure. 

Facoltativamente, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

- [Come abilitare le impostazioni di diagnostica per applicazione Azure gateway](application-gateway-diagnostics.md)

- [Come usare monitoraggio di Azure per le reti](../azure-monitor/insights/network-insights-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: distribuire lo SKU di Azure Web Application Firewall (WAF) V2 davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Web Application Firewall (WAF) è un servizio (funzionalità del gateway applicazione Azure) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Azure WAF consente di proteggere le app Web del servizio app Azure controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS. WAF si basa su regole dei set di regole principali OWASP (Open Web Application Security Project) 3.1 (solo WAF_v2), 3.0 e 2.2.9. 

Abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per la WAF di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti.  È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

Usare monitoraggio di Azure per le reti per una panoramica completa dell'integrità e delle metriche per tutte le risorse di rete distribuite, inclusi i gateway applicazione Azure. Nella console monitoraggio di Azure per reti è possibile visualizzare e creare avvisi per applicazione Azure gateway.

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

- [Come abilitare le impostazioni di diagnostica per applicazione Azure gateway](application-gateway-diagnostics.md)

- [Come usare monitoraggio di Azure per le reti](../azure-monitor/insights/network-insights-overview.md)

- [Come creare avvisi in Azure](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni**: distribuire il firewall applicazione Web di Azure (WAF) V2 davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Web Application Firewall (WAF) è un servizio (funzionalità del gateway applicazione Azure) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Azure WAF consente di proteggere le app Web del servizio app Azure controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS.

Configurare le impostazioni di diagnostica per le distribuzioni di applicazione Azure gateway. le impostazioni di diagnostica vengono usate per configurare il flusso di esportazione dei log e delle metriche della piattaforma per una risorsa nella destinazione scelta (account di archiviazione, Hub eventi e Log Analytics).

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Come configurare le impostazioni di diagnostica per Azure WAF](application-gateway-diagnostics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: controllare l'accesso del piano a applicazione Azure gateway viene controllato tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Per ulteriori informazioni, vedere i riferimenti seguenti.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: usare una registrazione di app di Azure (entità servizio) per recuperare un token che può essere usato per interagire con i gateway applicazione Azure tramite chiamate API.

- [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Come registrare l'applicazione client (entità servizio) con Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informazioni sull'API dei servizi di ripristino di Azure](/rest/api/recoveryservices/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare le workstation Paw (Privileged Access workstation) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

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

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini dei log eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con qualsiasi strumento Siem/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di protezione delle identità Azure Active Directory (Azure ad) e di rilevamento del rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Verificare che tutte le distribuzioni di rete virtuale applicazione Azure subnet del gateway dispongano di un gruppo di sicurezza di rete (NSG) applicato ai controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Sebbene i gruppi di sicurezza di rete siano supportati nel gateway applicazione Azure, esistono alcune restrizioni e requisiti che devono essere rispettati per consentire al gateway NSG e applicazione Azure di funzionare come previsto.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Informazioni sulle restrizioni e sui requisiti per l'uso di gruppi con applicazione Azure gateway](configuration-overview.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: assicurarsi che tutte le distribuzioni di rete virtuale applicazione Azure subnet del gateway dispongano di un gruppo di sicurezza di rete (NSG) applicato con controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Limitare il traffico in uscita solo ai percorsi attendibili per attenuare il rischio di exfiltration dei dati. Sebbene i gruppi di sicurezza di rete siano supportati nel gateway applicazione Azure, esistono alcune restrizioni e requisiti che devono essere rispettati per consentire al gateway NSG e applicazione Azure di funzionare come previsto.

- [Informazioni sulle restrizioni e sui requisiti per l'uso di gruppi con applicazione Azure gateway](configuration-overview.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: configurare la crittografia end-to-end con TLS per i gateway applicazione Azure.

- [Come configurare TLS end-to-end usando applicazione Azure gateway](end-to-end-ssl-portal.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di controllo del gateway applicazione Azure (il portale di Azure).

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate all'ambiente di produzione applicazione Azure istanze del gateway, oltre ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: attualmente non disponibile; la valutazione della vulnerabilità nel centro sicurezza di Azure non è ancora disponibile per il gateway applicazione Azure.

Piattaforma sottostante analizzata e munita di patch applicata da Microsoft. Esaminare i controlli di sicurezza disponibili per applicazione Azure gateway per ridurre le vulnerabilità relative alla configurazione.

- [Copertura delle funzioni (inclusa la valutazione della vulnerabilità) per i servizi PaaS di Azure](../security-center/features-paas.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: non ancora disponibili; la valutazione della vulnerabilità nel centro sicurezza di Azure non è ancora disponibile per il gateway applicazione Azure.

Piattaforma sottostante analizzata e munita di patch applicata da Microsoft. Esaminare i controlli di sicurezza disponibili per applicazione Azure gateway per ridurre le vulnerabilità relative alla configurazione.

- [Copertura delle funzioni (inclusa la valutazione della vulnerabilità) per i servizi PaaS di Azure](../security-center/features-paas.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: non ancora disponibili; la valutazione della vulnerabilità nel centro sicurezza di Azure non è ancora disponibile per il gateway applicazione Azure.

Piattaforma sottostante analizzata e munita di patch applicata da Microsoft. Esaminare i controlli di sicurezza disponibili per applicazione Azure gateway per ridurre le vulnerabilità relative alla configurazione.

- [Copertura delle funzioni (inclusa la valutazione della vulnerabilità) per i servizi PaaS di Azure](../security-center/features-paas.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Per ulteriori informazioni, vedere i riferimenti seguenti.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. 

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Per ulteriori informazioni, vedere i riferimenti seguenti.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Verificare che tutte le distribuzioni di rete virtuale applicazione Azure subnet del gateway dispongano di un gruppo di sicurezza di rete (NSG) applicato ai controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Sebbene i gruppi di sicurezza di rete siano supportati nel gateway applicazione Azure, esistono alcune restrizioni e requisiti che devono essere rispettati per consentire al gateway NSG e applicazione Azure di funzionare come previsto.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Informazioni sulle restrizioni e sui requisiti per l'uso di gruppi con applicazione Azure gateway](configuration-overview.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni del gateway applicazione Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei gateway applicazione Azure, delle reti virtuali di Azure e dei gruppi di sicurezza di rete. È anche possibile usare la definizione di criteri incorporata.

- [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: Se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità gestite per fornire al gateway applicazione Azure un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

Usare Azure Key Vault per archiviare in modo sicuro i certificati. Azure Key Vault è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere i segreti, le chiavi e i certificati SSL. Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. Questo supporto è limitato allo SKU del gateway applicazione v2.

- [Come configurare la terminazione SSL con Key Vault certificati utilizzando Azure PowerShell](configure-keyvault-ps.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire al gateway applicazione Azure un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

Usare Azure Key Vault per archiviare in modo sicuro i certificati. Azure Key Vault è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere i segreti, le chiavi e i certificati SSL. Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. Questo supporto è limitato allo SKU del gateway applicazione v2.

- [Come configurare la terminazione SSL con Key Vault certificati utilizzando Azure PowerShell](configure-keyvault-ps.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: distribuire il firewall applicazione Web di Azure (WAF) V2 davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Web Application Firewall (WAF) è un servizio (funzionalità del gateway applicazione Azure) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Azure WAF consente di proteggere le app Web del servizio app Azure controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS.

Configurare le impostazioni di diagnostica per le distribuzioni di applicazione Azure gateway. le impostazioni di diagnostica vengono usate per configurare il flusso di esportazione dei log e delle metriche della piattaforma per una risorsa nella destinazione scelta (account di archiviazione, Hub eventi e Log Analytics).

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Come configurare le impostazioni di diagnostica per Azure WAF](application-gateway-diagnostics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee guida**: quando si usa il Web Application Firewall (WAF) di Azure, è possibile configurare i criteri di WAF. Un criterio WAF è costituito da due tipi di regole di sicurezza: regole personalizzate create dal cliente e set di regole gestite che sono una raccolta di regole preconfigurate gestite da Azure. I set di regole gestiti da Azure consentono di distribuire in modo semplice la protezione da un set comune di minacce alla sicurezza. Poiché questi set sono gestiti da Azure, le regole vengono opportunamente aggiornate per assicurare la protezione dalle nuove firme di attacco.

- [Informazioni sui set di regole WAF gestite da Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy-and-rules)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: applicazione Azure gateway non archivia i dati dei clienti. Tuttavia, se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

Azure DevOps Services sfrutta molte funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza nell'area. Per di più, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Informazioni sulla disponibilità dei dati in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: eseguire il backup dei certificati gestiti dal cliente all'interno Azure Key Vault.

- [Come eseguire il backup dei certificati di Key Vault in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino di test di certificati gestiti dal cliente sottoposti a backup.

- [Come ripristinare i certificati dell'insieme di credenziali delle chiavi](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: assicurarsi che l'eliminazione temporanea sia abilitata per Azure Key Vault. L'eliminazione temporanea consente di recuperare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati.

- [Come usare l'eliminazione temporanea di Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

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

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
