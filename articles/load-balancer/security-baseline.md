---
title: Baseline di sicurezza di Azure per Azure Load Balancer
description: La Azure Load Balancer di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589262"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Baseline di sicurezza di Azure per Azure Load Balancer

Questa baseline di sicurezza applica le linee guida di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Microsoft Azure Load Balancer. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili Azure Load Balancer. **I** controlli non applicabili Azure Load Balancer sono stati esclusi.

 
Per informazioni su come Azure Load Balancer completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Azure Load Balancer [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** usare Azure Load Balancers interno per consentire solo il traffico alle risorse back-end dall'interno di determinate reti virtuali o reti virtuali con peered senza esposizione a Internet. Implementare un Load Balancer esterno con la rete di origine

Address Translation (SNAT) per mascherare gli indirizzi IP delle risorse back-end per la protezione dall'esposizione diretta a Internet.

Azure offre due tipi di Load Balancer offerte, Standard e Basic. Usare il Load Balancer Standard per tutti i carichi di lavoro di produzione. Implementare i gruppi di sicurezza di rete e consentire l'accesso solo alle porte e agli intervalli di indirizzi IP attendibili dell'applicazione. Nei casi in cui non è assegnato alcun gruppo di sicurezza di rete alla subnet back-end o alla scheda di interfaccia di rete delle macchine virtuali back-end, il traffico non sarà consentito a queste risorse dal servizio di bilanciamento del carico. Con Load Balancer Standard, fornire regole in uscita per definire NAT in uscita con un gruppo di sicurezza di rete. Esaminare queste regole in uscita per ottimizzare il comportamento delle connessioni in uscita.

L'uso di un Load Balancer Standard è consigliato per i carichi di lavoro di produzione e in genere basic Load Balancer viene usato solo per i test perché il tipo di base è aperto alle connessioni da Internet per impostazione predefinita e non richiede gruppi di sicurezza di rete per il funzionamento.

- [Connessioni in uscita in Azure](load-balancer-outbound-connections.md)

- [Aggiornare i servizi pubblici di Azure Load Balancer](upgrade-basic-standard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** il Load Balancer è un servizio pass-through perché si basa sulle regole dei gruppi di sicurezza di rete applicate alle risorse back-end e sulle regole in uscita configurate per controllare l'accesso a Internet.

Esaminare le regole in uscita configurate per il Load Balancer Standard tramite il pannello Regole in uscita del Load Balancer e il pannello Regole di bilanciamento del carico in cui è possibile che siano abilitate le regole in uscita implicite.

Monitorare il conteggio delle connessioni in uscita per tenere traccia della frequenza con cui le risorse raggiungono Internet. 

Usare il Centro sicurezza e seguire le raccomandazioni sulla protezione della rete per proteggere le risorse di rete di Azure.

Seguire le raccomandazioni sulla sicurezza per le risorse back-end, abilitare i log dei flussi dei gruppi di sicurezza di rete e inviare i log a un account Archiviazione di Azure per il controllo.

Inviare anche i log dei flussi a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure. I vantaggi Analisi del traffico includono la possibilità di visualizzare le attività di rete, identificare gli hot spot e le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare errori di configurazione della rete.

- [Come abilitare i log di flusso del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Ricerca per categorie le statistiche di connessione in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee** guida: definire in modo esplicito la connettività Internet e gli indirizzi IP di origine validi tramite regole in uscita e gruppi di sicurezza di rete con l'Load Balancer per usare l'intelligence sulle minacce di Microsoft per la protezione delle applicazioni Web.

- [Integrare il Firewall di Azure](../firewall/integrate-lb.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare la protezione Standard DDoS (Distributed Denial of Service) di Azure nella rete virtuale di Azure per la protezione dagli attacchi DDoS. 

Distribuire Firewall di Azure in ogni limite di rete dell'organizzazione con il filtro basato su intelligence sulle minacce abilitato e configurato su "Avviso e rifiuto" per il traffico di rete dannoso.

 

Usare la protezione dalle minacce del Centro sicurezza per rilevare le comunicazioni con indirizzi IP dannosi noti. 

Il Load Balancer Standard è progettato per essere sicuro per impostazione predefinita e parte di una rete virtuale privata e isolata. Viene chiusa ai flussi in ingresso a meno che non venga aperta dai gruppi di sicurezza di rete per consentire in modo esplicito il traffico consentito e per non consentire indirizzi IP dannosi noti. A meno che non esista un gruppo di sicurezza di rete in una subnet o una scheda di interfaccia di rete della risorsa macchina virtuale dietro il Load Balancer, il traffico non è autorizzato a raggiungere questa risorsa. 

Distribuire Firewall di Azure in ogni limite di rete dell'organizzazione con il filtro basato sull'intelligence per le minacce abilitato e configurato su "Avvisa e nega" per il traffico di rete dannoso per impedire attacchi da indirizzi IP dannosi. 

Implementare linee guida per integrare Firewall di Azure con il Load Balancer.

Usare le funzionalità di protezione dalle minacce del Centro sicurezza per rilevare le comunicazioni con indirizzi IP dannosi noti. 

Il Centro sicurezza (livello Standard) fornisce l'accesso just-in-time alle macchine virtuali e configura gli indirizzi IP di origine consentiti per consentire l'accesso solo da indirizzi/intervalli IP approvati.
 

Usare la funzionalità protezione avanzata adattiva della rete del Centro sicurezza per consigliare le configurazioni dei gruppi di sicurezza di rete che limitano le porte e gli IP di origine in base al traffico effettivo e all'intelligence sulle minacce.
 

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md)

- [Filtro basato sull'intelligence sulle minacce del firewall di Azure](../firewall/threat-intel.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Proteggere le porte di gestione con l'accesso JIT](../security-center/security-center-just-in-time.md)

- [Protezione avanzata adattiva della rete in Centro sicurezza di Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Integrare Firewall di Azure con il Load Balancer](../firewall/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** abilitare Network Watcher'acquisizione di pacchetti per analizzare le attività anomae.

- [Come creare un'Network Watcher predefinita](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee** guida: implementare un'offerta dal Azure Marketplace che supporta la funzionalità IDS/IPS con funzionalità di ispezione del payload per l'ambiente del Load Balancer. 

Usare Firewall di Azure intelligence sulle minacce se l'ispezione del payload non è un requisito. Firewall di Azure filtro basato sull'intelligence sulle minacce viene usato per avvisare e/o bloccare il traffico da e verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall preferita in ogni limite di rete dell'organizzazione per rilevare e/o bloccare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee** guida: definire in modo esplicito la connettività Internet e gli indirizzi IP di origine validi tramite regole in uscita e gruppi di sicurezza di rete con l'Load Balancer per usare le funzionalità di Intelligence per le minacce di Microsoft per proteggere le applicazioni Web.

- [Integrare il Firewall di Azure](../firewall/integrate-lb.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag di servizio al posto di indirizzi IP specifici durante la creazione di regole di sicurezza. Specificare il nome del tag di servizio nel campo di origine o di destinazione di una regola per consentire o negare il traffico per il servizio corrispondente. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

Per impostazione predefinita, ogni gruppo di sicurezza di rete include il tag del servizio AzureLoadBalancer per consentire il traffico del probe di integrità. 

Vedere la documentazione di Azure per tutti i tag di servizio disponibili per l'uso nelle regole del gruppo di sicurezza di rete.

- [Tag del servizio disponibili](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le risorse di rete con Criteri di Azure.

Usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resources Manager, controlli del controllo degli accessi in base al ruolo di Azure e criteri, in un'unica definizione di progetto. 

Applicare il progetto alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure esempi per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. 

Usare il campo "Descrizione" per documentare le regole che consentono il traffico da e verso una rete per le regole dei singoli gruppi di sicurezza di rete.

Implementare una delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore", che garantisce che tutte le risorse siano create con tag e per notificare eventuali risorse senza tag esistenti.

Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md)

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di Azure. 

Creare avvisi in Monitoraggio di Azure per ricevere una notifica quando vengono modificate le risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** esaminare le modifiche apportate alle regole in uscita e ai gruppi di sicurezza di rete per i servizi di bilanciamento del carico visualizzando il log attività nelle sottoscrizioni. 

Visualizzare i log dell'host interno per assicurarsi che le risorse back-end siano sicure.

Esportare questi log in Log Analytics o in un'altra piattaforma di archiviazione. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Abilitare e eseguire l'on board di questi dati Azure Sentinel o un SIEM di terze parti in base ai requisiti aziendali dell'organizzazione.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Come raccogliere i log host interni della macchina virtuale di Azure con Monitoraggio di Azure](../azure-monitor/vm/quick-collect-azurevm.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Log attività della piattaforma](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee** guida: esaminare le informazioni di controllo e registrazione del piano di gestione acquisite con i log attività per l'Load Balancer. Queste impostazioni di acquisizione sono abilitate per impostazione predefinita. 

Usare i log attività per monitorare le azioni sulle risorse per visualizzare tutte le attività e il relativo stato. 

Determinare le operazioni eseguite sulle risorse nella sottoscrizione con i log attività: 

- chi ha avviato l'operazione
- quando si è verificata l'operazione;
- lo stato dell'operazione;

- i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Recuperare informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o l'portale di Azure. 

Implementare la diagnostica multidimensionale per le Load Balancer Standard di configurazione con Monitoraggio di Azure.  Tra le metriche disponibili per la sicurezza sono incluse le informazioni sulle connessioni SNAT (Source Network Address Translation), sulle porte. Sono disponibili anche metriche aggiuntive sui pacchetti SYN (sincronizzazione) e sui contatori dei pacchetti. 

Recuperare le metriche multidimensionali a livello di codice tramite le API e scriverle in un account di archiviazione tramite l'opzione "Tutte le metriche".

Usare il pacchetto di contenuto Log di controllo di Azure con Microsoft Power BI per analizzare i dati con dashboard preconfigurato o per personalizzare le visualizzazioni in base alle esigenze.

Trasmettere i log a un hub eventi o a un'area di lavoro Log Analytics. Possono anche essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI. 

Abilitare e eseguire l'on board dei dati Azure Sentinel o un SIEM di terze parti in base ai requisiti aziendali.

- [Esaminare questo articolo con istruzioni dettagliate per ogni metodo descritto in Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Log di Monitoraggio di Azure per Load Balancer Basic pubblico](load-balancer-monitor-log.md)

- [Visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md)

- [Recuperare le metriche multidimensionali a livello di codice tramite le API](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** il log attività è abilitato per impostazione predefinita e viene mantenuto per 90 giorni nell'archivio dei log eventi di Azure. Impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione in Monitoraggio di Azure. Usare Archiviazione di Azure account per l'archiviazione a lungo termine e di archiviazione.

- [Visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md)

- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log Archiviazione di Azure account](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Indicazioni:** monitorare, gestire e risolvere i problemi delle risorse Load Balancer Standard usando la pagina Load Balancer nel portale di Azure e la pagina Integrità risorse in Monitoraggio di Azure. Le metriche disponibili per la sicurezza includono informazioni sulle connessioni SNAT (Source Network Address Translation), sulle porte. Sono inoltre disponibili metriche per i pacchetti SYN (sincronizzazione) e i contatori dei pacchetti. 

Usare Monitoraggio di Azure per esaminare lo stato del probe di integrità degli endpoint con metriche multidimensionali per i servizi di bilanciamento del carico Standard, esterni e interni. Raccogliere queste metriche a livello di codice tramite le API e scrivere in un account di archiviazione tramite l'opzione "Tutte le metriche".

Monitoraggio di Azure non sono disponibili per i servizi load balancer Basic interni. 

Usare Monitoraggio di Azure per visualizzare lo stato del probe di integrità riepilogato per ogni pool back-end per il Load Balancer esterno di base, ad esempio il numero di istanze nel pool back-end che non ricevono richieste dal Load Balancer a causa di errori del probe di integrità. 

Implementare la registrazione con Azure Operational Insights per fornire statistiche sullo stato di integrità del servizio di bilanciamento del carico. 

Usare i log attività per visualizzare gli avvisi e monitorare le azioni sulle risorse e il relativo stato nelle sottoscrizioni di Azure. I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure. 

Usare Microsoft Power BI con il pacchetto di contenuto Log di controllo di Azure e analizzare i dati con dashboard preconfigurato. Personalizzare le visualizzazioni in base alle esigenze aziendali. 

Trasmettere i log a un hub eventi o a un'area di lavoro Log Analytics. Possono anche essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI. È possibile abilitare e eseguire l'on board dei dati Azure Sentinel o un SIEM di terze parti.

- [Probe di integrità di Load Balancer](load-balancer-custom-probe-overview.md)

- [API REST di Monitoraggio di Azure](/rest/api/monitor)

- [Come recuperare le metriche tramite l'API REST](/rest/api/monitor/metrics/list)

- [Load Balancer Standard diagnostica con metriche, avvisi e integrità delle risorse](load-balancer-standard-diagnostics.md)

- [Log di Monitoraggio di Azure per Load Balancer Basic pubblico](load-balancer-monitor-log.md)

- [Consente di visualizzare le metriche di Load Balancer nel portale di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare il Centro sicurezza con l'area di lavoro Log Analytics per il monitoraggio e l'avviso sulle attività anoma Load Balancer nei log di sicurezza e negli eventi.

Abilitare e eseguire l'on board dei dati Azure Sentinel uno strumento SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come avvisare i dati del log di Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure, ad esempio Load Balancer tramite assegnazioni di ruolo. Assegnare questi ruoli a utenti, entità servizio dei gruppi e identità gestite.

Inventario Ruoli predefiniti e predefiniti per determinate risorse con strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o portale di Azure.

- [Come ottenere un ruolo di directory in Azure Active Directory (Azure AD) con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee** guida: abilitare Azure Active Directory (Azure AD) a più fattori e seguire le raccomandazioni di Gestione identità e accesso del Centro sicurezza.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare workstation con accesso con privilegi (PAW) con l'autenticazione a più fattori configurata per gestire e accedere alle risorse di rete di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](/security/compass/privileged-access-devices)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gestire le risorse di Azure solo da località approvate

**Indicazioni:** usare le località denominate per l'accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per i servizi. Azure AD protegge i dati usando la crittografia avanzata per i dati in transito e in transito, oltre a salti, hash e archivia in modo sicuro le credenziali utente.  

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** usare Azure Active Directory (Azure AD) per fornire log che consentono di individuare gli account non obsoleti. 

Le verifiche di accesso delle identità di Azure possono essere eseguite per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente deve essere verificato regolarmente per assicurarsi che solo gli utenti attivi abbia accesso continuo.

- [Informazioni sulla creazione Azure AD report](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** integrare le Azure Active Directory(Azure AD) del registro eventi di accesso, di controllo e di rischio con qualsiasi strumento di monitoraggio o informazioni di sicurezza e gestione degli eventi in base all'accesso.

Semplificare questo processo creando impostazioni di diagnostica per Azure AD utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Gli avvisi desiderati possono essere configurati all'interno dell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni:** usare Azure Active Directory funzionalità Di rischio e Identity Protection di Azure AD (Azure AD) per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inserire i dati in Azure Sentinel ulteriori indagini.

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1.2 o versione successiva.

Seguire Centro sicurezza di Azure per la crittografia dei dati in pausa e la crittografia in transito, se applicabile.

- [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso Load Balancer risorse.

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni:** Load Balancer è un servizio pass-through che non archivia i dati dei clienti. Fa parte della piattaforma sottostante gestita da Microsoft. 

Microsoft considera tutti i contenuti dei clienti come sensibili e fa molta distinzione per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. 

Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi quando vengono apportate modifiche alle risorse critiche di Azure, ad esempio i servizi di bilanciamento del carico usati per carichi di lavoro di produzione importanti.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per cercare e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) nelle sottoscrizioni. Azure Resource Manager è consigliabile creare e usare le risorse correnti.

Assicurarsi le autorizzazioni appropriate (lettura) nel tenant ed enumerare tutte le sottoscrizioni e le risorse di Azure nelle sottoscrizioni.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee** guida: applicare tag alle risorse di Azure con metadati per organizzare in modo logico in base a una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia degli asset. 

Riconciliare regolarmente l'inventario e assicurarsi che le risorse non autorizzate siano eliminate dalle sottoscrizioni in modo corretto.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

**Linee** guida: creare un elenco di risorse di Azure approvate in base alle esigenze dell'organizzazione che è possibile sfruttare come meccanismo di elenco consentiti. In questo modo l'organizzazione può eseguire l'onboarded di tutti i nuovi servizi di Azure disponibili dopo che sono stati esaminati e approvati formalmente dai processi di valutazione della sicurezza tipici dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Eseguire query e individuare le risorse con Azure Resource Graph all'interno delle sottoscrizioni di proprietà. 

Assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Esempi di criteri di Azure predefiniti per la rete virtuale](/azure/virtual-network/policy-samples)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare l'accesso condizionale Azure Active Directory (Azure AD) per limitare la capacità degli utenti di interagire con il Azure Resource Manager configurando "Blocca l'accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni:** il software necessario per le operazioni aziendali, ma che può causare rischi più elevati per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o rete virtuale e sufficientemente protetto con un Firewall di Azure o un gruppo di sicurezza di rete.

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias personalizzati per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure. Usare definizioni di Criteri di Azure incorporate.

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

Esportare Azure Resource Manager modelli in JavaScript Object Notation (JSON) ed esaminarli periodicamente per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza dell'organizzazione.

Implementare raccomandazioni dal Centro sicurezza come baseline di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.  È anche possibile usare modelli Azure Resource Manager per mantenere la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio definizioni Criteri di Azure personalizzate, modelli Azure Resource Manager e script di configurazione dello stato desiderati.

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se è integrato con Azure DevOps o in Azure AD se integrato con TFS.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando Criteri di Azure.  Usare Criteri di Azure alias personalizzati per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. Implementare definizioni di criteri incorporate correlate alle risorse Azure Load Balancer specifiche.  Usare anche Automazione di Azure per distribuire le modifiche di configurazione. alle risorse Azure Load Balancer di lavoro.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni:** usare il Centro sicurezza per eseguire analisi di base per le risorse di Azure e Criteri di Azure per avvisare e controllare le configurazioni delle risorse.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto. 

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Il cliente può anche sfruttare la Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. 

La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o nell'analisi usata per l'emissione dell'avviso, nonché sul livello di attendibilità che si è verificato un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili.  

È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni:** eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e quindi rivedere il piano di risposta in base alle esigenze. 

- [Pubblicazione di NIST: Guida ai programmi di test, formazione ed esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni:** esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. 

Usare la funzionalità di esportazione continua nel Centro sicurezza che consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. 

Usare il connettore dati del Centro sicurezza per trasmettere gli avvisi Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità Automazione del flusso di lavoro nel Centro sicurezza per attivare automaticamente le risposte agli avvisi di sicurezza e alle raccomandazioni per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro in Security enter](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Linee** guida: seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
