---
title: Baseline di sicurezza di Azure per Funzioni di Azure
description: La baseline Funzioni di Azure sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831079"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Baseline di sicurezza di Azure per Funzioni di Azure

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Funzioni di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Funzioni di Azure. **I** controlli non applicabili Funzioni di Azure sono stati esclusi.

 
Per informazioni su come Funzioni di Azure completamente mappato ad Azure Security Benchmark, vedere il file di mapping completo Funzioni di Azure baseline [di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** integrare le app Funzioni di Azure con una rete virtuale di Azure. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting delle app Web in Servizio app di Azure, che include la funzionalità "Integrazione rete virtuale".  Le reti virtuali di Azure consentono di inserire molte delle risorse di Azure, ad esempio Funzioni di Azure, in una rete instradabile non Internet.

- [Come integrare Funzioni con una rete virtuale di Azure](functions-create-vnet.md)

- [Informazioni sull'integrazione rete virtuale Funzioni di Azure e Servizio app di Azure](../app-service/web-sites-integrate-with-vnet.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione di rete per proteggere le risorse di rete e le configurazioni di rete correlate alle app Funzioni di Azure rete.

Se si usano gruppi di sicurezza di rete con l'implementazione Funzioni di Azure, abilitare i log di flusso dei gruppi di sicurezza di rete e inviare i log in un account Archiviazione di Azure per i controlli del traffico. È anche possibile inviare log di flusso dei gruppi di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee** guida: per proteggere completamente Funzioni di Azure endpoint nell'ambiente di produzione, è consigliabile implementare una delle opzioni di sicurezza a livello di app per le funzioni seguenti:

- Attivare l'autenticazione o l'autorizzazione del servizio app per l'app per le funzioni

- Usare Gestione API di Azure (APIM) per autenticare le richieste

- Distribuire l'app per le funzioni in un ambiente del servizio app di Azure.

Assicurarsi inoltre che il debug remoto sia stato disabilitato per l'ambiente di Funzioni di Azure. Inoltre, Condivisione risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'app per le funzioni in Azure. Consentire solo ai domini necessari di interagire con l'app per le funzioni.

Prendere in considerazione la Web application firewall di Azure (WAF) come parte della configurazione di rete per un'ispezione aggiuntiva del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics. 

- [Come proteggere Funzioni di Azure endpoint nell'ambiente di produzione](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare Protezione DDoS Standard nelle reti virtuali associate alle app per le funzioni per proteggersi dagli attacchi DDoS. Usare le funzionalità di Intelligence per le minacce Centro sicurezza di Azure integrate per negare le comunicazioni con indirizzi IP pubblici noti dannosi o inutilizzati.

Configurare anche un gateway front-end, ad esempio Web application firewall di Azure, per autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Web application firewall di Azure proteggere l'app per le funzioni controllando il traffico Web in ingresso per bloccare attacchi SQL injection, cross-site scripting, caricamenti di malware e attacchi DDoS. L'introduzione di un web application firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani premium e di servizio app.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** se si usa un gruppo di sicurezza di rete con l'implementazione Funzioni di Azure, abilitare i log dei flussi del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** configurare un gateway front-end, ad esempio Web application firewall di Azure autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Web application firewall di Azure proteggere le app per le funzioni controllando il traffico Web in ingresso per bloccare attacchi SQL injection, cross-site scripting, caricamenti di malware e attacchi DDoS. L'introduzione di un web application firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani premium e di servizio app.

In alternativa, sono disponibili più opzioni del marketplace, ad esempio Barracuda Web Application Firewall per Azure, disponibili nel Azure Marketplace che includono funzionalità di rilevamento o prevenzione delle intrusioni.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Funzioni di Azure Piano Premium](functions-premium-plan.md)

- [Introduzione agli ambienti del servizio app](../app-service/environment/intro.md)

- [Considerazioni sulla rete per un ambiente del servizio app](../app-service/environment/network-info.md) 

- [Informazioni Web application firewall di Azure](../web-application-firewall/overview.md)

- [Uso di endpoint privati per Funzioni di Azure](../app-service/networking/private-endpoint.md)

- [Informazioni sul servizio cloud Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni:** configurare un gateway front-end per la rete, ad esempio Web application firewall di Azure con crittografia TLS end-to-end. L'introduzione di web application firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani di servizio app e Premium.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Funzioni di Azure Piano Premium](functions-premium-plan.md)

- [Introduzione agli ambienti del servizio app](../app-service/environment/intro.md)

- [Considerazioni sulla rete per un ambiente del servizio app](../app-service/environment/network-info.md) 

- [Informazioni Web application firewall di Azure](../web-application-firewall/overview.md)

- [Come configurare TLS end-to-end usando il gateway applicazione con il portale](../application-gateway/end-to-end-ssl-portal.md)

- [Uso di endpoint privati per Funzioni di Azure](../app-service/networking/private-endpoint.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio Di rete virtuale per definire i controlli di accesso alla rete nel gruppo di sicurezza di rete o Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio ,ad esempio AzureAppService, nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Per altre informazioni sull'uso dei tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate all'Funzioni di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.Web" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del Funzioni di Azure. È anche possibile usare definizioni di criteri predefiniti per Funzioni di Azure, ad esempio:

- CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni

- L'app per le funzioni deve essere accessibile solo tramite HTTPS

- Nell'app per le funzioni deve essere usata la versione più recente di TLS

È anche possibile usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure e criteri in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** se si usa un gruppo di sicurezza di rete con l'implementazione Funzioni di Azure, usare tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata e così via, per tutte le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni Funzioni di Azure rete. Creare avvisi all'interno Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni di rete o alle risorse critiche. 

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Funzioni di Azure offre anche l'integrazione incorporata con applicazione Azure Insights per monitorare le funzioni. Application Insights raccoglie i dati di log, prestazioni ed errore. Oltre a rilevare automaticamente le anomalie nelle prestazioni, include strumenti di analisi avanzati che consentono di diagnosticare i problemi e conoscere come vengono usate le funzioni.

Se all'interno dell'app per le funzioni è disponibile una registrazione personalizzata di sicurezza/controllo, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel o una soluzione di gestione degli eventi e delle informazioni di sistema di terze parti. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come configurare Funzioni di Azure con applicazione Azure Insights](functions-monitoring.md)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per Funzioni di Azure](functions-monitor-log-analytics.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee** guida: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Se all'interno dell'app per le funzioni è disponibile una registrazione personalizzata di sicurezza/controllo, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per Funzioni di Azure](functions-monitor-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida:** non applicabile; questa linea guida è destinata alle risorse di calcolo IaaS.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee** guida: in Monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro Log Analytics associate alle app per le funzioni in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure e le impostazioni di diagnostica per l'app per le funzioni e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti.

Abilitare Application Insights per le app per le funzioni per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti dai Application Insights all'interno del portale di Azure.

Se si dispone di una registrazione di sicurezza/controllo personalizzata predefinita all'interno dell'app per le funzioni, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel o una soluzione di gestione di eventi e informazioni di sistema di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica per Funzioni di Azure](functions-monitor-log-analytics.md)

- [Come configurare l'Funzioni di Azure con applicazione Azure Insights e visualizzare i dati di telemetria](functions-monitoring.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure e le impostazioni di diagnostica per l'app per le funzioni e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti. È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

Abilitare Application Insights per le app per le funzioni per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti dai Application Insights e creare avvisi all'interno del portale di Azure.

Facoltativamente, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel o una soluzione di gestione di eventi e informazioni di sistema di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica per Funzioni di Azure](functions-monitor-log-analytics.md)

- [Come abilitare la Application Insights per Funzioni di Azure](./configure-monitoring.md#enable-application-insights-integration)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono disponibili per query. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee** guida: l'accesso al piano di Funzioni di Azure è controllato tramite Azure Active Directory (Azure AD). Azure AD non ha il concetto di password predefinite.

L'accesso al piano dati può essere controllato in diversi modi, tra cui chiavi di autorizzazione, restrizioni di rete e convalida di un'Azure AD identità. Le chiavi di autorizzazione vengono usate dai client che si connettono Funzioni di Azure endpoint HTTP e possono essere rigenerate in qualsiasi momento. Queste chiavi vengono generate per i nuovi endpoint HTTP per impostazione predefinita.

Sono disponibili più metodi di distribuzione per le app per le funzioni, alcuni dei quali possono sfruttare un set di credenziali generate. Esaminare i metodi di distribuzione che verranno usati per l'applicazione.

- [Proteggere un endpoint HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come ottenere e rigenerare le chiavi di autorizzazione](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Tecnologie di distribuzione in Funzioni di Azure](functions-deployment-technologies.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come usare i Centro sicurezza di Azure per monitorare l'identità e l'accesso ](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare Azure Active Directory (Azure AD) SSO invece di configurare singole credenziali autonome per l'accesso ai dati all'app per le funzioni. Usare Centro sicurezza di Azure indicazioni su Gestione identità e accessi. Implementare l'accesso Single Sign-On per le app per le funzioni usando la funzionalità di autenticazione/autorizzazione del servizio app.

- [Informazioni su autenticazione e autorizzazione in Funzioni di Azure](../app-service/overview-authentication-authorization.md#identity-providers)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi Azure Active Directory basato su autenticazione

**Indicazioni:** abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori e seguire le raccomandazioni Centro sicurezza di Azure gestione delle identità e degli accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare workstation con accesso con privilegi (PAW) con l'autenticazione a più fattori configurata per accedere e configurare le risorse di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare in caso di attività sospette da account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le app per le funzioni. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come configurare l'app per le funzioni per l'uso Azure AD accesso](../app-service/configure-authentication-provider-aad.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare anche le verifiche di accesso delle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni Azure AD report](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le app per le funzioni. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

È possibile accedere a Azure AD attività di accesso, alle origini del log eventi di controllo e di rischio, che consentono di eseguire l'integrazione con Azure Sentinel o un SIEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi di log desiderati in Log Analytics.

- [Come configurare l'app per le funzioni per l'uso Azure AD login](../app-service/configure-authentication-provider-aad.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le app per le funzioni. Per la deviazione del comportamento di accesso dell'account nel piano di controllo (portale di Azure), usare le funzionalità di rilevamento dei rischi e Azure AD Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le app per le funzioni devono essere separate dalla rete virtuale (VNet)/subnet e contrassegnate in modo appropriato.

È anche possibile usare endpoint privati per eseguire l'isolamento della rete. Un endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio (ad esempio, endpoint HTTP dell'app per le funzioni) basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. È possibile usare endpoint privati per le funzioni ospitate nei piani di servizio app e Premium.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Funzioni di Azure Piano Premium](functions-premium-plan.md)

- [Informazioni sull'endpoint privato](../private-link/private-endpoint-overview.md)

- [Uso di endpoint privati per Funzioni di Azure](../app-service/networking/private-endpoint.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** nel portale di Azure per le app per le funzioni, in "Funzionalità della piattaforma: Rete: SSL", abilitare l'impostazione "Solo HTTPs" e impostare la versione minima di TLS su 1.2.

- [Richiedere HTTPS nelle app per le funzioni](./security-concepts.md#require-https)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** non attualmente disponibile; Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono attualmente disponibili per Funzioni di Azure. App per le funzioni tag che potrebbero elaborare informazioni riservate come tali e implementare soluzioni di terze parti, se necessario per motivi di conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso al piano di controllo delle app per le funzioni (il portale di Azure).

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni:** non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

Microsoft gestisce l'infrastruttura sottostante per Funzioni di Azure e ha implementato rigorosi controlli per impedire la perdita o l'esposizione dei dati dei clienti.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** quando si crea un'app per le funzioni, è necessario creare o collegarsi a un account di Archiviazione di Azure per utilizzo generico che supporta l'archiviazione BLOB, code e tabelle. Questo perché Funzioni di Azure si basa internamente su Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati blob e file. Queste chiavi devono essere presenti nel Azure Key Vault che l'app per le funzioni possa accedere all'account di archiviazione.

- [Informazioni sulle considerazioni sull'archiviazione per Funzioni di Azure](storage-considerations.md)

- [Informazioni sulla crittografia di archiviazione di Azure per i dati in stato di inquieto](../storage/common/storage-service-encryption.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee** guida: usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche alle app per le funzioni di produzione, nonché ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee** guida: adottare una procedura DevSecOps per garantire che le app per le funzioni siano sicure e rimangano il più sicure possibile per tutta la durata del ciclo di vita. DevSecOps incorpora il team di sicurezza dell'organizzazione e le relative funzionalità nelle procedure DevOps, rendendo la sicurezza una responsabilità di tutti gli utenti del team.

Per proteggere le app per le funzioni, Centro sicurezza di Azure anche seguire le raccomandazioni fornite da questa pagina.

- [Come aggiungere la convalida continua della sicurezza alla pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

Linee **guida:** Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Funzioni di Azure, tuttavia è possibile usare la gravità delle raccomandazioni all'interno di Centro sicurezza di Azure e il punteggio di sicurezza per misurare i rischi all'interno dell'ambiente. Il punteggio di sicurezza si basa sul numero di raccomandazioni del Centro sicurezza mitigate. Per classificare in ordine di priorità le raccomandazioni da risolvere per prime, considerare la gravità di ognuna.

- [Guida di riferimento alle raccomandazioni per la sicurezza](../security-center/recommendations-reference.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Informazioni aggiuntive sono disponibili nei collegamenti a cui si fa riferimento.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni**: Definire il software e le risorse di Azure approvate per le risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni. 

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Informazioni aggiuntive sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per l'app per le funzioni con Criteri di Azure. Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Web" per creare criteri personalizzati per controllare o applicare la configurazione delle app per le funzioni. È anche possibile usare definizioni di criteri incorporate, ad esempio:

- Nell'app per le funzioni è necessario usare un'identità gestita

- Il debug remoto deve essere disattivato per le app per le funzioni

- L'app per le funzioni deve essere accessibile solo tramite HTTPS

Informazioni aggiuntive sono disponibili nei collegamenti a cui si fa riferimento.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** usare i criteri di Azure [deny] e [distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** archiviare e gestire in modo sicuro i modelli arm e le definizioni dei criteri di Azure personalizzati nel controllo del codice sorgente.

- [Che cos'è l'infrastruttura come codice](/azure/devops/learn/what-is-infrastructure-as-code)

- [Progettare criteri come flussi di lavoro del codice](../governance/policy/concepts/policy-as-code.md)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: usare definizioni Criteri di Azure predefinite e alias Criteri di Azure nello spazio dei nomi "Microsoft.Web" per creare criteri personalizzati per avvisare, controllare e applicare configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Linee** guida: usare le definizioni Criteri di Azure predefinite e gli alias Criteri di Azure nello spazio dei nomi "Microsoft.Web" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee** guida: usare le identità gestite in combinazione con Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Identità gestite consente all'app per le funzioni di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure Active Directory (Azure AD), incluso Key Vault, senza credenziali nel codice.

- [Come creare un Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../app-service/overview-managed-identity.md)

- [Come eseguire l'autenticazione Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio Key Vault di accesso](../key-vault/general/assign-access-policy-portal.md)

- [Usare Key Vault per il servizio app e Funzioni di Azure](../app-service/app-service-key-vault-references.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare identità gestite per fornire all'app per le funzioni un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../app-service/overview-managed-identity.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** usare la funzionalità Backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting delle app Web in Servizio app di Azure, che include la funzionalità "Backup e ripristino".

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza nell'area. Per di più, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](../app-service/manage-backup.md)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** usare la funzionalità Backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting delle app Web in Servizio app di Azure, che include la funzionalità "Backup e ripristino". Eseguire il backup delle chiavi gestite dal cliente Azure Key Vault.

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza nell'area. Per di più, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](../app-service/manage-backup.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** garantire la possibilità di eseguire periodicamente il ripristino dalla funzionalità Backup e ripristino. Se si usa un'altra posizione offline per eseguire il backup del codice, assicurarsi periodicamente di poter eseguire il ripristino completo. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Ripristinare un'app in Azure da un backup](../app-service/web-sites-restore.md)

- [Ripristinare un'app in Azure da uno snapshot](../app-service/app-service-web-restore-snapshots.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** i backup dalla funzionalità Backup e ripristino usano un account Archiviazione di Azure nella sottoscrizione. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati di archiviazione.

Se si usano chiavi gestite dal cliente, assicurarsi Soft-Delete in Key Vault sia abilitata per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Crittografia dei dati inattivi di Archiviazione di Azure](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee** guida: usare la funzionalità Automazione flusso di lavoro Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi di sicurezza e alle raccomandazioni con App per la logica.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di microsoft engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft per il Red Teaming e i test di penetrazione del sito in tempo reale per l'infrastruttura cloud gestita da Microsoft, i servizi e le applicazioni.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
