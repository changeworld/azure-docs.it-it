---
title: Baseline di sicurezza di Azure per Ricerca cognitiva di Azure
description: La Ricerca cognitiva di Azure di sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589194"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Baseline di sicurezza di Azure per Ricerca cognitiva di Azure

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Ricerca cognitiva di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Ricerca cognitiva di Azure. **Controlli** non applicabili alla Ricerca cognitiva di Azure o al cliente è stato escluso.

Per informazioni su come Ricerca cognitiva di Azure completamente mappato ad Azure Security Benchmark, vedere il file di mapping completo Ricerca cognitiva di Azure [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** assicurarsi che a tutte Rete virtuale di Microsoft Azure subnet sia applicato un gruppo di sicurezza di rete con regole per implementare uno schema di accesso con privilegi minimi. Consentire l'accesso solo alle porte e agli intervalli di indirizzi IP attendibili dell'applicazione. Distribuire Ricerca cognitiva di Azure con un endpoint privato di Azure, dove possibile, per abilitare l'accesso privato ai servizi dalla rete virtuale.

Ricerca cognitiva supporta anche funzionalità di sicurezza di rete aggiuntive per la gestione degli elenchi di controllo di accesso alla rete. Configurare il servizio di ricerca in modo da consentire la comunicazione solo con origini attendibili limitando l'accesso da intervalli di indirizzi IP pubblici specifici usando la funzionalità firewall.

- [Come configurare gli endpoint privati per Ricerca cognitiva di Azure](service-create-private-endpoint.md)

- [Come configurare il firewall Ricerca cognitiva di Azure](service-configure-firewall.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** La ricerca cognitiva non può essere distribuita direttamente in una rete virtuale. Tuttavia, se l'applicazione client o le origini dati sono in una rete virtuale, è possibile monitorare e registrare il traffico per tali componenti in rete, incluse le richieste inviate a un servizio di ricerca nel cloud. Le raccomandazioni standard includono l'abilitazione di un log di flusso del gruppo di sicurezza di rete e l'invio di log Archiviazione di Azure un'area di lavoro Log Analytics. Facoltativamente, è possibile usare Analisi del traffico informazioni dettagliate sui modelli di traffico.

- [Come abilitare i log di flusso del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee** guida: Ricerca cognitiva non offre una funzionalità specifica per contrastare un attacco Denial of Service distribuito, ma è possibile abilitare Protezione DDoS Standard nelle reti virtuali associate al servizio di ricerca cognitivo per la protezione generale.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Linee** guida: abilitare i log di flusso del gruppo di sicurezza di rete per i gruppi di sicurezza di rete che proteggono le macchine virtuali di Azure che si connetteranno alla macchina virtuale servizio di ricerca. Inviare i log in un account Archiviazione di Azure per il controllo del traffico. 

Abilitare Network Watcher'acquisizione di pacchetti, se necessario per l'analisi dell'attività anomaa.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee** guida: Ricerca cognitiva non supporta il rilevamento delle intrusioni di rete, ma come mitigazione delle intrusioni, è possibile configurare regole del firewall per specificare gli indirizzi IP accettati dal servizio di ricerca. Configurare un endpoint privato per mantenere il traffico di ricerca lontano dalla rete Internet pubblica.

- [Come configurare le chiavi gestite dal cliente per la crittografia dei dati](search-security-manage-encryption-keys.md)

- [Come ottenere informazioni sulle chiavi gestite dal cliente da indici e mappe sinonimi](search-security-get-encryption-keys.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio, se si usano indicizzatori e set di competenze in Ricerca cognitiva, per rappresentare un intervallo di indirizzi IP autorizzati a connettersi a risorse esterne. 

Consentire o negare il traffico verso le risorse specificando il nome del tag di servizio (ad esempio, AzureCognitiveSearch) nel campo di origine o destinazione appropriato di una regola. 

- [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** è possibile configurare Ricerca cognitiva con un endpoint privato di Azure per integrare il servizio di ricerca con una rete virtuale.  Usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per documentare le regole che consentono il traffico da e verso una rete. 
 

Usare una delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio gli effetti "Richiedi tag e relativo valore", per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag. 

È possibile usare l Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.  

 
- [Come creare un endpoint privato per Ricerca cognitiva](service-create-private-endpoint.md) 

 
 
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md) 

 
- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire i log correlati a Ricerca cognitiva tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine e l'archiviazione. In alternativa, è possibile abilitare e eseguire l'on board di questi dati Azure Sentinel o un SIEM di terze parti.
 

 
- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee** guida: i log di diagnostica e operativi forniscono informazioni dettagliate sulle operazioni dettagliate di Ricerca cognitiva e sono utili per il monitoraggio del servizio e per i carichi di lavoro che accedono al servizio.  Per acquisire i dati di diagnostica, abilitare la registrazione specificando dove vengono archiviate le informazioni di registrazione.
 

 
- [Come raccogliere e analizzare i dati di log per Ricerca cognitiva di Azure](search-monitor-logs.md) 

 
- [Come raccogliere log e metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Search**:

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** i dati cronologici che vengono feed nelle metriche di diagnostica vengono mantenuti da Ricerca cognitiva per 30 giorni per impostazione predefinita. Per una conservazione più lunga, assicurarsi di abilitare l'impostazione che specifica un'opzione di archiviazione per rendere persistenti gli eventi e le metriche registrati.
 

 
In Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare Archiviazione di Azure account per l'archiviazione a lungo termine e di archiviazione. 
 

 
- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Come configurare i criteri di conservazione per i log Archiviazione di Azure account](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee** guida: analizzare e monitorare i log dal servizio di ricerca comportamento anomalo. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

 
 
- [Come raccogliere e analizzare i dati di log per Ricerca cognitiva](search-monitor-logs.md)
 
- [Come visualizzare i dati del log di ricerca in Power BI](search-monitor-logs-powerbi.md)
 

 
- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Informazioni su Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** usare il Centro sicurezza con l'area di lavoro Log Analytics per il monitoraggio e la creazione di avvisi per le attività anomae presenti nei log di sicurezza e negli eventi. In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel.
 

 
- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Come creare un avviso per i dati di log di Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, entità servizio dei gruppi e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure.

I ruoli di Ricerca cognitiva sono associati alle autorizzazioni che supportano le attività di gestione a livello di servizio. Questi ruoli non concedono l'accesso all'endpoint del servizio. L'accesso alle operazioni sull'endpoint, ad esempio la gestione degli indici, il popolamento degli indici e le query sui dati di ricerca, usa le chiavi API per autenticare la richiesta.

- [Impostare i ruoli per l'accesso amministrativo Ricerca cognitiva di Azure](search-security-rbac.md)

- [Creare e gestire chiavi API per un Ricerca cognitiva di Azure servizio](search-security-api-keys.md)

- [Come ottenere un ruolo della directory in Azure Active Directory (Azure AD) con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** Ricerca cognitiva non ha il concetto di account amministratore di Azure Active Directory o Azure Active Directory (Azure AD) che possono essere usati per gestire indici e operazioni. 

Usare il Azure AD ruoli predefiniti che devono essere assegnati in modo esplicito per le operazioni di gestione. Richiamare il Azure AD powershell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come usare i ruoli per l'accesso amministrativo in Ricerca cognitiva](search-security-rbac.md)

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni:** usare l'autenticazione SSO con Azure Active Directory (Azure AD) per accedere alle informazioni del servizio di ricerca per le operazioni di gestione supportate tramite Azure Resource Manager. 

Stabilire un processo per ridurre il numero di identità e credenziali abilitando l'accesso SSO per il servizio con le identità preesiste dell'organizzazione.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee** guida: abilitare Azure Active Directory funzionalità di autenticazione a più fattori di Azure AD (Azure AD) e seguire le raccomandazioni relative all'identità e all'accesso del Centro sicurezza.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare una workstation PAW (Privileged Access Workstation) con autenticazione a più fattori configurata per accedere alle risorse di Azure e accedervi.
 

 
- [Informazioni sulle workstation protette gestite da Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Come abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory report di sicurezza (Azure AD) e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza per monitorare l'identità e l'attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le attività di gestione del livello di servizio in Ricerca cognitiva di Azure. Azure AD identità non concedono l'accesso all'endpoint del servizio di ricerca.  L'accesso a operazioni quali la gestione degli indici, il popolamento degli indici e le query sui dati di ricerca è disponibile tramite chiavi API.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Creare e gestire chiavi API per un Ricerca cognitiva di Azure servizio](search-security-api-keys.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. Usare le Azure AD delle identità e delle verifiche di accesso per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere verificato regolarmente per assicurarsi che solo gli utenti autorizzati possano continuare ad accedere. 

Esaminare i log di diagnostica di Ricerca cognitiva per l'attività nell'endpoint del servizio di ricerca, ad esempio la gestione degli indici, il popolamento degli indici e le query.

- [Informazioni sulla creazione Azure AD report](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Monitorare le operazioni e l'attività dei Ricerca cognitiva di Azure](search-monitor-usage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** Accesso ad Azure Active Directory di log eventi di Azure AD(Azure AD), di controllo e di rischio, consentono di eseguire l'integrazione con qualsiasi strumento di monitoraggio o informazioni di sicurezza e gestione degli eventi.

Semplificare questo processo creando impostazioni di diagnostica per Azure AD utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure con Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni:** usare Azure Active Directory (Azure AD) di Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inserire i dati in Azure Sentinel per un'analisi più approfondita, in base alle esigenze.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette all'interno di un gruppo di sicurezza di rete o Firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. Usare Collegamento privato per configurare un endpoint privato in Ricerca cognitiva.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare un endpoint privato per Ricerca cognitiva](service-create-private-endpoint.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: usare una soluzione di terze parti Azure Marketplace nei perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti, inviando avvisi ai professionisti della sicurezza delle informazioni.

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti sensibili e protegge dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per Ricerca cognitiva. Implementare una soluzione di terze parti, se necessario per motivi di conformità. 

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti sensibili e protegge dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Linee** guida: per l'amministrazione del servizio, usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle chiavi e alla configurazione. Per le operazioni sul contenuto, ad esempio l'indicizzazione e le query, Ricerca cognitiva usa chiavi anziché un modello di controllo di accesso basato sull'identità. Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle chiavi. 

 
- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Come usare i ruoli per l'accesso amministrativo a Ricerca cognitiva](search-security-rbac.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee** guida: Ricerca cognitiva crittografa automaticamente il contenuto indicizzato in pausa con chiavi gestite da Microsoft. Se è necessaria una maggiore protezione, è possibile integrare la crittografia predefinita con un secondo livello di crittografia usando le chiavi create e gestite in Azure Key Vault.

- [Configurare le chiavi gestite dal cliente per la crittografia dei dati in Ricerca cognitiva di Azure](search-security-manage-encryption-keys.md)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle istanze di produzione di Ricerca cognitiva e ad altre risorse critiche o correlate. 

 
- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Come creare avvisi per le attività di Ricerca cognitiva](search-monitor-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) nelle sottoscrizioni.

Verificare le autorizzazioni appropriate (lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse nelle sottoscrizioni.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle risorse di Azure con metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni:** definire un elenco di risorse di Azure approvate correlate all'indicizzazione e all'elaborazione del set di competenze in Ricerca cognitiva.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee** guida: è consigliabile definire un inventario delle risorse di Azure approvate per l'utilizzo in base ai criteri e agli standard aziendali precedenti, quindi monitorare le risorse di Azure non approvate con Criteri di Azure o Azure Resource Graph.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.  

 
Controllare l'accesso alle chiavi usate per autenticare le richieste per tutte le altre operazioni, in particolare quelle correlate al contenuto con Ricerca cognitiva.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Search" per creare criteri personalizzati per controllare o applicare la configurazione delle risorse Ricerca cognitiva di Azure personalizzate. È anche possibile usare definizioni Criteri di Azure per i servizi di Ricerca cognitiva, ad esempio:

Abilitare la registrazione di controllo per le risorse di Azure

Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni di Centro sicurezza di Azure come baseline di configurazione sicura per le risorse di Azure.

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](security-controls-policy.md)

- [Come visualizzare gli alias Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee** guida: usare Criteri di Azure gli effetti [nega] e [distribuisci se non esiste] per applicare impostazioni sicure tra le risorse servizio di ricerca cognitive. 

Azure Resource Manager modelli possono essere usati per mantenere la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](security-controls-policy.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di Criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/index)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse cognitive servizio di ricerca usando Criteri di Azure. 

Usare gli alias per creare criteri personalizzati per controllare o applicare le configurazioni di rete. È anche possibile usare le definizioni di criteri predefiniti correlate alle risorse specifiche. 

Inoltre, è possibile usare Automazione di Azure per distribuire le modifiche di configurazione e gestire le eccezioni dei criteri. 

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](security-controls-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare il Centro sicurezza per eseguire analisi di base delle risorse di servizio di ricerca cognitive.  È anche possibile usare Criteri di Azure avvisi e controllare le configurazioni delle risorse. 

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](security-controls-policy.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare identità gestite di Azure in combinazione con Azure Key Vault per semplificare la gestione dei segreti per le applicazioni cloud.

- [Come usare le identità gestite per le risorse di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Come creare un Key Vault](../key-vault/general/quick-create-portal.md)

- [Come fornire l'Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare un'identità gestita di Azure per concedere a Ricerca cognitiva l'accesso ad altri servizi di Azure, ad esempio origini dati Key Vault e indicizzatore, usando un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice. 

- [Configurare una connessione dell'indicizzatore a un'origine dati usando un'identità gestita](search-howto-managed-identities-data-sources.md)

- [Configurare le chiavi gestite dal cliente per la crittografia dei dati usando un'identità gestita](search-security-manage-encryption-keys.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee** guida: eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio Ricerca cognitiva, Archiviazione BLOB, database SQL di Azure e così via. 

È responsabilità dell'utente eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware del contenuto del cliente per conto dell'utente.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assicurarsi che il software antimalware e le firme siano aggiornati

**Linee guida:** non applicabile a Ricerca cognitiva. Non consente l'installazione di soluzioni antimalware nelle risorse. Per la piattaforma sottostante Microsoft gestisce l'aggiornamento di qualsiasi software e firma antimalware.  

 
Per tutte le risorse di calcolo di proprietà dell'organizzazione e usate nella soluzione di ricerca, seguire le raccomandazioni nel Centro sicurezza, App di calcolo per assicurarsi che tutti gli endpoint siano aggiornati con le &amp; firme più recenti. Per Linux, usare una soluzione antimalware di terze parti.

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni:** non è possibile eseguire il backup del contenuto archiviato in un servizio di ricerca tramite Backup di Azure o qualsiasi altro meccanismo predefinito, ma è possibile ricompilare un indice dal codice sorgente dell'applicazione e dalle origini dati primarie oppure compilare uno strumento personalizzato per recuperare e archiviare il contenuto indicizzato. 

 
- [Esempio di index-backup-restore di GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** Ricerca cognitiva attualmente non supporta il backup automatico per i dati in un servizio di ricerca e deve essere sottoposto a backup tramite un processo manuale. È anche possibile eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.
 

- [Eseguire il backup e il ripristino di un Ricerca cognitiva di Azure dati](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Come eseguire il backup Key Vault chiavi in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** Ricerca cognitiva attualmente non supporta il backup automatico dei dati in un servizio di ricerca e deve essere sottoposto a backup e ripristino tramite un processo manuale. Eseguire periodicamente il ripristino dei dati del contenuto di cui è stato eseguito manualmente il backup per garantire l'integrità end-to-end del processo di backup.

- [Eseguire il backup e il ripristino di un Ricerca cognitiva di Azure dati](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Come ripristinare le Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** Ricerca cognitiva attualmente non supporta il backup automatico dei dati in un servizio di ricerca e deve essere sottoposto a backup tramite un processo manuale.  È anche possibile eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.  

 
Abilitare l'eliminazione e la protezione dall'eliminazione Key Vault proteggere le chiavi da eliminazioni accidentali o dannose. Se Archiviazione di Azure viene usato per archiviare i backup manuali, abilitare l'eliminazione automatica per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot blob. 
 

 
- [Eseguire il backup e il ripristino di un Ricerca cognitiva di Azure dati](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Come abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva in Key Vault](../storage/blobs/soft-delete-blob-overview.md) 

- [Eliminazione soft per l'archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Linee guida:** sviluppare una guida alla risposta agli eventi imprevisti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono tutti i ruoli del personale, nonché le fasi di gestione e gestione degli eventi imprevisti, dal rilevamento alla revisione post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può anche sfruttare la Guida alla gestione degli eventi imprevisti per la sicurezza informatica di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o sull'uso analitico per l'emissione dell'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

Contrassegnare inoltre le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili. È responsabilità dell'utente assegnare la priorità alla correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione di NIST , "Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities" (Guida ai programmi di test, training ed esercizio per i piani e le funzionalità IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni:** Esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità Esportazione continua. L'esportazione continua consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza per trasmettere gli avvisi Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni:** seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
