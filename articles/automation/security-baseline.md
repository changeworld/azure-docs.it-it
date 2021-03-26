---
title: Baseline della sicurezza di Azure per l'automazione
description: La linea di base di sicurezza di automazione fornisce le informazioni e le procedure per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea75b9c1e8c7a1a242406b5a7f73db1b05c70276
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562206"
---
# <a name="azure-security-baseline-for-automation"></a>Baseline della sicurezza di Azure per l'automazione

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) ad automazione di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ad automazione di Azure. I **controlli** non applicabili ad automazione di Azure sono stati esclusi.

 
Per informazioni sul mapping completo di automazione di Azure al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza di automazione di Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: l'account di automazione di Azure non supporta ancora il collegamento privato di Azure per limitare l'accesso al servizio tramite endpoint privati. Manuali operativi che eseguono l'autenticazione e l'esecuzione su risorse in Azure vengono eseguite in una sandbox di Azure e sfruttano le risorse back-end condivise, che Microsoft è responsabile dell'isolamento reciproco. la loro rete è senza restrizioni e può accedere alle risorse pubbliche. Automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Per ottenere un ulteriore isolamento per la manuali operativi, è possibile usare i ruoli di lavoro ibridi per Runbook in esecuzione in macchine virtuali di Azure. Quando si crea una macchina virtuale di Azure, è necessario creare una rete virtuale (VNet) o usare un VNet esistente e configurare le VM con una subnet. Assicurarsi che tutte le subnet distribuite dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici per le porte e le origini attendibili. Per i requisiti specifici del servizio, fare riferimento alle raccomandazioni sulla sicurezza per il servizio specifico. In alternativa, se si ha un requisito specifico, è possibile usare anche il firewall di Azure per soddisfarlo.

- [Reti virtuali e macchine virtuali in Azure](../virtual-machines/network-overview.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Ambiente di esecuzione dei runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: automazione di Azure attualmente non dispone dell'integrazione della rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, assicurarsi che la subnet contenente i ruoli di lavoro sia abilitata con un gruppo di sicurezza di rete (NSG) e configurare i log di flusso per l'invio di log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Sebbene le regole di NSG e le route definite dall'utente non si applichino all'endpoint privato, i log di flusso e le informazioni di monitoraggio di NSG per le connessioni in uscita sono ancora supportati e possono essere usati.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, abilitare la protezione standard DDoS (Distributed Denial of Service) nelle reti virtuali che ospitano i ruoli di lavoro ibridi per Runbook per proteggersi da attacchi DDoS. Con l'Intelligence per le minacce integrata nel centro sicurezza di Azure, è possibile negare le comunicazioni con indirizzi IP dannosi noti.  Configurare il firewall di Azure in ogni segmento di rete virtuale, con l'Intelligence per le minacce abilitata, e configurare in modo da **avvisare e negare** il traffico di rete dannoso.

È possibile usare l'accesso just-in-time alla rete del Centro sicurezza di Azure per limitare l'esposizione delle macchine virtuali Windows agli indirizzi IP approvati per un periodo di tempo limitato.  Usare anche le raccomandazioni per la protezione avanzata della rete adattiva del Centro sicurezza di Azure per le configurazioni NSG per limitare le porte e gli indirizzi IP di origine basati sul traffico effettivo e sull'Intelligence per le minacce

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../security-center/security-center-adaptive-network-hardening.md)

- [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: automazione di Azure attualmente non dispone di integrazione della rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook, questo controllo non è applicabile se si usa il servizio predefinito senza ruoli di lavoro ibridi.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, è possibile registrare i log del flusso di NSG in un account di archiviazione per generare record di flusso per le macchine virtuali di Azure che fungono da Runbook Worker. Quando si analizza un'attività anomala, è possibile abilitare Network Watcher acquisizione pacchetti in modo che il traffico di rete possa essere esaminato per attività insolite e impreviste.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook ospitati in macchine virtuali di Azure, è possibile combinare le acquisizioni di pacchetti fornite da Network Watcher e gli strumenti di ID open source per eseguire il rilevamento delle intrusioni di rete per un'ampia gamma di minacce per i computer di lavoro. Inoltre, è possibile distribuire il firewall di Azure ai segmenti della rete virtuale in base alle esigenze, con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

- [Eseguire il rilevamento di intrusioni di rete con Network Watcher e strumenti open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato in Azure, che richiedono l'accesso alle risorse di automazione. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, GuestAndHybridManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete usate da automazione di Azure con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di rete. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: inviare i dati di log ai log di monitoraggio di Azure per aggregare i dati di sicurezza generati dalle risorse di automazione di Azure. In monitoraggio di Azure usare le query di log per cercare ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione. Automazione di Azure può inviare lo stato del processo Runbook, i flussi di processo, i dati di configurazione dello stato di automazione, la gestione degli aggiornamenti e Log Analytics il rilevamento delle modifiche o i log di inventario nell'area di lavoro Queste informazioni sono visibili dall'API dei log di monitoraggio portale di Azure, Azure PowerShell e Azure, che consente di eseguire semplici indagini.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md)

- [Integrare DSC con i log di monitoraggio di Azure](automation-dsc-diagnostics.md)

- [Aree supportate per l'area di lavoro Log Analytics collegata](how-to/region-mappings.md)

- [Log di query Gestione aggiornamenti](update-management/query-logs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare monitoraggio di Azure per l'accesso ai log di controllo e attività che includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 

- [Visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Dettagli sulla conservazione dei dati per gli account di automazione](./automation-managing-data.md#data-retention)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Usare le query del log di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informazioni sulle query di log in monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare il Centro sicurezza di Azure con monitoraggio di Azure per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di monitoraggio di Azure](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: usare i ruoli di amministratore predefiniti Azure Active Directory (Azure ad) che possono essere assegnati in modo esplicito e possono essere sottoposti a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. Ogni volta che si usano account RunAs dell'account di automazione per la manuali operativi, verificare che anche queste entità servizio siano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'account di automazione di Azure non ha il concetto di password predefinite.  I clienti sono responsabili per le applicazioni di terze parti e i servizi del Marketplace che possono usare password predefinite eseguite sul servizio o sui ruoli di lavoro ibridi per Runbook.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. Ogni volta che si usano account RunAs dell'account di automazione per la manuali operativi, verificare che anche queste entità servizio siano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Definire l'ambito di queste identità con le autorizzazioni con privilegi minimi necessari per consentire al manuali operativi di eseguire correttamente il processo automatizzato. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure Active Directory (Azure AD) Privileged Identity Management i ruoli con privilegi per i servizi Microsoft e Azure Resource Manager.

- [Altre informazioni su Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: laddove possibile, usare SSO con Azure Active Directory (Azure ad) invece di configurare singole credenziali autonome per ogni servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Accesso Single Sign-on alle applicazioni in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Usare Azure AD per l'autenticazione in Azure](automation-use-azure-ad.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare paw con autenticazione a più fattori configurato per accedere e configurare le risorse dell'account di automazione di Azure negli ambienti di produzione. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi 

**Linee guida**: usare i rilevamenti dei rischi Azure Active Directory (Azure ad) per visualizzare gli avvisi e i report sul comportamento utente rischioso. Facoltativamente, il cliente può inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure a monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Informazioni sui rilevamenti del rischio del Centro sicurezza di Azure (attività sospetta)](../active-directory/identity-protection/overview-identity-protection.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate 

**Linee guida**: è consigliabile usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare le località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. Se si usano i ruoli di lavoro ibridi per Runbook, è possibile sfruttare le identità gestite anziché gli account RunAs per abilitare autorizzazioni sicure più semplici.

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Usare l'autenticazione runbook con le identità gestite](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. Ogni volta che si usano account RunAs dell'account di automazione per manuali operativi, assicurarsi che anche queste entità servizio vengano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini dei log eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con qualsiasi strumento Siem/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory (Azure ad) per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente per la risorsa di rete. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: per gli account di automazione di Azure, il supporto tecnico Microsoft può accedere ai metadati delle risorse della piattaforma durante un caso di supporto aperto senza utilizzare un altro strumento.

Tuttavia, quando si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure e le terze parti devono accedere ai dati dei clienti (ad esempio durante una richiesta di supporto), usare Customer Lockbox (anteprima) per le macchine virtuali di Azure per rivedere e approvare o rifiutare le richieste di accesso ai dati del cliente.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di automazione di Azure che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Isolare gli ambienti usando risorse separate per gli account di automazione. Le risorse come i ruoli di lavoro ibridi per Runbook devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare avvisi o avvisi e negare con il firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usa la funzionalità Hybrid Runbook Worker, è consigliabile usare una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti, mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, tutti i contenuti dei clienti vengono considerati sensibili e protetti dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure in reti virtuali di Azure siano in grado di negoziare TLS 1,2 o versione successiva. Automazione di Azure supporta completamente e impone il protocollo TLS (Transport Layer) 1,2 e tutte le chiamate client o versioni successive per tutti gli endpoint HTPS esterni (tramite webhook, nodi DSC, ruolo di lavoro ibrido per Runbook).

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Applicazione TLS 1,2 di automazione di Azure](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli presenti in loco o presso un provider di servizi remoti e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso alle risorse di automazione di Azure usando le definizioni di ruolo predefinite, assegnare l'accesso per gli utenti che accedono alle risorse di automazione dopo un modello di accesso con privilegi minimi o "just-enough". Quando si usano i ruoli di lavoro ibridi per Runbook, è possibile sfruttare le identità gestite per le macchine virtuali per evitare di usare le entità servizio, quando si usano sia i ruoli di lavoro ibridi di Runbook multi-tenant che i ruoli di lavoro ibridi di Azure per l'identità dei Runbook Worker.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Gestire le autorizzazioni e la sicurezza dei ruoli](automation-role-based-access-control.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali del ruolo di lavoro Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, è necessario usare una soluzione di prevenzione della perdita dei dati basata su host di terze parti per applicare i controlli di accesso alle macchine virtuali del ruolo di lavoro ibrido per Runbook ospitato.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: usare chiavi gestite dal cliente con automazione di Azure. Automazione di Azure supporta l'uso di chiavi gestite dal cliente per crittografare tutti gli asset protetti usati, ad esempio credenziali, certificati, connessioni e variabili crittografate. Usare le variabili crittografate con la manuali operativi per tutte le richieste di ricerca variabili persistenti per evitare un'esposizione imprevista.

Quando si usano i ruoli di lavoro ibridi per Runbook, i dischi virtuali delle macchine virtuali vengono crittografati a riposo usando la crittografia lato server o la crittografia dischi di Azure (ADE). Crittografia dischi di Azure sfrutta la funzionalità BitLocker di Windows per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

- [Crittografia lato server di Azure Managed Disks](../virtual-machines/disk-encryption.md)

- [Crittografia dischi di Azure per macchine virtuali Windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Variabili gestite in automazione di Azure](shared-resources/variables.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle risorse di Azure critiche, ad esempio i componenti di rete, gli account di automazione di Azure e manuali operativi. 

- [Registrazione diagnostica per un gruppo di sicurezza di rete](../private-link/private-link-overview.md#logging-and-monitoring)

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle risorse di Azure

- [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-recommendations.md)

- [Riferimento per le raccomandazioni del Centro sicurezza](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Quando si usa la raccomandazione sulla gestione delle vulnerabilità suggerita dal centro sicurezza di Azure, il cliente può passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure per definire la priorità della correzione delle vulnerabilità individuate.

- [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](../security-center/secure-score-security-controls.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse di automazione di Azure all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di automazione di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Eliminare un account RunAs o un account RunAs classico](delete-run-as-account.md)

- [Gestire un account RunAs di Automazione di Azure](manage-runas-account.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti: 

- Tipi di risorse non consentiti 
- Tipi di risorse consentiti 

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questo può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Esempi di criteri di Azure predefiniti per automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: il cliente può impedire la creazione o l'utilizzo di risorse con criteri di Azure come richiesto dalle linee guida aziendali del cliente. È possibile implementare un processo personalizzato per la rimozione di risorse non autorizzate. All'interno dell'offerta di automazione di Azure è possibile installare, rimuovere e gestire i moduli di PowerShell o Python a cui manuali operativi può accedere tramite il portale o i cmdlet. Il modulo non approvato o precedente deve essere rimosso o aggiornato per manuali operativi.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Gestire il modulo in automazione di Azure](shared-resources/modules.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare i criteri di accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure" da percorsi o dispositivi non protetti o non approvati. 

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di automazione di Azure e le risorse correlate. È anche possibile usare le definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure predefiniti per automazione di Azure](policy-reference.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare modelli di Azure Resource Manager e criteri di Azure per configurare in modo sicuro le risorse di Azure associate ad automazione di Azure. Azure Resource Manager modelli sono file basati su JSON usati per distribuire le risorse di Azure. tutti i modelli personalizzati dovranno essere archiviati e gestiti in modo sicuro in un repository di codice. Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

- [Informazioni sulla creazione di modelli di Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Distribuire un account di automazione usando un modello di Azure Resource Manager](/azure/automation/quickstart-create-automation-account-template)

- [Esempi di criteri di Azure predefiniti per automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS. Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Esempi di criteri di Azure predefiniti per automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse di Azure. i criteri possono essere usati per rilevare determinate risorse non configurate con un endpoint privato.

Quando si usa la funzionalità Hybrid Runbook Worker, sfruttare il Centro sicurezza di Azure per eseguire analisi di base per le macchine virtuali di Azure.  I metodi aggiuntivi per la configurazione automatica includono: configurazione dello stato di automazione di Azure.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure predefiniti per automazione di Azure](policy-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: usare Azure Resource Manager per distribuire gli account di automazione di Azure e le risorse correlate. Azure Resource Manager offre la possibilità di esportare i modelli che possono essere usati come backup per ripristinare gli account di automazione di Azure e le risorse correlate. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari.

Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Riferimento al modello di Azure Resource Manager per le risorse di automazione di Azure](/azure/templates/microsoft.automation/allversions)

- [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione ad automazione di Azure](automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: usare Azure Resource Manager per distribuire gli account di automazione di Azure e le risorse correlate. Azure Resource Manager offre la possibilità di esportare i modelli che possono essere usati come backup per ripristinare gli account di automazione di Azure e le risorse correlate. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari. Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault. È possibile esportare i manuali operativi in file di script usando portale di Azure o PowerShell.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Riferimento al modello di Azure Resource Manager per le risorse di automazione di Azure](/azure/templates/microsoft.automation/allversions)

- [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione ad automazione di Azure](automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Backup dei dati di Azure per gli account di automazione](./automation-managing-data.md#data-backup)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari con una sottoscrizione isolata, se necessario. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Distribuire le risorse con i modelli ARM e portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Uso delle chiavi gestite dal cliente per un account di Automazione](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice come modelli Azure Resource Manager. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrato con TFS.

Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Usare l'integrazione del controllo del codice sorgente](source-control-integration.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza 

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
