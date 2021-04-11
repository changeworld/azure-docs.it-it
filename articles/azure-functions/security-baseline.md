---
title: Baseline della sicurezza di Azure per funzioni di Azure
description: La linea di base di sicurezza di funzioni di Azure fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8987c4ac61e81c35f664e19dfc72936bb6754e18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566959"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Baseline della sicurezza di Azure per funzioni di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) alle funzioni di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a funzioni di Azure. I **controlli** non applicabili alle funzioni di Azure sono stati esclusi.

 
Per informazioni sul modo in cui funzioni di Azure esegue il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza di funzioni di Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: integrare le app di funzioni di Azure con una rete virtuale di Azure. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità "integrazione VNet".  Le reti virtuali di Azure consentono di inserire molte delle risorse di Azure, ad esempio funzioni di Azure, in una rete instradabile non Internet.

- [Come integrare funzioni con una rete virtuale di Azure](functions-create-vnet.md)

- [Informazioni sull'integrazione di VNET per funzioni di Azure e servizio app Azure](../app-service/web-sites-integrate-with-vnet.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete e le configurazioni di rete correlate alle app di funzioni di Azure.

Se si usano gruppi di sicurezza di rete con l'implementazione di funzioni di Azure, abilitare i gruppi di sicurezza di rete e inviare i log a un account di archiviazione di Azure per i controlli del traffico. È anche possibile inviare i log di flusso dei gruppi di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: per proteggere completamente gli endpoint di funzioni di Azure nell'ambiente di produzione, è consigliabile implementare una delle seguenti opzioni di sicurezza a livello di app per le funzioni:

- Attivare l'autenticazione o l'autorizzazione del servizio app per l'app per le funzioni

- Usare Gestione API di Azure (APIM) per autenticare le richieste

- Distribuire l'app per le funzioni in un ambiente del servizio app di Azure.

Assicurarsi inoltre che il debug remoto sia stato disabilitato per le funzioni di Azure di produzione. Inoltre, la condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'app per le funzioni in Azure. Consente solo ai domini richiesti di interagire con l'app per le funzioni.

Prendere in considerazione la distribuzione di Web Application Firewall (WAF) di Azure come parte della configurazione di rete per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics. 

- [Come proteggere gli endpoint di funzioni di Azure nell'ambiente di produzione](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate alle app per le funzioni per proteggersi da attacchi DDoS. Usare le funzionalità di intelligence per le minacce nel centro sicurezza di Azure integrato per negare le comunicazioni con indirizzi IP pubblici dannosi o inutilizzati noti.

Inoltre, configurare un gateway front-end, ad esempio Web Application Firewall di Azure, per autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Il Web Application Firewall di Azure consente di proteggere l'app per le funzioni controllando il traffico Web in ingresso per bloccare gli attacchi SQL injection, gli script tra siti, i caricamenti di malware e gli attacchi DDoS. L'introduzione di un Web Application Firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani Premium e di servizio app.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: se si usa un gruppo di sicurezza di rete con l'implementazione di funzioni di Azure, abilitare i log di flusso dei gruppi di sicurezza di rete e inviare i log a un account di archiviazione per il controllo È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: configurare un gateway front-end, ad esempio Web Application Firewall di Azure per autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Il Web Application Firewall di Azure consente di proteggere le app per le funzioni controllando il traffico Web in ingresso per bloccare gli attacchi SQL injection, gli script tra siti, i caricamenti di malware e gli attacchi DDoS. L'introduzione di un Web Application Firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani Premium e di servizio app.

In alternativa, esistono più opzioni del Marketplace come il Web Application Firewall di Barracuda per Azure disponibili in Azure Marketplace che includono funzionalità di rilevamento o prevenzione delle intrusioni.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Piano Premium di funzioni di Azure](functions-premium-plan.md)

- [Introduzione agli ambienti del servizio app](../app-service/environment/intro.md)

- [Considerazioni sulla rete per un ambiente del servizio app](../app-service/environment/network-info.md) 

- [Informazioni sul firewall applicazione Web di Azure](../web-application-firewall/overview.md)

- [Uso di endpoint privati per funzioni di Azure](../app-service/networking/private-endpoint.md)

- [Informazioni su Barracuda WAF Cloud Service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: configurare un gateway front-end per la rete, ad esempio il firewall applicazione Web di Azure con la crittografia TLS end-to-end. L'introduzione di un Web Application Firewall richiede un ambiente del servizio app o l'uso di endpoint privati. È possibile usare endpoint privati per le funzioni ospitate nei piani Premium e di servizio app.

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Piano Premium di funzioni di Azure](functions-premium-plan.md)

- [Introduzione agli ambienti del servizio app](../app-service/environment/intro.md)

- [Considerazioni sulla rete per un ambiente del servizio app](../app-service/environment/network-info.md) 

- [Informazioni sul firewall applicazione Web di Azure](../web-application-firewall/overview.md)

- [Come configurare TLS end-to-end usando il gateway applicazione con il portale](../application-gateway/end-to-end-ssl-portal.md)

- [Uso di endpoint privati per funzioni di Azure](../app-service/networking/private-endpoint.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nel gruppo di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, AzureAppService) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle funzioni di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. Web" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle funzioni di Azure. È anche possibile usare le definizioni di criteri predefinite per funzioni di Azure, ad esempio:

- CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni

- L'app per le funzioni deve essere accessibile solo tramite HTTPS

- Nell'app per le funzioni è necessario usare la versione più recente di TLS

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: se si usa un gruppo di sicurezza di rete con l'implementazione di funzioni di Azure, usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata e così via, per tutte le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni di funzioni di Azure. Creare avvisi in monitoraggio di Azure che si attiveranno quando si verificano modifiche alle impostazioni o alle risorse di rete critiche. 

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Funzioni di Azure offre anche l'integrazione incorporata con applicazione Azure Insights per monitorare le funzioni. Application Insights raccoglie i dati di log, delle prestazioni e degli errori. Oltre a rilevare automaticamente le anomalie nelle prestazioni, include strumenti di analisi avanzati che consentono di diagnosticare i problemi e conoscere come vengono usate le funzioni.

Se nell'app per le funzioni è incorporata la registrazione di sicurezza/controllo personalizzata, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e delle informazioni di sistema di terze parti. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come configurare funzioni di Azure con applicazione Azure Insights](functions-monitoring.md)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per funzioni di Azure](functions-monitor-log-analytics.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Se nell'app per le funzioni è incorporata la registrazione di sicurezza/controllo personalizzata, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per funzioni di Azure](functions-monitor-log-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione del log per le aree di lavoro log Analytics associate alle app per le funzioni in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per l'app per le funzioni e inviare i log a un'area di lavoro log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti.

Abilitare Application Insights per le app per le funzioni per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti da Application Insights all'interno del portale di Azure.

Se nell'app per le funzioni è incorporata la registrazione di sicurezza/controllo personalizzata, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e delle informazioni di sistema di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica per funzioni di Azure](functions-monitor-log-analytics.md)

- [Come configurare funzioni di Azure con applicazione Azure Insights e visualizzare i dati di telemetria](functions-monitoring.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per l'app per le funzioni e inviare i log a un'area di lavoro log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti. È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

Abilitare Application Insights per le app per le funzioni per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti da Application Insights e creare avvisi all'interno del portale di Azure.

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e delle informazioni di sistema di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come abilitare le impostazioni di diagnostica per funzioni di Azure](functions-monitor-log-analytics.md)

- [Come abilitare Application Insights per funzioni di Azure](./configure-monitoring.md#enable-application-insights-integration)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'accesso del piano di controllo alle funzioni di Azure viene controllato tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati può essere controllato tramite diversi mezzi, incluse le chiavi di autorizzazione, le restrizioni di rete e la convalida di un'identità Azure AD. Le chiavi di autorizzazione vengono usate dai client che si connettono agli endpoint HTTP di funzioni di Azure e possono essere rigenerate in qualsiasi momento. Per impostazione predefinita, queste chiavi vengono generate per i nuovi endpoint HTTP.

Sono disponibili più metodi di distribuzione per le app per le funzioni, alcune delle quali possono sfruttare un set di credenziali generate. Esaminare i metodi di distribuzione che verranno usati per l'applicazione.

- [Proteggere un endpoint HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come ottenere e rigenerare le chiavi di autorizzazione](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Tecnologie di distribuzione in funzioni di Azure](functions-deployment-technologies.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso ](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: laddove possibile, usare Azure Active Directory (Azure ad) SSO anziché configurare singole credenziali autonome per l'accesso ai dati all'app per le funzioni. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure. Implementare Single Sign-On per le app per le funzioni usando la funzionalità di autenticazione/autorizzazione del servizio app.

- [Informazioni sull'autenticazione e l'autorizzazione in funzioni di Azure](../app-service/overview-authentication-authorization.md#identity-providers)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app per le funzioni. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come configurare l'app per le funzioni per l'uso di Azure AD account di accesso](../app-service/configure-authentication-provider-aad.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

Materiale sussidiario **: Azure Active Directory**(Azure ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app per le funzioni. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

È possibile accedere alle origini del registro eventi di attività, controllo e rischio Azure AD di accesso, che consentono di eseguire l'integrazione con Azure Sentinel o con SIEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

- [Come configurare l'app per le funzioni per l'uso di Azure AD account di accesso](../app-service/configure-authentication-provider-aad.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app per le funzioni. Per la deviazione del comportamento di accesso dell'account nel piano di controllo (il portale di Azure), usare Azure AD Identity Protection e funzionalità di rilevamento dei rischi per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le app per le funzioni devono essere separate da rete virtuale (VNet)/della subnet e contrassegnate in modo appropriato.

È anche possibile usare endpoint privati per eseguire l'isolamento rete. Un endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio (ad esempio, endpoint HTTPs per l'app per le funzioni) con tecnologia di collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. È possibile usare endpoint privati per le funzioni ospitate nei piani Premium e di servizio app.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

- [Piano Premium di funzioni di Azure](functions-premium-plan.md)

- [Informazioni sull'endpoint privato](../private-link/private-endpoint-overview.md)

- [Uso di endpoint privati per funzioni di Azure](../app-service/networking/private-endpoint.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni**: nella portale di Azure per le app per le funzioni, in "funzionalità piattaforma: rete: SSL", abilitare l'impostazione "solo HTTPS" e impostare la versione minima di TLS su 1,2.

- [Richiedi HTTPS nelle app per le funzioni](./security-concepts.md#require-https)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: non attualmente disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono attualmente disponibili per funzioni di Azure. Contrassegnare le app per le funzioni che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso al piano di controllo delle app per le funzioni (il portale di Azure).

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

Microsoft gestisce l'infrastruttura sottostante per funzioni di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Questo perché Funzioni di Azure si basa internamente su Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati di BLOB e file. Queste chiavi devono essere presenti in Azure Key Vault affinché l'app per le funzioni sia in grado di accedere all'account di archiviazione.

- [Informazioni sulle considerazioni sull'archiviazione per funzioni di Azure](storage-considerations.md)

- [Comprendere la crittografia di archiviazione di Azure per i dati inattivi](../storage/common/storage-service-encryption.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle app per le funzioni di produzione e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: adottare una procedura DevSecOps per garantire che le app per le funzioni siano sicure e rimangano nel modo più sicuro possibile per tutta la durata del ciclo di vita. DevSecOps incorpora il team di sicurezza dell'organizzazione e le relative funzionalità nelle procedure DevOps che rendono la sicurezza una responsabilità di tutti gli utenti del team.

Inoltre, seguire le raccomandazioni del Centro sicurezza di Azure per proteggere le app per le funzioni.

- [Come aggiungere la convalida della sicurezza continua alla pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano funzioni di Azure, ma è possibile usare la gravità delle raccomandazioni nel centro sicurezza di Azure e il Punteggio sicuro per misurare i rischi all'interno dell'ambiente. Il Punteggio sicuro si basa sul numero di raccomandazioni del Centro sicurezza che sono state mitigate. Per definire la priorità delle raccomandazioni da risolvere per prima cosa, considerare la gravità di ciascuna di esse.

- [Guida di riferimento per le raccomandazioni sulla sicurezza](../security-center/recommendations-reference.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

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

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni**: Definire il software e le risorse di Azure approvate per le risorse di calcolo.

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

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per l'app per le funzioni con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per controllare o applicare la configurazione delle app per le funzioni. È anche possibile usare le definizioni di criteri predefinite, ad esempio:

- Nell'app per le funzioni è necessario usare un'identità gestita

- Il debug remoto deve essere disattivato per le app per le funzioni

- l'app per le funzioni deve essere accessibile solo tramite HTTPS

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: archiviare e gestire i modelli ARM e le definizioni di criteri personalizzati di Azure in modo sicuro nel controllo del codice sorgente.

- [Che cos'è l'infrastruttura come codice](/azure/devops/learn/what-is-infrastructure-as-code)

- [Criteri di progettazione come flussi di lavoro di codice](../governance/policy/concepts/policy-as-code.md)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità gestite insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Identità gestite consente all'app per le funzioni di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure Active Directory (Azure AD), incluso Key Vault, senza credenziali nel codice.

- [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../app-service/overview-managed-identity.md)

- [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Usare i riferimenti Key Vault per il servizio app e funzioni di Azure](../app-service/app-service-key-vault-references.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire all'app per le funzioni un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../app-service/overview-managed-identity.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Indicazioni**: usare la funzionalità di backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di backup e ripristino.

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps per archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza nell'area. Per di più, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](../app-service/manage-backup.md)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: usare la funzionalità di backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di backup e ripristino. Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault.

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps per archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza nell'area. Per di più, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](../app-service/manage-backup.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino dalla funzionalità di backup e ripristino. Se si usa un altro percorso offline per eseguire il backup del codice, verificare periodicamente la possibilità di eseguire ripristini completi. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Ripristinare un'app in Azure da un backup](../app-service/web-sites-restore.md)

- [Ripristinare un'app in Azure da uno snapshot](../app-service/app-service-web-restore-snapshots.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: i backup della funzionalità di backup e ripristino usano un account di archiviazione di Azure nella sottoscrizione. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati di archiviazione.

Se si usano chiavi gestite dal cliente, assicurarsi che Soft-Delete in Key Vault sia abilitato per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Crittografia dei dati inattivi di Archiviazione di Azure](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guida alla gestione degli eventi di sicurezza del computer NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Condiviso

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

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza con le app per la logica.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
