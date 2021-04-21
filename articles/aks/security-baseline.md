---
title: Baseline di sicurezza di Azure per servizio Azure Kubernetes
description: La servizio Azure Kubernetes di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769590"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Baseline di sicurezza di Azure per servizio Azure Kubernetes

Questa baseline di sicurezza applica le linee guida di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) ad Azure Kubernetes. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili ad Azure Kubernetes. **Sono** stati esclusi i controlli non applicabili ad Azure Kubernetes o per i quali la responsabilità è di Microsoft.

Per informazioni sul mapping completo di Azure Kubernetes a Azure Security Benchmark, vedere il file di mapping completo della baseline di sicurezza di [Azure Kubernetes.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Linee** guida: per impostazione predefinita, vengono creati automaticamente un gruppo di sicurezza di rete e una tabella di route con la creazione di un cluster del servizio Kubernetes Microsoft Azure. Il servizio AzureKs modifica automaticamente i gruppi di sicurezza di rete per il flusso di traffico appropriato quando i servizi vengono creati con servizi di bilanciamento del carico, mapping delle porte o route in ingresso. Il gruppo di sicurezza di rete viene associato automaticamente alle schede di interfaccia di rete virtuali nei nodi del cliente e alla tabella di route con la subnet nella rete virtuale. 

Usare i criteri di rete del servizio AKS per limitare il traffico di rete definendo regole per il traffico in ingresso e in uscita tra pod Linux in un cluster in base alla scelta di spazi dei nomi e selettori di etichetta. L'utilizzo dei criteri di rete richiede Azure CNI plug-in con reti virtuali e subnet definite e può essere abilitato solo durante la creazione del cluster. Non possono essere distribuiti in un cluster del servizio Web del servizio Web di Microsoft Windows esistente.

È possibile implementare un cluster del servizio AKS privato per garantire che il traffico di rete tra il server API del servizio Gateway Gateway e i pool di nodi rimanga solo nella rete privata. Il piano di controllo o il server API risiede in una sottoscrizione di Azure gestita dal servizio Azure Sdk e usa indirizzi IP interni (RFC1918), mentre il cluster o il pool di nodi del cliente si trova nella propria sottoscrizione. Il server e il cluster o il pool di nodi comunicano tra loro usando il servizio collegamento privato di Azure nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster del servizio AKS del cliente.  In alternativa, usare un endpoint pubblico per il server DELL'API del servizio Web Diaks, ma limitare l'accesso con la funzionalità Degli intervalli IP autorizzati del server API del servizio Web Diaks. 

- [Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes](concepts-security.md)

- [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes](use-network-policies.md)

- [Creare un cluster privato del servizio Azure Kubernetes](private-clusters.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza e seguire le raccomandazioni sulla protezione di rete per proteggere le risorse di rete usate dai cluster servizio Azure Kubernetes (AKS). 

Abilitare i log dei flussi dei gruppi di sicurezza di rete e inviare i log a un account Archiviazione di Azure per il controllo. È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure per visualizzare l'attività di rete, identificare aree sensibili e minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare gli errori di configurazione della rete.

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare i log dei flussi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee** guida: usare un web application firewall (WAF) abilitato per gateway applicazione di Azure di fronte a un cluster del servizio Web del servizio Web per fornire un ulteriore livello di sicurezza filtrando il traffico in ingresso verso le applicazioni Web. Azure WAF usa un set di regole, fornite da The Open Web Application Security Project (OWASP), per attacchi, ad esempio, cross-site scripting o cookie poisoning su questo traffico. 

Usare un gateway API per l'autenticazione, l'autorizzazione, la limitazione, la memorizzazione nella cache, la trasformazione e il monitoraggio per le API usate nell'ambiente del servizio Web AKS. Un gateway API funge da front door per i microservizi, disaccoglie i client dai microservizi e riduce la complessità dei microservizi rimuovendo il carico di gestione delle problematiche trasversali.

- [Informazioni sulle procedure consigliate per la connettività di rete e la sicurezza nel servizio Web Diaks](operator-best-practices-network.md)

- [Controller in ingresso del gateway applicazione ](../application-gateway/ingress-controller-overview.md)

- [Usare Azure API Management con microservizi distribuiti in servizio Azure Kubernetes](../api-management/api-management-kubernetes.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee** guida: Abilitare la protezione Standard DDoS (Distributed Denial of Service) Microsoft nelle reti virtuali in cui vengono distribuiti i componenti servizio Azure Kubernetes (AKS) per le protezioni dagli attacchi DDoS.

Installare il motore dei criteri di rete e creare criteri di rete Kubernetes per controllare il flusso di traffico tra i pod nel servizio App Kubernetes perché, per impostazione predefinita, tutto il traffico è consentito tra questi pod. I criteri di rete devono essere usati solo per pod e nodi basati su Linux nel servizio Azure Kubernetes. Definire regole che limitano la comunicazione dei pod per migliorare la sicurezza. 

Scegliere di consentire o negare il traffico in base a impostazioni quali etichette assegnate, spazio dei nomi o porta del traffico. I criteri di rete necessari possono essere applicati automaticamente quando i pod vengono creati dinamicamente in un cluster del servizio AppKs. 

- [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes](use-network-policies.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Linee** guida: usare Network Watcher di pacchetti come richiesto per l'analisi dell'attività anomaa. 

Network Watcher viene abilitata automaticamente nell'area della rete virtuale quando si crea o si aggiorna una rete virtuale nella sottoscrizione. È anche possibile creare nuove istanze di Network Watcher usando PowerShell, l'interfaccia della riga di comando di Azure, l'API REST o il Azure Resource Manager Client

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** proteggere il cluster servizio Azure Kubernetes (AKS) con un gateway applicazione di Azure abilitato con un Web application firewall (WAF). 

Se il rilevamento e/o la prevenzione delle intrusioni in base all'analisi del comportamento o all'ispezione del payload non è un requisito, è possibile usare e configurare un gateway applicazione di Azure con WAF in "modalità di rilevamento" per registrare avvisi e minacce o "modalità di prevenzione" per bloccare attivamente le intrusioni e gli attacchi rilevati.

- [Informazioni sulle procedure consigliate per la protezione del cluster del servizio Web Diaks con un WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Come distribuire gateway applicazione di Azure (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete associati alle istanze servizio Azure Kubernetes (AKS). I tag di servizio possono essere usati al posto di indirizzi IP specifici durante la creazione di regole di sicurezza per consentire o negare il traffico per il servizio corrispondente specificando il nome del tag del servizio. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Applicare un tag di Azure ai pool di nodi nel cluster del servizio AzureKs. Sono diversi rispetto ai tag del servizio di rete virtuale e vengono applicati a ogni nodo all'interno del pool di nodi e vengono mantenuti durante gli aggiornamenti. 

- [Comprendere e usare i tag del servizio](../virtual-network/service-tags-overview.md)

- [Informazioni su NSG per il servizio Servizio DinK](support-policies.md)

- [Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes](limit-egress-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard con Criteri di Azure per le risorse di rete associate ai cluster servizio Azure Kubernetes (AKS). 

Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.ContainerService" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei cluster del servizio Servizio App. 

Usare anche le definizioni dei criteri predefiniti correlate al server del supporto di Accesso remoto, ad esempio:

- Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes

- Imponi traffico HTTPS in ingresso nel cluster Kubernetes

- Verifica che i servizi siano in ascolto solo sulle porte consentite nel cluster Kubernetes

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure esempi per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee** guida: usare i tag per i gruppi di sicurezza di rete e altre risorse per il flusso di traffico da e verso i cluster servizio Azure Kubernetes (AKS). Usare il campo "Descrizione" per le singole regole del gruppo di sicurezza di rete per specificare le esigenze aziendali e/o la durata e così via per tutte le regole che consentono il traffico da e verso una rete.

Usare una qualsiasi delle definizioni incorporate Criteri di Azure relative all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" che garantisca che tutte le risorse siano create con tag e ricevano notifiche per le risorse senza tag esistenti.

Scegliere di consentire o negare percorsi di rete specifici all'interno del cluster in base agli spazi dei nomi e ai selettori di etichetta con criteri di rete. Usare questi spazi dei nomi ed etichette come descrittori per le regole di configurazione del traffico. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Criteri di Azure con l'interfaccia della riga di comando](/cli/azure/policy)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai cluster servizio Azure Kubernetes (servizio AzureKs). 

In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche. Tutte le voci dell'utente AzureContainerService nei log attività vengono registrate come azioni della piattaforma. 

Usare Monitoraggio di Azure log per abilitare ed eseguire query nei log dei componenti master, kube-apiserver e kube-controller-manager. Creare e gestire i nodi che eseguono il kubelet con il runtime del contenitore e distribuire le applicazioni tramite il server API Kubernetes gestito. 

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes](/azure/aks/view-master-logs)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni:** i nodi servizio Azure Kubernetes (AKS) usano ntp.ubuntu.com per la sincronizzazione dell'ora, insieme alla porta UDP 123 e al protocollo NTP (Network Time Protocol). 

Assicurarsi che i server NTP siano accessibili dai nodi del cluster se si usano server DNS personalizzati. 

- [Informazioni sui requisiti del dominio e della porta NTP per i nodi del cluster del servizio Web Diaks](limit-egress-traffic.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** abilitare i log di controllo dai componenti master del servizio Azure Kubernetes, kube-apiserver e kube-controller-manager, forniti come servizio gestito. 

- kube-auditaksService: nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) 

- masterclient: nome visualizzato nel log di controllo per MasterClientCertificate, il certificato che si ottiene da az aks get-credentials 

- nodeclient: nome visualizzato per ClientCertificate, usato dai nodi agente

Abilitare anche altri log di controllo, ad esempio kube-audit. 

Esportare questi log in Log Analytics o in un'altra piattaforma di archiviazione. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine e l'archiviazione.

Abilitare e eseguire l'on board di questi dati Azure Sentinel o un SIEM di terze parti in base ai requisiti aziendali dell'organizzazione.

- [Esaminare lo schema del log, inclusi i ruoli di log, qui](/azure/aks/view-master-logs)

- [Informazioni Monitoraggio di Azure per i contenitori](/azure/azure-monitor/insights/container-insights-overview)

- [Come abilitare la Monitoraggio di Azure per i contenitori](/azure/azure-monitor/insights/container-insights-onboard)

- [Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes](/azure/aks/view-master-logs)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** usare i log attività per monitorare le azioni sulle risorse servizio Azure Kubernetes (AKS) per visualizzare tutte le attività e il relativo stato. Determinare le operazioni eseguite sulle risorse nella sottoscrizione con i log attività: 

- chi ha avviato l'operazione
- quando si è verificata l'operazione;
- lo stato dell'operazione;
- i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Recuperare informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o l'portale di Azure. 

Abilitare i log di controllo nei componenti master del server del supporto di AKS, ad esempio:Enable audit logs on AKS master components, such as: 

- kube-auditaksService: nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) 

- masterclient: il nome visualizzato nel log di controllo per MasterClientCertificate, il certificato che si ottiene da az aks get-credentials 

- nodeclient: nome visualizzato per ClientCertificate, usato dai nodi dell'agente

Attivare anche altri log di controllo, ad esempio kube-audit. 

- [Come abilitare ed esaminare i log del nodo master Kubernetes nel servizio Kubernetes](/azure/aks/view-master-logs)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee** guida: abilitare l'installazione automatica degli agenti di Log Analytics per la raccolta di dati dai nodi del cluster del servizio Web Diaks. Inoltre, attivare il provisioning automatico dell'agente di monitoraggio di Azure Log Analytics Centro sicurezza di Azure, come per impostazione predefinita, il provisioning automatico è disattivato. L'agente può anche essere installato manualmente. Con il provisioning automatico, il Centro sicurezza distribuisce l'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e in quelle nuove create. Il Centro sicurezza raccoglie dati da macchine virtuali di Azure, set di scalabilità di macchine virtuali e contenitori IaaS, ad esempio nodi del cluster Kubernetes, per monitorare le vulnerabilità e le minacce per la sicurezza. I dati vengono raccolti usando l'agente di Azure Log Analytics, che legge varie configurazioni e log eventi correlati alla sicurezza dal computer e copia i dati nell'area di lavoro per l'analisi. 

La raccolta dei dati è necessaria per fornire visibilità su aggiornamenti mancanti, impostazioni di sicurezza del sistema operativo non configurate correttamente, stato di endpoint protection e rilevamenti di integrità e minacce.

- [Come abilitare il provisioning automatico dell'agente di Log Analytics](../security-center/security-center-enable-data-collection.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee** guida: eseguire l'onboardimento delle istanze di servizio Azure Kubernetes (AKS) Monitoraggio di Azure e impostare il periodo di conservazione dell'area di lavoro Azure Log Analytics corrispondente in base ai requisiti di conformità dell'organizzazione. 

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Indicazioni:** eseguire l'onboarding servizio Azure Kubernetes istanze del servizio Monitoraggio di Azure e configurare le impostazioni di diagnostica per il cluster. 

Usare Monitoraggio di Azure'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log. Monitoraggio di Azure sono abilitati e gestiti nel portale di Azure o tramite l'interfaccia della riga di comando e funzionano con il controllo degli accessi in base al ruolo di Kubernetes, il controllo degli accessi in base al ruolo di Azure e i cluster del servizio Azure Kubernetes non abilitati per il controllo degli accessi in base al ruolo.

Visualizzare i log generati dai componenti master del servizio Ku kube (kube-apiserver e kube-controllermanager) per la risoluzione dei problemi dell'applicazione e dei servizi. Abilitare e eseguire l'on board dei dati Azure Sentinel o un SIEM di terze parti per la gestione e il monitoraggio centralizzati dei log.

- [Come abilitare ed esaminare i log del nodo master Kubernetes nel servizio Kubernetes](/azure/aks/view-master-logs)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** usare il servizio Azure Kubernetes (AKS) insieme al Centro sicurezza per ottenere una maggiore visibilità sui nodi del servizio Web AKS. 

Esaminare gli avvisi del Centro sicurezza sulle minacce e sulle attività dannose rilevate a livello di host e cluster. Il Centro sicurezza implementa l'analisi continua degli eventi di sicurezza non elaborati che si verificano in un cluster del servizio Kubernetes, ad esempio i dati di rete, la creazione di processi e il log di controllo di Kubernetes. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente. Usare le metriche e i log Monitoraggio di Azure creare un'istanza dei risultati. 

- [Informazioni sull'integrazione dei servizi Azure Kubernetes con il Centro sicurezza](../security-center/defender-for-kubernetes-introduction.md)

- [Come abilitare il Centro sicurezza di Azure Standard](../security-center/security-center-get-started.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni:** installare e abilitare l'antimalware Microsoft per Azure per servizio Azure Kubernetes macchine virtuali e i nodi del set di scalabilità di macchine virtuali. Esaminare gli avvisi nel Centro sicurezza per la correzione.

- [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](../security/fundamentals/antimalware.md)

- [Guida di riferimento per gli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori - servizio Azure Kubernetes cluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee** guida: servizio Azure Kubernetes (AKS) usa il progetto CoreDNS per la gestione e la risoluzione DNS del cluster.

Abilitare la registrazione delle query DNS applicando la configurazione documentata in Coredns-custom ConfigMap. 

- [Personalizzare CoreDNS con il servizio Azure Kubernetes](coredns-custom.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee** guida: usare kubectl, un client della riga di comando, in servizio Azure Kubernetes (AKS) per gestire un cluster Kubernetes e ottenere i relativi log dal nodo del servizio Kubernetes per la risoluzione dei problemi. Kubectl è già installato se si usa Azure Cloud Shell. Per installare kubectl in locale, usare il cmdlet 'Install-AzAksKubectl'.

- [Guida introduttiva- Distribuire un cluster servizio Azure Kubernetes con PowerShell](kubernetes-walkthrough-powershell.md)

- [Ottenere i log di kubelet dai nodi del cluster del servizio Azure Kubernetes](kubelet-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee** guida: servizio Azure Kubernetes non fornisce una soluzione di gestione delle identità che archivia gli account utente e le password normali. Con Azure Active Directory (Azure AD), è possibile concedere a utenti o gruppi l'accesso alle risorse Kubernetes all'interno di uno spazio dei nomi o all'interno del cluster.

Eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi del servizio Web AKS con il modulo Azure AD PowerShell

Usare l'interfaccia della riga di comando di Azure per operazioni come "Ottenere le credenziali di accesso per un cluster Kubernetes gestito" per facilitare la riconciliazione dell'accesso a intervalli regolari. Implementare questo processo per mantenere un inventario aggiornato degli account del servizio, che sono un altro tipo di utente primario nel servizio Web AKS. Applicare le raccomandazioni di Gestione identità e accesso del Centro sicurezza.

- [Come integrare il Azure AD](azure-ad-integration-cli.md)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Come monitorare l'identità e l'accesso con Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** servizio Azure Kubernetes (AKS) non ha il concetto di password predefinite comuni e non offre una soluzione di gestione delle identità in cui archiviare gli account utente e le password normali. Con Azure Active Directory (Azure AD) è possibile concedere l'accesso in base al ruolo alle risorse del servizio Web Disassocato all'interno di uno spazio dei nomi o all'interno del cluster. 

Eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi del servizio Web Disatteso con il Azure AD PowerShell

- [Informazioni sull'accesso e le opzioni di identità per il servizio Servizio Web di gestione degli accessi](concepts-identity.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** integrare l'autenticazione utente per i cluster servizio Azure Kubernetes (AKS) con Azure Active Directory (Azure AD). Accedere a un cluster del servizio Web Diaks usando un token Azure AD di autenticazione. Configurare il controllo degli accessi in base al ruolo di Kubernetes per l'accesso amministrativo alle informazioni e alle autorizzazioni di configurazione di Kubernetes (kubeconfig), agli spazi dei nomi e alle risorse del cluster. 

Creare criteri e procedure per l'uso di account amministrativi dedicati. Implementare le raccomandazioni di Gestione delle identità e degli accessi del Centro sicurezza.

- [Come monitorare l'identità e l'accesso con Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Controllare l'accesso alle risorse del cluster](azure-ad-rbac.md)

- [Usare i controlli degli accessi in base al ruolo di Azure](control-kubeconfig-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni:** usare l'accesso Single Sign-On per servizio Azure Kubernetes (AKS) con l'autenticazione integrata Azure Active Directory (Azure AD) per un cluster del servizio Servizio Azure Kubernetes.

- [Come visualizzare log, eventi e metriche dei pod di Kubernetes in tempo reale](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** integrare l'autenticazione servizio Azure Kubernetes (AKS) con Azure Active Directory (Azure AD). 

Abilitare Azure AD'autenticazione a più fattori e seguire le raccomandazioni di Gestione delle identità e degli accessi del Centro sicurezza.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare una workstation PAW (Privileged Access Workstation), con Multi-Factor Authentication (MFA), configurata per accedere ai cluster servizio Azure Kubernetes (AKS) specificati e alle risorse correlate.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare Multi-Factor Authentication (MFA) in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory (Azure AD) con l'autenticazione integrata Azure AD per servizio Azure Kubernetes (AKS). Gli avvisi possono essere generati quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza per monitorare l'identità e l'attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e l'accesso degli utenti in Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Indicazioni:** usare percorsi denominati di accesso condizionale per consentire l'accesso ai cluster servizio Azure Kubernetes (AKS) solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche. Questa operazione richiede l'autenticazione integrata per il servizio Azure Active Directory (Azure AD).

Limitare l'accesso al server API del servizio Gateway Gateway da un set limitato di intervalli di indirizzi IP, in quanto riceve le richieste di eseguire azioni nel cluster per creare risorse o ridimensionare il numero di nodi. 

- [Proteggere l'accesso al server API usando intervalli di indirizzi IP autorizzati in servizio Azure Kubernetes (AKS)](api-server-authorized-ip-ranges.md)

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per servizio Azure Kubernetes (AKS). Azure AD protegge i dati usando la crittografia avanzata per i dati in pausa e in transito e salt, hash e archivia in modo sicuro le credenziali utente.

Usare i ruoli predefiniti del servizio Azure Kubernetes con controllo degli accessi in base al ruolo di Azure - Collaboratore ai criteri delle risorse e Proprietario per le operazioni di assegnazione dei criteri al cluster Kubernetes

- [Panoramica dei criteri di Azure](../governance/policy/overview.md)

- [Come integrare il Azure AD con il Azure AD di Controllo di accesso](azure-ad-integration-cli.md)

- [Integrare i servizi gestiti dal servizio Azure AD](managed-aad.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** usare i report Azure Active Directory (Azure AD) con Azure AD'autenticazione integrata per servizio Azure Kubernetes (AKS). Cercare Azure AD log per individuare gli account non obsoleti. 

Eseguire verifiche di accesso alle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Correggere le raccomandazioni relative a identità e accesso dal Centro sicurezza.

Tenere presente i ruoli usati a scopo di supporto o risoluzione dei problemi. Ad esempio, tutte le azioni del cluster eseguite dal supporto tecnico Microsoft (con il consenso dell'utente) vengono eseguite con un ruolo predefinito di "modifica" di Kubernetes con il nome aks-support-rolebinding. Con questo ruolo è abilitato il supporto per il servizio AKS per modificare la configurazione e le risorse del cluster per risolvere e diagnosticare i problemi del cluster. Tuttavia, questo ruolo non può modificare le autorizzazioni né creare ruoli o associazioni di ruolo. Questo accesso al ruolo è abilitato solo in ticket di supporto attivi con accesso JIT (Just-In-Time).
 
- [Opzioni di accesso e identità per il servizio Azure Kubernetes](concepts-identity.md)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Come monitorare l'identità e l'attività di accesso dell'utente in Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** integrare l'autenticazione utente servizio Azure Kubernetes (AKS) con Azure Active Directory (Azure AD). Creare le impostazioni di diagnostica per Azure AD, inviando i log di controllo e di accesso a un'area di lavoro Azure Log Analytics. Configurare gli avvisi desiderati,ad esempio quando un account disattivato tenta di accedere, in un'area di lavoro Azure Log Analytics.
- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come creare, visualizzare e gestire gli avvisi di log usando Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee** guida: Integrare l'autenticazione utente servizio Azure Kubernetes (AKS) con Azure Active Directory (Azure AD). Usare Azure AD di Rilevamento dei rischi e Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inserire i dati in Azure Sentinel ulteriori indagini in base alle esigenze aziendali.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag nelle risorse correlate alle distribuzioni servizio Azure Kubernetes (AKS) per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Aggiornare i tag per i cluster gestiti](/rest/api/aks/managedclusters/updatetags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** isolare logicamente team e carichi di lavoro nello stesso cluster con servizio Azure Kubernetes (AKS) per fornire il minor numero di privilegi, con ambito per le risorse richieste da ogni team. 

Usare lo spazio dei nomi in Kubernetes per creare un limite di isolamento logico. Valutare la possibilità di implementare funzionalità Kubernetes aggiuntive per l'isolamento e la multi-tenancy, ad esempio pianificazione, rete, autenticazione/autorizzazione e contenitori.

Implementare sottoscrizioni e/o gruppi di gestione separati per ambienti di sviluppo, test e produzione. Separare i cluster del servizio AKS con la rete distribuendoli in reti virtuali distinte, contrassegnate in modo appropriato.

- [Informazioni sulle procedure consigliate per l'isolamento del cluster nel servizio AppKs](operator-best-practices-cluster-isolation.md)

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Informazioni sulle procedure consigliate per la connettività di rete e la sicurezza nel servizio Web Diaks](operator-best-practices-network.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: usare una soluzione di terze parti Azure Marketplace nei perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti inviando avvisi ai professionisti della sicurezza delle informazioni.

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Elenco delle porte, degli indirizzi e dei nomi di dominio necessari per la funzionalità del server del supporto di AKS](limit-egress-traffic.md)

- [Come configurare le impostazioni di diagnostica per Firewall di Azure](../firewall/firewall-diagnostics.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** creare un controller di ingresso HTTPS e usare i propri certificati TLS (o, facoltativamente, Let's Encrypt) per le distribuzioni di servizio Azure Kubernetes (AKS). 

Il traffico in uscita di Kubernetes viene crittografato tramite HTTPS/TLS per impostazione predefinita. Esaminare il traffico in uscita potenzialmente non crittografato dalle istanze del servizio Web Diaks per un monitoraggio aggiuntivo. Ad esempio, il traffico NTP, il traffico DNS e il traffico HTTP per il recupero degli aggiornamenti in alcuni casi. 

- [Come creare un controller di ingresso HTTPS nel servizio Web Diaks e usare i propri certificati TLS](ingress-own-tls.md)

- [Come creare un controller di ingresso HTTPS nel servizio Web Di seguito con Let's Encrypt](ingress-tls.md)

- [Elenco delle porte e dei protocolli in uscita potenziali usati dal componente AKS](limit-egress-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le Archiviazione di Azure o di calcolo. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.
Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. 

Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Indicazioni:** usare il sistema di autorizzazione del controllo degli accessi in base al ruolo di Azure basato su Azure Resource Manager per fornire una gestione degli accessi con granularità fine delle risorse di Azure.

Configurare servizio Azure Kubernetes per l'uso di Azure Active Directory (Azure AD) per l'autenticazione utente. Accedere a un cluster del servizio Servizio Web Di Azure AD token di autenticazione usando questa configurazione. 

Usare i ruoli predefiniti del servizio Azure Azure Con controllo degli accessi in base al ruolo di Azure - Collaboratore ai criteri delle risorse e Proprietario per le operazioni di assegnazione dei criteri al cluster del servizio AzureKs

- [Come controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo di Azure Azure AD identità nel servizio AzureKs](azure-ad-rbac.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee** guida: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per Archiviazione di Azure risorse di calcolo. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.
Microsoft gestisce la piattaforma sottostante e tratta tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** i due tipi principali di archiviazione disponibili per i volumi in servizio Azure Kubernetes (AKS) sono supportati da Dischi di Azure o File di Azure. Entrambi i tipi di archiviazione usano Archiviazione di Azure Service Encryption (SSE), che crittografa i dati in pausa per migliorare la sicurezza. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.

La crittografia in stato di inquisizione tramite chiavi gestite dal cliente è disponibile per crittografare sia il sistema operativo che i dischi dati nei cluster del servizio Web Dick per un controllo aggiuntivo sulle chiavi di crittografia. I clienti sono responsabili delle attività di gestione delle chiavi, ad esempio il backup e la rotazione delle chiavi. Attualmente i dischi non possono essere crittografati mediante Crittografia dischi di Azure a livello di nodo del servizio Azure Kubernetes.

- [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes](operator-best-practices-storage.md)

- [Bring Your Own Keys (BYOK) with Azure disks in servizio Azure Kubernetes (AKS)](azure-disk-customer-managed-keys.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure contenitori per monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti nei cluster Kubernetes gestiti ospitati in servizio Azure Kubernetes (AKS). 

Configurare gli avvisi per la creazione proattiva di notifiche o log quando l'utilizzo della CPU e della memoria nei nodi o nei contenitori supera le soglie definite o quando si verifica una modifica dello stato di integrità nel cluster nel rollup dell'integrità dell'infrastruttura o dei nodi. 

Usare il log attività di Azure per monitorare i cluster del servizio AzureKs e le risorse correlate a un livello elevato. Eseguire l'integrazione con Prometheus per visualizzare le metriche delle applicazioni e dei carichi di lavoro raccolte dai nodi e da Kubernetes usando query per creare avvisi personalizzati, dashboard ed eseguire analisi dettagliate.

- [Informazioni Monitoraggio di Azure per i contenitori](/azure/azure-monitor/insights/container-insights-overview)

- [Come abilitare la Monitoraggio di Azure per i contenitori](/azure/azure-monitor/insights/container-insights-onboard)

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** usare il Centro sicurezza per monitorare le Registro Azure Container incluse le istanze servizio Azure Kubernetes (AKS) per le vulnerabilità. Abilitare il bundle Registri contenitori nel Centro sicurezza per assicurarsi che il Centro sicurezza sia pronto per analizzare le immagini di cui viene inserito il push nel registro.

Ricevere una notifica nel dashboard del Centro sicurezza quando vengono rilevati problemi dopo l'analisi dell'immagine da parte del Centro sicurezza con Qualys. La funzionalità bundle Registri contenitori offre una visibilità più approfondita delle vulnerabilità delle immagini usate Azure Resource Manager registri basati su contenitori. 

Usare il Centro sicurezza per consigli di intervento per ogni vulnerabilità. Queste raccomandazioni includono una classificazione della gravità e indicazioni per la correzione. 

- [Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore nel servizio Azure Kubernetes](../security-center/defender-for-container-registries-introduction.md)

- [Informazioni sulle procedure consigliate per la gestione e la sicurezza delle immagini dei contenitori nel servizio Container](operator-best-practices-container-image-management.md)

- [Informazioni sull'integrazione del registro contenitori con Centro sicurezza di Azure](../security-center/defender-for-container-registries-introduction.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni:** gli aggiornamenti della sicurezza vengono applicati automaticamente ai nodi Linux per proteggere i cluster servizio Azure Kubernetes (AKS) del cliente. Questi aggiornamenti includono correzioni della sicurezza del sistema operativo o gli aggiornamenti del kernel. 

Si noti che il processo per mantenere aggiornati i nodi di Windows Server è diverso dai nodi che eseguono Linux perché i nodi windows server non ricevono aggiornamenti giornalieri. Al contrario, i clienti devono eseguire un aggiornamento nei pool di nodi di Windows Server nei cluster del servizio Azure Service Pack che distribuisce nuovi nodi con l'immagine windows server di base più recente e le patch usando il Pannello di controllo di Azure o l'interfaccia della riga di comando di Azure. Questi aggiornamenti contengono miglioramenti della sicurezza o delle funzionalità del server Delktaks.

- [Informazioni sul modo in cui vengono applicati gli aggiornamenti ai nodi del cluster del servizio Web del servizio Web di Servizio Controllo di accesso che eseguono Linux](node-updates-kured.md)

- [Come aggiornare un pool di nodi del servizio AKS per i cluster del servizio Web Diaks che usano nodi di Windows Server](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [servizio Azure Kubernetes dell'immagine del nodo del servizio Disattesa](node-image-upgrade.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuire una soluzione di gestione automatica delle patch per titoli software di terze parti

**Linee** guida: implementare un processo manuale per garantire che le applicazioni di terze parti del nodo del cluster servizio Azure Kubernetes (AKS) rimangano patch per la durata del cluster. Questa operazione potrebbe richiedere l'abilitazione degli aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee** guida: Esportare i risultati dell'analisi del Centro sicurezza a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corretti.

Usare il cmdlet di PowerShell "Get-AzSecurityTask" per automatizzare il recupero delle attività di sicurezza che il Centro sicurezza consiglia di eseguire per rafforzare la posizione di sicurezza e i risultati dell'analisi delle vulnerabilità di correzione.

- [Come usare PowerShell per visualizzare le vulnerabilità individuate da Centro sicurezza di Azure](/powershell/module/az.security/get-azsecuritytask)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare la classificazione di gravità fornita dal Centro sicurezza per classificare in ordine di priorità la correzione delle vulnerabilità. 

Usare common vulnerability scoring system (CVSS) (o un altro sistema di assegnazione dei punteggi come fornito dallo strumento di analisi) se si usa uno strumento di valutazione della vulnerabilità incorporato (ad esempio Qualys o Rapid7, offerto da Azure).

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query o individuare tutte le risorse (ad esempio calcolo, archiviazione, rete e così via) all'interno delle sottoscrizioni. Assicurarsi di avere le autorizzazioni appropriate (lettura) nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Anche se le risorse classiche di Azure possono essere individuate tramite Resource Graph, è consigliabile creare e usare risorse basate Azure Resource Manager in futuro.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle risorse di Azure con metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia degli asset. 

Applicaretaints, etichette o tag durante la creazione di un pool di nodi servizio Azure Kubernetes (AKS). Anche tutti i nodi all'interno del pool ereditano tale taint, etichetta o tag.

Itaints, labels o tags possono essere usati per riconciliare l'inventario a intervalli regolari e garantire che le risorse non autorizzate vengono eliminate dalle sottoscrizioni in modo immediato.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e impostare tag utente](../azure-resource-manager/management/tag-resources.md)

- [Cluster gestiti - Aggiornare i tag](/rest/api/aks/managedclusters/updatetags)

- [Specificare un taint, un'etichetta o un tag per un pool di nodi](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni:** definire un elenco di risorse di Azure approvate e software approvato per le risorse di calcolo in base alle esigenze aziendali dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:
-   Tipi di risorse non consentiti 

-   Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate in base ai requisiti aziendali dell'organizzazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni:** usare Automazione di Azure Rilevamento modifiche e inventario per trovare il software installato nell'ambiente. 

Raccogliere e visualizzare l'inventario per software, file, daemon Linux, servizi Windows e chiavi del Registro di sistema di Windows nei computer e monitorare le applicazioni software non approvate. 

Tenere traccia delle configurazioni dei computer per individuare i problemi operativi nell'ambiente e comprendere meglio lo stato dei computer.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni:** usare Automazione di Azure Rilevamento modifiche e inventario funzionalità per individuare il software installato nell'ambiente. 

Raccogliere e visualizzare l'inventario per software, file, daemon Linux, servizi Windows e chiavi del Registro di sistema di Windows nei computer e monitorare le applicazioni software non approvate. 

Tenere traccia delle configurazioni dei computer per individuare i problemi operativi nell'ambiente e comprendere meglio lo stato dei computer.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Come usare il monitoraggio dell'integrità dei file](../security-center/security-center-file-integrity-monitoring.md)

- [Informazioni su Azure Rilevamento modifiche](../automation/change-tracking/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni:** usare Automazione di Azure Rilevamento modifiche e inventario per trovare il software installato nell'ambiente. 

Raccogliere e visualizzare l'inventario per software, file, daemon Linux, servizi Windows e chiavi del Registro di sistema di Windows nei computer e monitorare le applicazioni software non approvate. 

Tenere traccia delle configurazioni dei computer per individuare i problemi operativi nell'ambiente e comprendere meglio lo stato dei computer.

Abilitare l'analisi adattiva delle applicazioni nel Centro sicurezza per le applicazioni presenti nell'ambiente.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Come usare i controlli Centro sicurezza di Azure applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni usando definizioni di criteri predefiniti.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.
- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script nelle risorse di calcolo

**Indicazioni:** servizio Azure Kubernetes (AKS) non offre una soluzione di gestione delle identità in cui vengono archiviati gli account utente e le password normali. Usare invece Azure Active Directory (Azure AD) come soluzione di gestione delle identità integrata per i cluster del servizio Web Diaks.

Concedere a utenti o gruppi l'accesso alle risorse Kubernetes all'interno di uno spazio dei nomi o all'interno del cluster usando l Azure AD integrazione.

Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi del servizio Azure AD e usarlo per riconciliare l'accesso a intervalli regolari. Usare l'interfaccia della riga di comando di Azure per operazioni come 'Ottenere le credenziali di accesso per un cluster Kubernetes gestito. Implementare le raccomandazioni di Gestione delle identità e degli accessi del Centro sicurezza.

- [Gestire il servizio AzureKs con l'interfaccia della riga di comando di Azure](/cli/azure/aks)

- [Informazioni sull'integrazione Azure AD servizio Azure AD servizio Web](concepts-identity.md)

- [Come integrare il Azure AD](azure-ad-integration-cli.md)

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Come monitorare l'identità e l'accesso con Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee** guida: usare le funzionalità servizio Azure Kubernetes (AKS) per isolare logicamente i team e i carichi di lavoro nello stesso cluster per il minor numero di privilegi, con ambito per le risorse richieste da ogni team. 

Implementare lo spazio dei nomi in Kubernetes per creare un limite di isolamento logico. Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.ContainerService" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di servizio Azure Kubernetes (AKS). 

Esaminare e implementare altre funzionalità e considerazioni di Kubernetes per l'isolamento e la multi-tenancy per includere gli elementi seguenti: pianificazione, rete, autenticazione/autorizzazione e contenitori. Usare anche sottoscrizioni e gruppi di gestione separati per lo sviluppo, il test e la produzione. Separare i cluster del servizio AKS con reti virtuali, subnet contrassegnate in modo appropriato e protette con web application firewall (WAF).

- [Informazioni sulle procedure consigliate per l'isolamento del cluster nel servizio AppKs](operator-best-practices-cluster-isolation.md)

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Informazioni sulle procedure consigliate per la connettività di rete e la sicurezza nel servizio Web Diaks](operator-best-practices-network.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.ContainerService" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di servizio Azure Kubernetes (AKS). Usare le definizioni Criteri di Azure predefiniti.

Di seguito sono riportati alcuni esempi di definizioni di criteri predefiniti per il server del supporto di Microsoft Windows Server 2003:

- Imponi traffico HTTPS in ingresso nel cluster Kubernetes

- Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes

- Il controllo degli accessi in base al ruolo deve essere usato nei servizi Kubernetes

- Verifica che solo le immagini dei contenitori consentite siano in esecuzione nel cluster Kubernetes

Esportare un modello della configurazione del servizio Servizio JavaScript Object Notation servizio JavaScript Object Notation (JSON) con Azure Resource Manager. Esaminarlo periodicamente per assicurarsi che queste configurazioni soddisfino i requisiti di sicurezza per l'organizzazione. Usare le raccomandazioni di Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure. 

- [Come configurare e gestire i criteri di sicurezza dei pod del servizio Contenitore di AKS](use-pod-security-policies.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni:** i cluster del servizio Azure Kubernetes vengono distribuiti nelle macchine virtuali host con un sistema operativo ottimizzato per la sicurezza. Il sistema operativo host include ulteriori passaggi di protezione avanzata per ridurre la superficie di attacco e consente la distribuzione dei contenitori in modo sicuro. 

Azure applica patch giornaliere (incluse le patch di sicurezza) agli host delle macchine virtuali del servizio AzureKs con alcune patch che richiedono un riavvio. I clienti sono responsabili della pianificazione dei riavvii dell'host delle macchine virtuali del servizio Web Diaks in base alle esigenze. 

- [Protezione avanzata della sicurezza per il sistema operativo host del nodo dell'agente del servizio Diaks](security-hardened-vm-host-image.md)

- [Informazioni sulla protezione avanzata negli host delle macchine virtuali del servizio Web Diaks](security-hardened-vm-host-image.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** proteggere il cluster servizio Azure Kubernetes (AKS) usando i criteri di sicurezza dei pod.  Limitare i pod che possono essere pianificati per migliorare la sicurezza del cluster. 

I pod che richiedono risorse non consentite non possono essere eseguiti nel cluster del servizio Contenitore di Microsoft. 

Usare anche gli effetti Criteri di Azure [nega] e [distribuisci se non esiste] per applicare impostazioni sicure per le risorse di Azure correlate alle distribuzioni del servizio Azure Azure Criteri di Azure(ad esempio reti virtuali, subnet, firewall di Azure, account di archiviazione e così via). 

Creare definizioni Criteri di Azure personalizzate usando gli alias degli spazi dei nomi seguenti: 

- Microsoft.ContainerService

- Microsoft.Network

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni:** servizio Azure Kubernetes cluster del servizio Azure Servizio Azure Kubernetes vengono distribuiti in macchine virtuali host con un sistema operativo ottimizzato per la sicurezza. Il sistema operativo host include ulteriori passaggi di protezione avanzata incorporati al suo interno per ridurre la superficie di attacco e consente la distribuzione di contenitori in modo sicuro. 

Fare riferimento all'elenco dei controlli Center for Internet Security (CIS) incorporati nel sistema operativo host.  

- [Protezione avanzata della sicurezza per il sistema operativo host del nodo dell'agente del servizio AKS](security-hardened-vm-host-image.md)

- [Informazioni sulla configurazione dello stato dei cluster del servizio AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Informazioni sulla protezione avanzata della sicurezza negli host di macchine virtuali del servizio Servizio Web Di windows](security-hardened-vm-host-image.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** usare Azure Repos per archiviare e gestire in modo sicuro le configurazioni se si usano definizioni Criteri di Azure personalizzate. Esportare un modello della configurazione servizio Azure Kubernetes (AKS) in JavaScript Object Notation (JSON) con Azure Resource Manager. Esaminarlo periodicamente per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

Implementare soluzioni di terze parti, ad esempio Terraform, per creare un file di configurazione che dichiari le risorse per il cluster Kubernetes. È possibile applicare la protezione avanzata alla distribuzione del servizio Web Diack implementando le procedure consigliate per la sicurezza e archiviando la configurazione come codice in una posizione protetta.

- [Definire un cluster Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Protezione avanzata della sicurezza per il sistema operativo host del nodo dell'agente del servizio AKS](security-hardened-vm-host-image.md)

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni:** non applicabile a servizio Azure Kubernetes (AKS). Il servizio Gateway Gateway offre un sistema operativo host ottimizzato per la sicurezza per impostazione predefinita. Non esiste alcuna opzione corrente per selezionare un sistema operativo alternativo o personalizzato.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni usando definizioni di criteri predefiniti e alias Criteri di Azure nello spazio dei nomi "Microsoft.ContainerService". 

Creare criteri personalizzati per controllare e applicare le configurazioni di sistema. Sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni:** servizio Azure Kubernetes cluster del servizio Azure Servizio Azure Kubernetes vengono distribuiti in macchine virtuali host con un sistema operativo ottimizzato per la sicurezza. Il sistema operativo host include ulteriori passaggi di protezione avanzata per ridurre la superficie di attacco e consente la distribuzione di contenitori in modo sicuro. 

Fare riferimento all'elenco dei controlli Center for Internet Security (CIS) incorporati negli host del servizio Web Diaks.  

- [Protezione avanzata della sicurezza per il sistema operativo host del nodo dell'agente del servizio Diaks](security-hardened-vm-host-image.md)

- [Informazioni sulla protezione avanzata negli host delle macchine virtuali del servizio Web Diaks](security-hardened-vm-host-image.md)

- [Informazioni sulla configurazione dello stato dei cluster del servizio Web Disassociato di Microsoft](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare il Centro sicurezza per eseguire analisi di base per le risorse correlate alle distribuzioni servizio Azure Kubernetes (AKS). Le risorse di esempio includono, tra l'altro, il cluster del servizio Web Disassociato di Microsoft, la rete virtuale in cui è stato distribuito il cluster, l'account Archiviazione di Azure usato per tenere traccia dello stato di Terraform o le istanze di Azure Key Vault usate per le chiavi di crittografia per i dischi dati e del sistema operativo del cluster del servizio Web.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni:** usare le raccomandazioni per i contenitori del Centro sicurezza nella sezione "App di calcolo" per eseguire analisi di base &amp; per i cluster servizio Azure Kubernetes (AKS). 

Ricevere una notifica nel dashboard del Centro sicurezza quando vengono rilevati problemi di configurazione o vulnerabilità. Questa operazione richiede l'abilitazione del bundle di registri contenitori facoltativo che consente al Centro sicurezza di analizzare l'immagine.  

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee** guida: integrare Azure Key Vault con un cluster servizio Azure Kubernetes (AKS) usando un'unità FlexVolume. Usare Azure Key Vault per archiviare e ruotare regolarmente segreti, ad esempio credenziali, chiavi dell'account di archiviazione o certificati. Questo driver consente al cluster del servizio Azure Kubernetes di recuperare le credenziali in modo nativo da Azure Key Vault e fornirle in tutta sicurezza solo al pod che le ha richieste. Usare un'identità gestita del pod per richiedere l'accesso Key Vault e recuperare le credenziali necessarie tramite il driver FlexVolume. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata. 

Limitare l'esposizione delle credenziali non definendo le credenziali nel codice dell'applicazione. 

Evitare l'uso di credenziali fisse o condivise. 

- [Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes](concepts-security.md)

- [Come usare il Key Vault con il cluster del servizio Web Diaks](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** non definire le credenziali nel codice dell'applicazione come procedura consigliata per la sicurezza. Usare le identità gestite per le risorse di Azure per consentire a un pod di autenticarsi su qualsiasi servizio in Azure che lo supporta, incluso Azure Key Vault. Al pod viene assegnata un'identità di Azure per l'autenticazione a Azure Active Directory (Azure AD) e la ricezione di un token digitale che può essere presentato ad altri servizi di Azure che controllano se il pod è autorizzato ad accedere al servizio ed eseguire le azioni necessarie.

Si noti che le identità gestite dal pod sono destinate all'uso solo con pod Linux e immagini del contenitore. Effettuare Azure Key Vault per archiviare e recuperare le chiavi e le credenziali digitali. Le chiavi, ad esempio quelle usate per crittografare i dischi del sistema operativo, possono essere archiviate in un Azure Key Vault.

Le entità servizio possono essere usate anche nei cluster del servizio Web Diaks. Tuttavia, i cluster che usano entità servizio alla fine possono raggiungere uno stato in cui l'entità servizio deve essere rinnovata per mantenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più semplice usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio che per le identità gestite.

- [Informazioni su identità gestite e Key Vault con servizio Azure Kubernetes (servizio AzureKs)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD'identità del pod](https://github.com/Azure/aad-pod-identity)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner incoraggia anche lo spostamento delle credenziali individuate in posizioni più sicure, ad esempio Azure Key Vault con raccomandazioni.

Limitare l'esposizione delle credenziali non definendo le credenziali nel codice dell'applicazione. ed evitare l'uso di credenziali condivise. Azure Key Vault usare per archiviare e recuperare le chiavi digitali e le credenziali. Usare le identità gestite per le risorse di Azure per consentire al pod di richiedere l'accesso ad altre risorse. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Procedure consigliate per gli sviluppatori per la sicurezza dei pod](developer-best-practices-pod-security.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Usare software antimalware gestito centralmente

**Indicazioni:** il servizio Diaas gestisce il ciclo di vita e le operazioni dei nodi agente per conto dell'utente. La modifica delle risorse IaaS associate ai nodi agente non è supportata. Tuttavia, per i nodi Linux è possibile usare set di daemon per installare software personalizzato come una soluzione antimalware.

- [Guida di riferimento agli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori - servizio Azure Kubernetes cluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Responsabilità condivisa e set di daemon del centro di controllo di accesso](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** eseguire l'analisi preliminare di tutti i file caricati nelle risorse del componente AKS. Usare il rilevamento delle minacce del Centro sicurezza per i servizi dati per rilevare il malware caricato in account di archiviazione se si usa un account Archiviazione di Azure come archivio dati o per tenere traccia dello stato di Terraform per il cluster del servizio AzureKs. 

- [Informazioni Centro sicurezza di Azure rilevamento delle minacce per i servizi dati](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assicurarsi che il software antimalware e le firme siano aggiornati

**Indicazioni:** il servizio Diaas gestisce il ciclo di vita e le operazioni dei nodi agente per conto dell'utente. La modifica delle risorse IaaS associate ai nodi agente non è supportata. Tuttavia, per i nodi Linux è possibile usare set di daemon per installare software personalizzato come una soluzione antimalware.

- [Guida di riferimento per gli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori - servizio Azure Kubernetes cluster](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS shared responsibility and Daemon Sets](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni:** eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi persistenti insieme a risorse e configurazioni aggiuntive del cluster. Verificare periodicamente l'integrità e la sicurezza di tali backup. 

Rimuovere lo stato dalle applicazioni prima del backup. Nei casi in cui questa operazione non può essere eseguita, eseguire il backup dei dati dai volumi permanenti e testare regolarmente le operazioni di ripristino per verificare l'integrità dei dati e i processi necessari.

- [Procedure consigliate per l'archiviazione e i backup nel servizio AzureKs](operator-best-practices-storage.md)

- [Procedure consigliate per la continuità aziendale e il ripristino di emergenza nel servizio AKS](operator-best-practices-multi-region.md)

- [Informazioni Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Come configurare Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi permanenti insieme a risorse e configurazioni aggiuntive del cluster. 

Eseguire backup automatizzati regolari di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti, con i comandi di PowerShell. 

- [Come eseguire il backup Key Vault certificati](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Come eseguire il backup Key Vault chiavi](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Come eseguire il backup Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup Key Vault segreti](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Come abilitare la Backup di Azure](/azure/backup/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** eseguire periodicamente il ripristino dei dati del contenuto all'interno di Velero Backup. Se necessario, testare il ripristino in una rete virtuale isolata.

Eseguire periodicamente il ripristino dei dati Key Vault certificati, chiavi, account di archiviazione gestiti e segreti, con i comandi di PowerShell.

- [Come ripristinare i Key Vault certificati](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Come ripristinare le Key Vault chiave](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Come ripristinare gli Key Vault di archiviazione gestiti](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare i Key Vault segreti](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Come ripristinare i file dal backup delle macchine virtuali di Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi permanenti insieme a configurazioni e risorse cluster aggiuntive. 

Abilitare Soft-Delete in Key Vault proteggere le chiavi da eliminazioni accidentali o dannose se Azure Key Vault viene usato con per le distribuzioni servizio Azure Kubernetes (AKS).

- [Informazioni sulla Archiviazione di Azure del servizio](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

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

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee** guida: stabilire in ordine di priorità quali avvisi devono essere esaminati prima con la gravità assegnata dal Centro sicurezza agli avvisi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o nell'analisi usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.
Contrassegnare chiaramente le sottoscrizioni (ad esempio, produzione, non di produzione) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee** guida: eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere i piani di risposta agli eventi imprevisti in base alle esigenze.

- [Guida ai programmi di test, training ed esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. 

Esaminare gli eventi imprevisti, dopo il fatto, per assicurarsi che i problemi siano risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee** guida: Esportare avvisi e raccomandazioni del Centro sicurezza usando la funzionalità Esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. 

Scegliere il connettore dati del Centro sicurezza per trasmettere gli avvisi Azure Sentinel, in base alle esigenze e in base ai requisiti aziendali dell'organizzazione.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee** guida: usare la funzionalità Automazione flusso di lavoro nel Centro sicurezza per attivare automaticamente le risposte tramite "App per la logica" sugli avvisi di sicurezza e sulle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee** guida: seguire le regole microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Per altre informazioni sulla strategia e l'esecuzione di Microsoft dei test di penetrazione del red teaming e del sito live sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft, vedere i collegamenti a cui si fa riferimento.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
