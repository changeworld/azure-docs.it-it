---
title: Baseline di sicurezza di Azure per Azure Machine Learning
description: La baseline Azure Machine Learning sicurezza fornisce linee guida e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817000"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Baseline di sicurezza di Azure per Azure Machine Learning

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Microsoft Azure Machine Learning. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Azure Machine Learning. **I** controlli non applicabili Azure Machine Learning sono stati esclusi.

 
Per informazioni su come Azure Machine Learning completamente mappato ad Azure Security Benchmark, vedere il [file di mapping](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)completo Azure Machine Learning baseline di sicurezza .

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. Ad esempio, è possibile usare l'istanza di calcolo Machine Learning virtuale di Azure per eseguire il training di un modello e quindi distribuire il modello in servizio Azure Kubernetes (AKS). È possibile proteggere i cicli di vita di Machine Learning isolando Azure Machine Learning di training e inferenza all'interno di una rete virtuale di Azure.

Firewall di Azure possibile usare per controllare l'accesso all'area Azure Machine Learning lavoro e alla rete Internet pubblica.

- [Panoramica dell'isolamento della rete virtuale e della privacy](how-to-network-security-overview.md)

- [Usare l'area di lavoro dietro Firewall di Azure per Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Assegnare gruppi di sicurezza di rete alle reti create come Machine Learning distribuzione. 

Abilitare i log dei flussi dei gruppi di sicurezza di rete e inviare i log a un account Archiviazione di Azure per il controllo. È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure. Alcuni vantaggi delle Analisi del traffico sono la possibilità di visualizzare le attività di rete, identificare gli hot spot e le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni erre della rete.

- [Come abilitare i log di flusso del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni:** è possibile abilitare HTTPS per proteggere la comunicazione con i servizi Web distribuiti da Azure Machine Learning. I servizi Web vengono distribuiti in Azure Kubernetes Services (AKS) o Istanze di Azure Container (ACI) e proteggere i dati inviati dai client. È anche possibile usare l'indirizzo IP privato con il servizio AKS per limitare l'assegnazione dei punteggi, in modo che solo i client dietro una rete virtuale possano accedere al servizio Web.

- [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)

- [Panoramica dell'isolamento della rete virtuale e della privacy](how-to-network-security-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare Protezione DDoS Standard nelle reti virtuali associate all'istanza di Machine Learning per la protezione dagli attacchi DDoS (Distributed Denial of Service). Usare Centro sicurezza di Azure rilevamento delle minacce integrato per rilevare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Distribuire Firewall di Azure in ogni limite di rete dell'organizzazione con il filtro basato su intelligence sulle minacce abilitato e configurato su "Avviso e rifiuto" per il traffico di rete dannoso.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Usare l'area di lavoro Firewall di Azure per Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Per altre informazioni sul rilevamento Centro sicurezza di Azure delle minacce](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** per tutte le macchine virtuali con l'estensione appropriata installata nei servizi Azure Machine Learning, è possibile abilitare Network Watcher'acquisizione di pacchetti per analizzare le attività anomale. 

- [Come creare un'Network Watcher predefinita](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** distribuire la soluzione firewall preferita in ogni limite di rete dell'organizzazione per rilevare e/o bloccare il traffico dannoso.

Selezionare un'offerta da Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload.  Quando l'ispezione del payload non è un requisito, Firewall di Azure possibile usare l'intelligence per le minacce. Firewall di Azure filtro basato sull'intelligence per le minacce viene usato per avvisare e/o bloccare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

- [Come distribuire Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere all'account Azure Machine Learning, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, AzureMachineLearning) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

servizio Azure Machine Learning un elenco di tag di servizio per le destinazioni di calcolo all'interno di una rete virtuale che consente di ridurre al minimo la complessità, è possibile usarlo come linee guida per la gestione della rete.

- [Per altre informazioni sull'uso dei tag di servizio](../virtual-network/service-tags-overview.md)

- [Panoramica dell'isolamento della rete virtuale e della privacy](how-to-network-security-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate Azure Machine Learning spazi dei nomi con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.MachineLearning" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi Machine Learning rete. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per le risorse di rete associate alla distribuzione Azure Machine Learning per organizzarli in modo logico in base a una tassonomia.

Per una risorsa nella Azure Machine Learning virtuale che supporta il campo Descrizione, usarla per documentare le regole che consentono il traffico da e verso una rete.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare Log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate Azure Machine Learning. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da Azure Machine Learning. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare Archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione. In alternativa, è possibile abilitare e caricare i dati in Azure Sentinel o in una integrazione di Informazioni di sicurezza e gestione degli eventi (SIEM) di terze parti.

- [Come configurare i log di diagnostica per Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare le impostazioni di diagnostica nelle risorse di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

È anche possibile correlare Machine Learning dei log delle operazioni del servizio per motivi di sicurezza e conformità.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-settings)

- [Informazioni sulla registrazione e i diversi tipi di log in Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Abilitare la registrazione Azure Machine Learning](how-to-log-view-metrics.md)

- [Monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della raccolta e del monitoraggio. 

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, usare Centro sicurezza di Azure per monitorare il sistema operativo. 

- [Come raccogliere i log dell'host interno della macchina virtuale di Azure con Monitoraggio di Azure](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Informazioni Centro sicurezza di Azure raccolta dati](../security-center/security-center-enable-data-collection.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** in Monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro Log Analytics associate alle istanze di Azure Machine Learning in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati dell'Azure Machine Learning. Usare Monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'on board dei dati Azure Sentinel o un SIEM di terze parti. 

- [Come eseguire query per l'Azure Machine Learning nelle aree di lavoro Log Analytics](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Abilitare la registrazione in Azure Machine Learning](how-to-log-view-metrics.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introduzione alle query di Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** in Monitoraggio di Azure configurare i log correlati a Azure Machine Learning all'interno del log attività e le impostazioni di diagnostica di Machine Learning per inviare i log in un'area di lavoro Log Analytics su cui eseguire query o in un account di archiviazione per l'archiviazione a lungo termine. Usare l'area di lavoro Log Analytics per creare avvisi per le attività anomae trovate nei log di sicurezza e negli eventi.

In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel.

- [Per altre informazioni sugli avvisi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Come avvisare i dati di log dell'area di lavoro Log Analytics](/azure/azure-monitor/learn/tutorial-response)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della distribuzione antimalware di servizio Azure Machine Learning.

Azure Machine Learning il supporto varia tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, abilitare la raccolta di eventi antimalware per Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali.

- [Come configurare l'estensione Microsoft Antimalware per i servizi cloud](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Informazioni Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida:** non applicabile; Azure Machine Learning non elabora o produce log correlati a DNS.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Centro sicurezza di Azure per abilitare il monitoraggio del log eventi di sicurezza per le macchine virtuali di Azure. Centro sicurezza di Azure esegue il provisioning dell'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e in quelle nuove create se è abilitato il provisioning automatico. In caso di installazione manuale dell'agente. L'agente abilita l'evento di creazione del processo 4688 e il campo della riga di comando all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati dal registro eventi e monitorati dai servizi di rilevamento del Centro sicurezza.

- [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** è possibile usare la scheda Identity and Access Management (Gestione delle identità e degli accessi) per una risorsa nel portale di Azure per configurare il controllo degli accessi in base al ruolo di Azure e mantenere l'inventario Azure Machine Learning risorse. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile usare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi.

Azure Machine Learning fornisce ruoli predefiniti per scenari di gestione comuni in Azure Machine Learning. Un utente con un profilo in Azure Active Directory (Azure AD) può assegnare questi ruoli a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso alle risorse e alle operazioni Azure Machine Learning risorse.

È anche possibile usare il Azure AD PowerShell per eseguire query adhoc per individuare gli account membri dei gruppi amministrativi.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure in Azure Machine Learning](how-to-assign-roles.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** la gestione degli accessi Machine Learning risorse è controllata tramite Azure Active Directory (Azure AD). Azure AD non ha il concetto di password predefinite.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** Azure Machine Learning con tre ruoli predefiniti quando viene creata una nuova area di lavoro, creando procedure operative standard per l'uso degli account proprietari.

È anche possibile abilitare l'accesso JUST-In-Time agli account amministrativi usando Azure Active Directory (Azure AD) Privileged Identity Management e Azure Resource Manager.

- [Per altre informazioni sui Machine Learning predefiniti](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Altre informazioni sulle Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni:** Machine Learning è integrato con Azure Active Directory (Azure AD), usare Azure AD SSO invece di configurare singole credenziali autonome per servizio. Usare le Centro sicurezza di Azure per l'identità e l'accesso.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni sull'identità e l'accesso.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare una workstation protetta gestita da Azure (nota anche come workstation di accesso con privilegi o PAW) per attività amministrative che richiedono privilegi elevati.

- [Informazioni sulle workstation protette gestite da Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory report di sicurezza (Azure AD) e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Indicazioni:** usare Azure Active Directory (Azure AD) denominate per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare le Azure AD denominate](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.
 
L'ambito dell'accesso al ruolo può essere limitato a più livelli in Azure. Ad Machine Learning, i ruoli possono essere gestiti a livello di area di lavoro, ad esempio si dispone dell'accesso proprietario a un'area di lavoro che potrebbe non disporre dell'accesso proprietario al gruppo di risorse che contiene l'area di lavoro. In questo modo vengono forniti controlli di accesso più granulari per separare i ruoli all'interno dello stesso gruppo di risorse. 

- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md) 
 
- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. È anche possibile usare le Azure AD di identità e di accesso per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere rivisto a intervalli regolari per assicurarsi che solo gli utenti autorizzati possano continuare ad accedere.

Usare Azure AD Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

- [Informazioni sulla creazione Azure AD report](/azure/active-directory/reports-monitoring)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuire Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** è possibile accedere alle origini del registro eventi di Azure Active Directory (Azure AD) di accesso, controllo e rischio, che consentono di eseguire l'integrazione con qualsiasi strumento di monitoraggio/informazioni di sicurezza e gestione degli eventi.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente Azure AD e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure con Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni:** usare Azure Active Directory (Azure AD) di Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** non applicabile; servizio Azure Machine Learning non supporta customer lockbox.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.
 
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare l'isolamento usando sottoscrizioni e gruppi di gestione separati per i singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di riservatezza dei dati. È possibile limitare il livello di accesso alle risorse di Azure richiesto dalle applicazioni e dall'ambiente aziendale. È possibile controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure.
 
- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

 
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: usare una soluzione di terze parti Azure Marketplace nei perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti, inviando avvisi ai professionisti della sicurezza delle informazioni. 

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e li protegge dalla perdita e dall'esposizione. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** i servizi Web distribuiti tramite Azure Machine Learning supportano solo TLS versione 1.2 che applica la crittografia dei dati in transito.

- [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per Azure Machine Learning. Implementare una soluzione di terze parti, se necessario per motivi di conformità.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Linee** guida: Azure Machine Learning supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste Machine Learning risorse. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md)

- [Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes](../aks/manage-azure-rbac.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee** guida: Azure Machine Learning snapshot, output e log nell'account di archiviazione BLOB di Azure associato all'area di lavoro Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'Archiviazione BLOB di Azure vengono crittografati quando inattivi con le chiavi gestite da Microsoft. È anche possibile crittografare i dati archiviati nell'archivio BLOB di Azure con le proprie chiavi Machine Learning servizio. 

- [Azure Machine Learning crittografia dei dati in stato di inalter](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle istanze di produzione di Azure Machine Learning e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della gestione delle vulnerabilità dei servizio Azure Machine Learning. 

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni di Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle macchine virtuali di Azure, nelle immagini del contenitore e nei server SQL.

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della gestione delle patch servizio Azure Machine Learning. 

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, usare Automazione di Azure Gestione aggiornamenti per assicurarsi che gli aggiornamenti della sicurezza più recenti siano installati nelle macchine virtuali Windows e Linux. Per le macchine virtuali Windows, assicurarsi che Windows Update sia stato abilitato e impostato per l'aggiornamento automatico.

- [Come configurare le Gestione aggiornamenti per le macchine virtuali in Azure](../automation/update-management/overview.md)

- [Informazioni su criteri di sicurezza di Azure monitorati dal Centro sicurezza](../security-center/policy-reference.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuire una soluzione di gestione automatica delle patch per titoli software di terze parti

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare una soluzione di gestione delle patch di terze parti. I clienti che usano Gestione configurazione nel proprio ambiente possono anche usare System Center Updates Publisher, consentendo loro di pubblicare aggiornamenti personalizzati nel servizio Windows Server Update. Ciò consente Gestione aggiornamenti di applicare patch ai computer Gestione configurazione come repository di aggiornamento con software di terze parti.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni di Centro sicurezza di Azure per l'esecuzione di valutazioni della vulnerabilità nelle macchine virtuali di Azure, nelle immagini dei contenitori e nei server SQL. Esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati con le analisi precedenti per verificare che le vulnerabilità siano state corretti. Quando si usano le raccomandazioni di gestione delle vulnerabilità suggerite da Centro sicurezza di Azure, è possibile eseguire il pivot nel portale della soluzione selezionata per visualizzare i dati di analisi cronologica.

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni. Verificare le autorizzazioni appropriate (lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse nelle sottoscrizioni.

Anche se le risorse classiche di Azure possono essere individuate tramite Azure Resource Graph Explorer, è consigliabile creare e usare le Azure Resource Manager in futuro.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle risorse di Azure, aggiungendo metadati per organizzare logicamente in base a una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [ Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [ Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)
 
- [ Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni:** creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

* Tipi di risorse non consentiti
* Tipi di risorse consentiti

Inoltre, usare il Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni:** Azure Machine Learning ha un supporto variabile per risorse di calcolo diverse e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Automazione di Azure Rilevamento modifiche e inventario per automatizzare la raccolta di informazioni di inventario dalle macchine virtuali Windows e Linux. Il nome software, la versione, l'editore e l'ora di aggiornamento sono disponibili nel portale di Azure. Per ottenere la data di installazione del software e altre informazioni, abilitare la diagnostica a livello di guest e indirizzare i log eventi di Windows all'area di lavoro Log Analytics.

- [Come abilitare la raccolta Automazione di Azure inventario per una macchina virtuale](../automation/automation-tutorial-installed-software.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare il monitoraggio dell'integrità dei file (FIM) di Centro sicurezza di Azure per identificare tutto il software installato nelle macchine virtuali. Un'altra opzione che può essere usata al posto di o in combinazione con FIM Automazione di Azure Rilevamento modifiche e inventario raccogliere l'inventario dalle macchine virtuali Linux e Windows. 

È possibile implementare un processo personalizzato per la rimozione di software non autorizzato. È anche possibile usare una soluzione di terze parti per identificare il software non approvato.

Rimuovere le risorse di Azure quando non sono più necessarie.

- [Come usare il monitoraggio dell'integrità dei file](../security-center/security-center-file-integrity-monitoring.md)

- [Informazioni Automazione di Azure Rilevamento modifiche e inventario](../automation/change-tracking/overview.md)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Gruppo di risorse di Azure ed eliminazione di risorse](../azure-resource-manager/management/delete-resource-group.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Centro sicurezza di Azure controlli delle applicazioni adattivi per garantire che solo il software autorizzato sia eseguito e che l'esecuzione di tutto il software non autorizzato sia bloccata nelle macchine virtuali di Azure.

- [Come usare i controlli Centro sicurezza di Azure applicazioni adattive](../security-center/security-center-adaptive-application.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

* Tipi di risorse non consentiti
* Tipi di risorse consentiti

Usare inoltre il Azure Resource Graph per eseguire query e individuare le risorse nelle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

**Indicazioni:** Azure Machine Learning ha un supporto variabile per risorse di calcolo diverse e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare i controlli Centro sicurezza di Azure applicazioni adattivi per specificare a quali tipi di file può essere applicata o meno una regola.

Implementare una soluzione di terze parti se i controlli applicazioni adattivi non soddisfano il requisito.

- [Come usare i controlli Centro sicurezza di Azure applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare l'Azure Active Directory condizionale Azure Active Directory (Azure AD) per limitare la capacità degli utenti di interagire con Azure Resources Manager configurando "Blocca l'accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script nelle risorse di calcolo

**Indicazioni:** Azure Machine Learning ha un supporto variabile per risorse di calcolo diverse e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure. È anche possibile usare i Centro sicurezza di Azure applicazioni adattivi per garantire che solo il software autorizzato sia in esecuzione e che l'esecuzione di tutto il software non autorizzato sia bloccata nelle macchine virtuali di Azure.

- [Come controllare l'esecuzione di script di PowerShell in ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Come usare i controlli Centro sicurezza di Azure applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard per servizio Azure Machine Learning con Criteri di Azure. Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.MachineLearning" per creare criteri personalizzati per controllare o applicare la configurazione dei Azure Machine Learning servizio.

Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni di Centro sicurezza di Azure come baseline di configurazione sicura per le risorse di Azure.

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro; È possibile clonare i repository direttamente nell'file system dell'area di lavoro condivisa, usare Git nella workstation locale e assicurarsi che le configurazioni sicure siano applicabili alle risorse di codice come parte dell'ambiente Machine Learning locale.

- [Come visualizzare gli alias Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile delle configurazioni sicure del sistema operativo servizio Azure Machine Learning.

Azure Machine Learning il supporto varia tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Centro sicurezza di Azure raccomandazioni per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.  È anche possibile usare immagini del sistema operativo personalizzate o Automazione di Azure stato per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare le Centro sicurezza di Azure raccomandazioni](../security-center/security-center-recommendations.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

- [Automazione di Azure State Configuration panoramica](../automation/automation-dsc-overview.md)

- [Caricare un disco rigido virtuale e usarlo per creare nuove macchine virtuali Windows in Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure](../virtual-machines/linux/upload-vhd.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure. È anche possibile usare i modelli Azure Resource Manager per mantenere la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 
 
- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)
 
- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager panoramica dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile delle configurazioni sicure del sistema operativo servizio Azure Machine Learning.

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni Centro sicurezza di Azure sull'esecuzione di valutazioni delle vulnerabilità nelle risorse di calcolo di Azure.  È anche possibile usare modelli Azure Resource Manager, immagini del sistema operativo personalizzate o Automazione di Azure State Configuration per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.   I modelli di macchina virtuale Microsoft combinati con il Automazione di Azure State Configuration possono essere utili per soddisfare e gestire i requisiti di sicurezza. 

Si noti Azure Marketplace macchine virtuali Le immagini pubblicate da Microsoft vengono gestite e gestite da Microsoft. 

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

- [Come creare una macchina virtuale di Azure da un modello di Azure Arm](../virtual-machines/windows/ps-template.md)

- [Automazione di Azure State Configuration panoramica](../automation/automation-dsc-overview.md)

- [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md)

- [Informazioni su come scaricare il modello di macchina virtuale](/previous-versions/azure/virtual-machines/windows/download-template)

- [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di Criteri di Azure personalizzate per il Machine Learning o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro; È possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare Git nella workstation locale e assicurarsi che le configurazioni sicure siano applicabili alle risorse di codice come parte dell'ambiente Machine Learning locale.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee** guida: Azure Machine Learning supporto variabile tra risorse di calcolo diverse e anche le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare il controllo degli accessi in base al ruolo di Azure per assicurarsi che solo gli utenti autorizzati possano accedere alle immagini personalizzate. Usare una raccolta di immagini condivise di Azure per condividere le immagini con utenti, entità servizio o gruppi Azure Active Directory (Azure AD) all'interno dell'organizzazione. Archiviare le immagini dei contenitori Registro Azure Container e usare il controllo degli accessi in base al ruolo di Azure per garantire che solo gli utenti autorizzati hanno accesso.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure per Registro Contenitori](../container-registry/container-registry-roles.md)

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Panoramica di Raccolta immagini condivise](../virtual-machines/shared-image-galleries.md)

- [Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes](../aks/manage-azure-rbac.md)

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.MachineLearning" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della distribuzione della configurazione sicura servizio Azure Machine Learning.

Azure Machine Learning il supporto varia tra risorse di calcolo diverse e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Automazione di Azure State Configuration per i nodi Desired State Configuration (DSC) in qualsiasi data center cloud o locale. È possibile eseguire facilmente l'onboard delle macchine virtuali, assegnare loro configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. 

- [Come abilitare le Automazione di Azure State Configuration](../automation/automation-dsc-onboarding.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure. È anche possibile usare Criteri di Azure avvisi e controllare le configurazioni delle risorse di Azure.
 
 
 
- [ Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni:** se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile del monitoraggio automatizzato della configurazione sicura servizio Azure Machine Learning.

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare app di calcolo Centro sicurezza di Azure e seguire le raccomandazioni per macchine &amp; virtuali, server e contenitori.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare le identità gestite in combinazione con Azure Key Vault per semplificare la gestione dei segreti per le applicazioni cloud.

Azure Machine Learning supporta la crittografia dell'archivio dati con chiavi gestite dal cliente, è necessario gestire la rotazione delle chiavi e revocare i requisiti di sicurezza e conformità per ogni organizzazione. 

Usare Azure Key Vault per passare segreti a esecuzioni remote in modo sicuro anziché testo non crittografato negli script di training.

- [Azure Machine Learning chiavi gestite dal cliente](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Usare i segreti delle credenziali di autenticazione nelle Azure Machine Learning di training](how-to-use-secrets-in-runs.md)

- [Come usare le identità gestite per le risorse di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare un Key Vault](../key-vault/general/quick-create-portal.md)

- [Come eseguire l'autenticazione Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio Key Vault di accesso](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee** guida: Azure Machine Learning supporta sia i ruoli predefiniti che la possibilità di creare ruoli personalizzati. Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Key Vault, senza credenziali nel codice.

- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md)

- [Come configurare le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Usare software antimalware gestito centralmente

**Indicazioni:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Machine Learning), ma non viene eseguito sul contenuto del cliente.

Azure Machine Learning il supporto varia tra risorse di calcolo diverse e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Microsoft Antimalware azure per monitorare e proteggere continuamente le risorse. Per Linux, usare una soluzione antimalware di terze parti. Usare anche il Centro sicurezza di Azure di minacce per i servizi dati per rilevare il malware caricato in account di archiviazione.

- [Come configurare Microsoft Antimalware per Azure](../security/fundamentals/antimalware.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee** guida: Microsoft Anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Machine Learning), ma non viene eseguito sul contenuto del cliente.

È responsabilità dell'utente eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware del contenuto del cliente per conto dell'utente.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assicurarsi che il software antimalware e le firme siano aggiornati

**Indicazioni:** l'antimalware Microsoft è abilitato e gestito per l'host sottostante che supporta i servizi di Azure(ad esempio, Azure Machine Learning), ma non viene eseguito sui contenuti dei clienti.

Azure Machine Learning ha un supporto variabile per diverse risorse di calcolo e anche per le proprie risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni in Centro sicurezza di Azure, App di calcolo per assicurarsi che tutti gli endpoint siano aggiornati con le &amp; firme più recenti. Per Linux, usare una soluzione antimalware di terze parti.

- [Come distribuire i Microsoft Antimalware per Azure](../security/fundamentals/antimalware.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni:** la gestione del ripristino dei dati Machine Learning servizio è tramite la gestione dei dati negli archivi dati connessi. Assicurarsi di seguire le linee guida per il ripristino dei dati per gli archivi connessi per eseguire il backup dei dati in base ai criteri dell'organizzazione dei clienti.

- [Come ripristinare i file dal backup delle macchine virtuali di Azure](../backup/backup-azure-restore-files-from-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** il backup dei dati Machine Learning servizio viene eseguito tramite la gestione dei dati negli archivi dati connessi. Abilitare Backup di Azure per le macchine virtuali e configurare la frequenza e i periodi di conservazione desiderati. Eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.

- [Come ripristinare i file dal backup delle macchine virtuali di Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Come ripristinare le Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** la convalida del backup dei dati Machine Learning servizio è tramite la gestione dei dati negli archivi dati connessi. Eseguire periodicamente il ripristino dei dati del contenuto Backup di Azure. Assicurarsi di poter ripristinare le chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare i file da un backup di macchine virtuali di Azure](../backup/backup-azure-restore-files-from-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** per il backup locale, la crittografia in stato di inquieto viene fornita usando la passphrase fornita durante il backup in Azure. Usare il controllo degli accessi in base al ruolo di Azure per proteggere i backup e le chiavi gestite dal cliente. 

Abilitare la protezione da eliminazione e ripulitura software Key Vault proteggere le chiavi da eliminazioni accidentali o dannose. Se Archiviazione di Azure per archiviare i backup, abilitare l'eliminazione soft per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Come abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Eliminazione soft per l'archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Linee guida:** sviluppare una guida alla risposta agli eventi imprevisti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono tutti i ruoli del personale, nonché le fasi di gestione e gestione degli eventi imprevisti, dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Usare la Guida alla gestione degli eventi imprevisti per la sicurezza del computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee** guida: Centro sicurezza di Azure una gravità a ogni avviso per definire la priorità degli avvisi da ricercare per primi. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o sull'uso analitico per l'emissione dell'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

Contrassegnare inoltre le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

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

**Indicazioni:** esportare gli avvisi Centro sicurezza di Azure e le raccomandazioni usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore Centro sicurezza di Azure dati per trasmettere gli avvisi a Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità di automazione del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi di sicurezza e alle raccomandazioni per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

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
