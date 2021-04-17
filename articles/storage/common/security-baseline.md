---
title: Baseline di sicurezza di Azure per Archiviazione di Azure
description: La Archiviazione di Azure di sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589449"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Baseline di sicurezza di Azure per Archiviazione di Azure

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../../security/benchmarks/overview-v1.md) a Archiviazione di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Archiviazione di Azure. **I** controlli non applicabili Archiviazione di Azure sono stati esclusi.

 
Per informazioni su come Archiviazione di Azure completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Archiviazione di Azure [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** configurare il firewall dell'account di archiviazione limitando l'accesso ai client da intervalli di indirizzi IP pubblici specifici, selezionare reti virtuali o risorse di Azure specifiche.  È anche possibile configurare endpoint privati in modo che il traffico verso il servizio di archiviazione dall'azienda viaggi esclusivamente su reti private.

Nota: gli account di archiviazione classici non supportano firewall e reti virtuali.

- [Come configurare il firewall Archiviazione di Azure rete](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Come configurare gli endpoint privati per Archiviazione di Azure](storage-private-endpoints.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: Archiviazione di Azure un modello di sicurezza a più livelli. È possibile limitare l'accesso all'account di archiviazione alle richieste provenienti da indirizzi IP, intervalli IP specificati o da un elenco di subnet in una rete virtuale di Azure. È possibile usare le Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione della rete per proteggere le risorse di rete in Azure. Abilitare anche i log dei flussi dei gruppi di sicurezza di rete per le reti virtuali o la subnet configurata per gli account di archiviazione tramite il firewall dell'account di archiviazione e inviare i log a un account di archiviazione per il controllo del traffico. 

 
Si noti che se all'account di archiviazione sono collegati endpoint privati, non è possibile configurare le regole del gruppo di sicurezza di rete per le subnet. 
 

 
- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md) 
 

 
- [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md) 
 

 
- [Informazioni sugli endpoint privati per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni:** non applicabile; La raccomandazione è destinata alle applicazioni Web in esecuzione Servizio app di Azure risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare la Azure Defender archiviazione per l'account Archiviazione di Azure personale. Azure Defender per Archiviazione offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione.  Centro sicurezza di Azure avvisi integrati sono basati su attività per le quali la comunicazione di rete è stata associata a un indirizzo IP risolto correttamente, indipendentemente dal fatto che l'indirizzo IP sia o meno un indirizzo IP rischioso noto (ad esempio, un cryptominer noto) o un indirizzo IP non riconosciuto in precedenza come rischioso. Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. 

- [Configurare Azure Defender per Archiviazione](azure-defender-storage-configure.md) 

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** Network Watcher'acquisizione di pacchetti consente di creare sessioni di acquisizione per tenere traccia del traffico tra l'account di archiviazione e una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri usi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione su una macchina virtuale desiderata, consentendo un notevole risparmio di tempo. 

- [ Gestire le acquisizioni di pacchetti con Azure Network Watcher tramite il portale](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee** guida: Azure Defender per l'archiviazione offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi di sicurezza sono integrati con il Centro sicurezza di Azure e vengono anche inviati tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare le minacce e difendersi da esse. 

- [Configurare Azure Defender per Archiviazione](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida:** non applicabile; la raccomandazione è destinata alle applicazioni Web in esecuzione Servizio app di Azure risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee** guida: per le risorse nelle reti virtuali che devono accedere all'account di archiviazione, usare i tag del servizio di rete virtuale per la rete virtuale configurata per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nelle Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio Archiviazione) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

Quando l'accesso alla rete deve essere limitato a account di archiviazione specifici, usare i criteri degli endpoint di servizio di rete virtuale.

- [Per altre informazioni sull'uso dei tag di servizio](../../virtual-network/service-tags-overview.md)

- [Per altre informazioni sui criteri degli endpoint di servizio di rete virtuale per Archiviazione di Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate all'account Archiviazione di Azure con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.Storage" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse dell'account di archiviazione. 

È anche possibile usare le definizioni dei criteri predefiniti correlate all'account di archiviazione, ad esempio: Gli account di archiviazione devono usare un endpoint di servizio di rete virtuale 

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md) 

- [Criteri di Azure esempi per l'archiviazione](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Criteri di Azure esempi per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Come creare un progetto di Azure](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. L'assegnazione di tag consente di associare coppie nome-valore incorporate e personalizzate a una determinata risorsa di rete, consentendo di organizzare le risorse di rete e di correlare le risorse di Azure alla progettazione della rete.

Usare una delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag. 

I gruppi di sicurezza di rete supportano i tag, ma non le singole regole di sicurezza. Le regole di sicurezza hanno **un campo** Descrizione che è possibile usare per archiviare alcune delle informazioni che normalmente si inserirebbero in un tag.

- [Come creare e usare i tag delle risorse](../../azure-resource-manager/management/tag-resources.md)

- [Come filtrare il traffico di rete con un gruppo di sicurezza di rete](../../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare Criteri di Azure per registrare le modifiche di configurazione per le risorse di rete.  In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.  

 
- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Come creare avvisi in Monitoraggio di Azure](../../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire i log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine/archiviazione, facoltativamente con funzionalità di sicurezza come l'archiviazione non modificabile e i blocchi di conservazione applicati.

 
- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** Analisi archiviazione di Azure log per BLOB, code e tabelle. È possibile usare il portale di Azure per configurare quali log vengono registrati per l'account.   

 
- [Come configurare il monitoraggio per l'account Archiviazione di Azure](manage-storage-analytics-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** quando si archiviano i log eventi di sicurezza nell'account Archiviazione di Azure o nell'area di lavoro Log Analytics, è possibile impostare i criteri di conservazione in base ai requisiti dell'organizzazione.  

 
- [Come configurare i criteri di conservazione per i log Archiviazione di Azure account](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** per esaminare i log Archiviazione di Azure, sono disponibili le opzioni consuete, ad esempio le query tramite l'offerta Log Analytics, nonché un'opzione univoca per la visualizzazione diretta dei file di log. In Archiviazione di Azure, i log vengono archiviati in BLOB a cui è necessario accedere direttamente in .La cartella di registrazione è nascosta per impostazione predefinita, quindi sarà necessario `http://accountname.blob.core.windows.net/$logs` spostarsi direttamente. Non verrà visualizzato nei comandi Elenco) 

 
Abilitare anche l'Azure Defender per l'archiviazione per l'account di archiviazione. Azure Defender per Archiviazione offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi di sicurezza sono integrati con il Centro sicurezza di Azure e vengono anche inviati tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare le minacce e difendersi da esse. 
 

 
- [Registrare ed esaminare i dati](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Configurare Azure Defender per Archiviazione](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** in Centro sicurezza di Azure abilitare Azure Defender per l'account di archiviazione. Abilitare le impostazioni di diagnostica per l'account di archiviazione e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione SOAR (Security Orchestration Automated Response). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarli per risolvere i problemi di sicurezza.  

 
- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Come gestire gli avvisi in Centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Come avvisare i dati del log di Log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Registrazione di Analisi archiviazione di Azure](storage-analytics-logging.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni:** usare il Centro sicurezza di Azure e abilitare Azure Defender for Storage per rilevare i caricamenti di malware in Archiviazione di Azure usando l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita Tor attivo (un proxy anonimo). 

- [Configurare Azure Defender per Archiviazione](azure-defender-storage-configure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee** guida: DNS di Azure Analytics (anteprima) in Monitoraggio di Azure raccoglie informazioni dettagliate sull'infrastruttura DNS su sicurezza, prestazioni e operazioni.  Attualmente non supporta gli account Archiviazione di Azure, tuttavia è possibile usare una soluzione di registrazione DNS di terze parti.  

 
- [Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione DNS Analytics (anteprima)](../../azure-monitor/insights/dns-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono disponibili per query. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** Archiviazione di Azure account e Azure Active Directory (Azure AD) hanno il concetto di password predefinite o vuote. Archiviazione di Azure implementa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure, nonché le firme di accesso condiviso e chiave condivisa. Una caratteristica dell'autenticazione con chiave condivisa e firma di accesso condiviso è che nessuna identità è associata al chiamante e pertanto non è possibile eseguire l'autorizzazione basata sulle autorizzazioni dell'entità di sicurezza.

- [Autorizzazione dell'accesso ai dati in Archiviazione di Azure](storage-auth.md)

- [Informazioni sulle entità di sicurezza e sul controllo di accesso Archiviazione di Azure'account](storage-introduction.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso all'account di archiviazione. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un accesso JUST-In-Time/Just-Enough usando i ruoli con privilegi di Azure Active Directory (Azure AD) Privileged Identity Management per i servizi Microsoft e Azure Resource Manager.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management panoramica](/azure/active-directory/privileged-identity-management/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare Azure Active Directory (Azure AD) SSO invece di configurare singole credenziali autonome per ogni servizio. Usare Centro sicurezza di Azure indicazioni su Gestione identità e accessi.
 

 
- [Informazioni sull'accesso SSO con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Autorizzazione dell'accesso ai dati in Archiviazione di Azure](storage-auth.md)
 

 
- [Autorizzare l'accesso a BLOB e code usando Azure AD](storage-auth-aad.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Linee** guida: abilitare Azure Active Directory (Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni sulla gestione delle identità e degli accessi per proteggere le risorse dell'account di archiviazione.

- [Come abilitare l'autenticazione a più fattori in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usare workstation protette gestite da Azure per attività amministrative

**Linee** guida: usare paw (workstation di accesso con privilegi) con autenticazione a più fattori configurata per accedere e configurare le risorse dell'account di archiviazione.  

 
- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Come abilitare l'autenticazione a più fattori in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Indicazioni:** Inviare Centro sicurezza di Azure avvisi di rilevamento dei rischi Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando gruppi di azioni. Abilitare Azure Defender per l'account di archiviazione per generare avvisi per attività sospette. Usare anche i Azure Active Directory (Azure AD) per visualizzare avvisi e report sul comportamento degli utenti a rischio.

- [Configurare Azure Defender per Archiviazione](azure-defender-storage-configure.md)
 

- [Informazioni sui rilevamenti di rischi di Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Come configurare i gruppi di azioni per avvisi e notifiche personalizzati](../../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** usare percorsi denominati di accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare località denominate in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure offre il controllo degli accessi in base al ruolo di Azure per un controllo con granularità fine sull'accesso di un client alle risorse in un account di archiviazione.   Usare Azure AD credenziali quando possibile come procedura di sicurezza consigliata, anziché usare la chiave dell'account, che può essere compromessa più facilmente. Quando la progettazione dell'applicazione richiede firme di accesso condiviso per l'accesso all'archiviazione BLOB, usare le credenziali Azure AD per creare una firma di accesso condiviso di delega utente quando possibile per una maggiore sicurezza.

- [Come creare e configurare un'istanza di Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usare il provider Archiviazione di Azure risorse per accedere alle risorse di gestione](authorization-resource-provider.md)

- [Come configurare l'accesso ai dati di BLOB e code di Azure con il controllo degli accessi in base al ruolo di Azure in portale di Azure](storage-auth-aad-rbac-portal.md)

- [Autorizzazione dell'accesso ai dati in Archiviazione di Azure](storage-auth.md)

- [Concedere l'accesso limitato Archiviazione di Azure risorse usando firme di accesso condiviso](storage-sas-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** esaminare i log Azure Active Directory (Azure AD) per individuare gli account non obsoleti che possono includere quelli con ruoli amministrativi dell'account di archiviazione. Usare anche le verifiche di accesso delle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere alle risorse dell'account di archiviazione e le assegnazioni di ruolo. L'accesso utente deve essere verificato regolarmente per assicurarsi che solo gli utenti con diritti di accesso continuino ad accedere. 
 

 
È anche possibile usare la firma di accesso condiviso per fornire l'accesso delegato sicuro alle risorse nell'account di archiviazione senza compromettere la sicurezza dei dati. È possibile controllare le risorse a cui il client può accedere, quali autorizzazioni hanno per tali risorse e per quanto tempo la firma di accesso condiviso è valida, tra gli altri parametri.
 

 
Esaminare anche l'accesso in lettura anonimo a contenitori e BLOB. Per impostazione predefinita, un contenitore e i BLOB al suo interno sono accessibili solo a un utente a cui sono state concesse le autorizzazioni appropriate. È possibile usare Monitoraggio di Azure per avvisare l'accesso anonimo per gli account di archiviazione usando una condizione di autenticazione anonima.
 

 
Un modo efficace per ridurre il rischio di accesso non specificato all'account utente è limitare la durata dell'accesso concesso agli utenti. Gli URI di firma di accesso condiviso con limiti di tempo sono un modo efficace per scadere automaticamente l'accesso utente a un account di archiviazione. Inoltre, la rotazione frequente delle chiavi dell'account di archiviazione è un modo per garantire che l'accesso imprevisto tramite le chiavi dell'account di archiviazione sia di durata limitata.
 

 
- [Informazioni Azure AD reporting](/azure/active-directory/reports-monitoring/) 
 

 
- [Come visualizzare e modificare l'accesso a Archiviazione di Azure account](storage-auth-aad-rbac-portal.md)
 

 
- [Concedere l'accesso limitato Archiviazione di Azure risorse usando firme di accesso condiviso](storage-sas-overview.md)
 

 
- [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/anonymous-read-access-configure.md)
 

 
- [Monitorare un account di archiviazione nel portale di Azure](manage-storage-analytics-logs.md)
 

 
- [Gestire le chiavi di accesso dell'account di archiviazione](storage-account-keys-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Linee** guida: usare Analisi archiviazione per registra informazioni dettagliate sulle richieste riuscite e non riuscite a un servizio di archiviazione. Tutti i log vengono archiviati in BLOB in blocchi all'interno di un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata l'Analisi archiviazione per un account di archiviazione.
 

Creare impostazioni di diagnostica per Azure Active Directory (Azure AD), inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

Per monitorare gli errori di autenticazione Archiviazione di Azure account, è possibile creare avvisi per notificare quando sono state raggiunte determinate soglie per le metriche delle risorse di archiviazione. È anche possibile usare Monitoraggio di Azure per avvisare l'accesso anonimo per gli account di archiviazione usando una condizione di autenticazione anonima.

- [Registrazione di Analisi archiviazione di Azure](storage-analytics-logging.md)
 

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Come configurare gli avvisi delle metriche per Archiviazione di Azure account](manage-storage-analytics-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare Azure Active Directory funzionalità Di rischio e Identity Protection di Azure Active Directory (Azure AD) per configurare risposte automatiche alle azioni sospette rilevate correlate alle risorse dell'account di archiviazione. È consigliabile abilitare le risposte automatizzate tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox (anteprima per l'account di archiviazione) fornisce un'interfaccia che consente ai clienti di esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti. Microsoft non richiederà né richiederà l'accesso ai segreti dell'organizzazione archiviati nell'account di archiviazione.

- [Informazioni Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag per tenere traccia delle risorse dell'account di archiviazione che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare l'isolamento usando sottoscrizioni, gruppi di gestione e account di archiviazione separati per singoli domini di sicurezza, ad esempio l'ambiente e la riservatezza dei dati.  È possibile limitare l'account di archiviazione per controllare il livello di accesso agli account di archiviazione richiesti dalle applicazioni e dall'ambiente aziendale, in base al tipo e al subset di reti usate. Quando le regole di rete sono configurate, solo le applicazioni che richiedono dati sul set di reti specificato possono accedere a un account di archiviazione. È possibile controllare l'accesso ai Archiviazione di Azure tramite il controllo degli accessi in base al ruolo di Azure.

È anche possibile configurare endpoint privati per migliorare la sicurezza quando il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

- [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md)

- [Endpoint servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** per le risorse dell'account di archiviazione che archiviano o elaborano informazioni riservate, contrassegnare le risorse come sensibili usando tag. Per ridurre il rischio di perdita di dati tramite esfiltrazione, limitare il traffico di rete in uscita per Archiviazione di Azure account usando Firewall di Azure.  

Usare anche i criteri degli endpoint servizio di rete virtuale per filtrare il traffico di rete virtuale in uscita verso gli account Archiviazione di Azure tramite l'endpoint di servizio e consentire l'esfiltrazione dei dati solo ad account Archiviazione di Azure specifici.

- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Criteri degli endpoint servizio di rete virtuale per Archiviazione di Azure](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** è possibile imporre l'uso di HTTPS abilitando Trasferimento sicuro necessario per l'account di archiviazione. Se l'opzione è abilitata, le connessioni che usano il protocollo HTTP verranno rifiutate.  È anche possibile usare Centro sicurezza di Azure e Criteri di Azure per applicare il trasferimento sicuro per l'account di archiviazione.

- [Come richiedere il trasferimento sicuro in Archiviazione di Azure](storage-require-secure-transfer.md)

- [Criteri di sicurezza di Azure monitorati dal Centro sicurezza](../../security-center/policy-reference.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione dei dati non sono ancora disponibili Archiviazione di Azure'account e le risorse correlate. Implementare una soluzione di terze parti, se necessaria ai fini della conformità. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee** guida: Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il controllo degli accessi in base al ruolo di Azure. Archiviazione di Azure definisce un set di ruoli controllo degli accessi in base al ruolo predefiniti di Azure che includono set comuni di autorizzazioni usati per accedere ai dati blob o di coda.

- [Come assegnare i ruoli del controllo degli accessi in base al ruolo di Azure per Archiviazione di Azure account](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Usare il provider Archiviazione di Azure per accedere alle risorse di gestione](authorization-resource-provider.md)

- [Come configurare l'accesso ai dati BLOB e code di Azure con il controllo degli accessi in base al ruolo di Azure in portale di Azure](storage-auth-aad-rbac-portal.md)

- [Come creare e configurare un'istanza di Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizzazione dell'accesso ai dati in Archiviazione di Azure](storage-auth.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** la Archiviazione di Azure è abilitata per tutti gli account di archiviazione e non può essere disabilitata. Archiviazione di Azure crittografa automaticamente i dati quando vengono resi persistenti nel cloud. Quando i dati vengono letti da Archiviazione di Azure, vengono decrittografati dal servizio di archiviazione prima di essere restituiti. Archiviazione di Azure la crittografia consente di proteggere i dati in stato di inquieto senza dover modificare il codice o aggiungere codice alle applicazioni. 

- [Informazioni sulla Archiviazione di Azure dei dati in stato di inalter](storage-service-encryption.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle risorse dell'account di archiviazione.  È anche possibile abilitare la Archiviazione di Azure per tenere traccia del modo in cui ogni richiesta effettuata Archiviazione di Azure è stata autorizzata. I log indicano se una richiesta è stata effettuata in modo anonimo, usando un token OAuth 2.0, usando la chiave condivisa o una firma di accesso condiviso.  È anche possibile usare Monitoraggio di Azure per avvisare sull'accesso anonimo per gli account di archiviazione usando una condizione di autenticazione anonima. 

 
- [Come creare avvisi per gli eventi del log attività di Azure](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Registrazione di Analisi archiviazione di Azure](storage-analytics-logging.md) 

 
- [Come configurare gli avvisi delle metriche per Archiviazione di Azure account](manage-storage-analytics-logs.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** seguire le raccomandazioni Centro sicurezza di Azure per controllare e monitorare continuamente la configurazione degli account di archiviazione.  

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni:** non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano gli account di archiviazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni di rischio predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure. 

- [Informazioni Centro sicurezza di Azure Secure Score](/azure/security-center/security-center-secure-score)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi gli account di archiviazione) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle risorse dell'account di archiviazione che danno metadati per organizzarli in modo logico in una tassonomia. 

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia degli account di archiviazione e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. 

Usare anche Azure Defender per l'archiviazione per rilevare risorse di Azure non autorizzate. 

- [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md) 

- [Come creare gruppi di gestione](../../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

- [Configurare Azure Defender per Archiviazione](azure-defender-storage-configure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni:** è necessario creare un inventario delle risorse di Azure approvate in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti: 

 
 - Tipi di risorse non consentiti 
 
 - Tipi di risorse consentiti 
 

 
Usare inoltre il Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Ciò può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione. 
 

 
- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Come creare query con Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee** guida: il cliente può impedire la creazione o l'utilizzo delle Criteri di Azure come richiesto dai criteri aziendali del cliente. 

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefiniti seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Criteri di Azure definizioni incorporate - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente a sicurezza elevata, ad esempio quelle con account di archiviazione. 

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi **Microsoft.Storage** per creare criteri personalizzati per controllare o applicare la configurazione delle istanze dell'account di archiviazione. È anche possibile usare definizioni di Criteri di Azure per Archiviazione di Azure account, ad esempio:

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Distribuire Azure Defender per l'archiviazione
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato

Usare le raccomandazioni Centro sicurezza di Azure come linea di base di configurazione sicura per gli account di archiviazione.

- [Come visualizzare gli alias Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** usare Criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse dell'account di archiviazione. 

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md) 

- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** usare Azure Repos per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati, modelli Azure Resource Manager, Desired State Configuration script e così via. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps o Azure AD se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** sfruttare Criteri di Azure avvisi, controllare e applicare configurazioni di sistema per l'account di archiviazione. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri. 

- [ Come configurare e gestire i Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Linee** guida: sfruttare Centro sicurezza di Azure per eseguire analisi di base per le Archiviazione di Azure dell'account. 

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** Archiviazione di Azure crittografa automaticamente i dati quando vengono resi persistenti nel cloud. È possibile usare le chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure gestire la crittografia con le proprie chiavi. Se si usano chiavi fornite dal cliente, è possibile sfruttare Azure Key Vault per archiviare in modo sicuro le chiavi. 

Ruotare inoltre le chiavi dell'account di archiviazione su base frequente per limitare l'impatto della perdita o della divulgazione delle chiavi dell'account di archiviazione.

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)

- [Gestire le chiavi di accesso dell'account di archiviazione](storage-account-keys-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** autorizzare l'accesso a BLOB e code all'interno di Archiviazione di Azure con Azure Active Directory (Azure AD) e identità gestite. Archiviazione BLOB e code di Azure supporta Azure AD autenticazione con identità gestite per le risorse di Azure. 

Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati blob e code usando le credenziali Azure AD dalle applicazioni in esecuzione in Macchine virtuali di Azure r, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con Azure AD autenticazione, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.

- [Come concedere l'accesso ai dati di BLOB e code di Azure usando un'identità gestita](storage-auth-aad-rbac-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [ Come configurare lo scanner delle credenziali](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** usare Azure Defender per l'archiviazione per rilevare i caricamenti di malware in Archiviazione di Azure usando l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita tor attivo (un proxy di anonimizzazione). 
 

 
È anche possibile eseguire l'analisi preliminare di qualsiasi contenuto alla ricerca di malware prima di caricarlo in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e altri, per soddisfare i requisiti dell'organizzazione.
 

 
- [Configurare Azure Defender per Archiviazione](azure-defender-storage-configure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** i dati nell'account Microsoft Azure di archiviazione vengono sempre replicati automaticamente per garantire durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente. 

È anche possibile abilitare Automazione di Azure per creare snapshot regolari dei BLOB.

- [Informazioni Archiviazione di Azure ridondanza e Service-Level contratti](storage-redundancy.md)

- [Creare uno snapshot di un BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Panoramica di Automazione di Azure](../../automation/automation-intro.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** per eseguire il backup dei dati dai servizi supportati dall'account di archiviazione, sono disponibili diversi metodi, tra cui l'uso di azcopy o di strumenti di terze parti. L'archiviazione non modificabile per BLOB di Azure consente agli utenti di archiviare oggetti dati critici per l'azienda nello stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente.
 

Le chiavi gestite dal cliente o fornite possono essere supportate all'interno di Azure Key Vault tramite l'interfaccia della riga di comando di Azure o PowerShell. 

 
- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)  

- [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** eseguire periodicamente il ripristino dei dati Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i comandi di PowerShell seguenti:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Come ripristinare i Key Vault certificati](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Come ripristinare Key Vault chiavi](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Come ripristinare Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare i Key Vault segreti](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB, file e dati di tabelle in o da un account di archiviazione](storage-use-azcopy-v10.md)

Nota: se si vogliono copiare dati da e verso il servizio di archiviazione tabelle di Azure, installare AzCopy versione 7.3.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** per abilitare le chiavi gestite dal cliente in un account di archiviazione, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare entrambe le proprietà Soft Delete e Do Not Purge nell'insieme di credenziali delle chiavi.  Key Vault funzionalità di eliminazione automatica consente il ripristino di insiemi di credenziali e oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati.  Se si esegue il backup dei dati dell'account di archiviazione Archiviazione di Azure BLOB, abilitare l'eliminazione soft per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB.  È consigliabile considerare i backup come dati sensibili e applicare i controlli di accesso e protezione dei dati pertinenti come parte di questa baseline.  Inoltre, per una protezione migliorata, è possibile archiviare gli oggetti dati business critical in uno stato WORM (Write Once, Read Many).

- [Come usare l'Azure Key Vault soft delete di Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/soft-delete-blob-overview.md)

- [Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](../blobs/storage-blob-immutable-storage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o nell'analisi usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso. 

 
Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.
 

 
- [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)
 

 
- [Usare tag per organizzare le risorse di Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità Automazione flusso di lavoro Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" per gli avvisi di sicurezza e le raccomandazioni per proteggere le risorse di Azure.
    

- [ Come configurare l'automazione del flusso di lavoro e app per la logica](../../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di microsoft engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft per il Red Teaming e i test di penetrazione del sito in tempo reale per l'infrastruttura cloud gestita da Microsoft, i servizi e le applicazioni.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
