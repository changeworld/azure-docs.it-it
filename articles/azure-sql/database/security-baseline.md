---
title: Baseline di sicurezza di Azure per database SQL di Azure
description: La database SQL di Azure di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29db5b82d73bf96465581ccd6a663455464bbeb9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599571"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Baseline di sicurezza di Azure per database SQL di Azure

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../../security/benchmarks/overview-v1.md) a database SQL di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a database SQL di Azure. **I** controlli non applicabili database SQL di Azure, o per i quali la responsabilità è di Microsoft, sono stati esclusi. Per informazioni su come database SQL di Azure mappato completamente a Azure Security Benchmark, vedere il file di mapping completo Monitoraggio di Azure baseline [di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** è possibile abilitare collegamento privato di Azure per consentire l'accesso ai servizi PaaS di Azure (ad esempio, database SQL) e ai servizi clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. 

Per consentire al traffico di raggiungere il database SQL di Azure, usare i tag di servizio SQL per consentire il traffico in uscita tramite gruppi di sicurezza di rete.

Le regole di rete virtuale consentono al database SQL di Azure di accettare solo le comunicazioni inviate da subnet specifiche all'interno di una rete virtuale.

- [Come configurare Collegamento privato per il database SQL di Azure](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [Come usare le regole e gli endpoint del servizio di rete virtuale per i server di database](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare Centro sicurezza di Azure e correggere le raccomandazioni sulla protezione di rete per la subnet in cui database SQL di Azure Server. 

Per le macchine virtuali di Azure che si connetteranno all'istanza di database SQL di Azure Server, abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) per i gruppi di sicurezza di rete che proteggono tali macchine virtuali e inviare i log in un account Archiviazione di Azure per il controllo del traffico. 

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

- [Come abilitare e usare Analisi del traffico](../../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare Protezione DDoS Standard nelle reti virtuali associate alle istanze di SQL Server per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare la protezione DDoS](/azure/virtual-network/manage-ddos-protection)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](/azure/security-center/security-center-alerts-data-services)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** per le macchine virtuali di Azure che si connetteranno all'istanza di database SQL di Azure, abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) per i gruppi di sicurezza di rete che proteggono tali macchine virtuali e inviare i log in un account Archiviazione di Azure per il controllo del traffico. Se necessario per l'analisi delle attività anoma Network Watcher l'acquisizione di pacchetti.

- [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** abilitare Advanced Threat Protection (ATP) per database SQL di Azure.  Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection integra anche gli avvisi con Centro sicurezza di Azure. 

- [Comprendere e usare Advanced Threat Protection per database SQL di Azure](/azure/sql-database/sql-database-threat-detection-overview)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Quando si usano endpoint di servizio per database SQL di Azure, sono necessari indirizzi IP pubblici in uscita verso database SQL di Azure: i gruppi di sicurezza di rete (NSG) devono essere aperti agli indirizzi IP database SQL di Azure per consentire la connettività. A tale scopo, è possibile usare i tag di servizio del gruppo di database SQL di Azure.

- [Informazioni su tag di servizio con endpoint di servizio per database SQL di Azure](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [Comprendere e usare i tag di servizio](../../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza di rete per le istanze database SQL di Azure server con Criteri di Azure. È possibile usare lo spazio dei nomi "Microsoft.Sql" per definire definizioni di criteri personalizzate oppure usare una delle definizioni di criteri incorporate progettate per la protezione della rete database SQL di Azure server. Un esempio di un criterio di sicurezza di rete predefinito applicabile per database SQL di Azure server è: "SQL Server deve usare un endpoint servizio di rete virtuale".

 

Usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio i modelli di Azure Resource Management, il controllo degli accessi in base al ruolo di Azure e i criteri, in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze database SQL di Azure server. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** abilitare il controllo per database SQL di Azure tenere traccia degli eventi del database e scriverli in un log di controllo nell'account di Archiviazione di Azure, nell'area di lavoro Log Analytics o in Hub eventi.

Inoltre, è possibile trasmettere Azure SQL dati di telemetria di diagnostica in Analisi SQL di Azure, una soluzione cloud che monitora le prestazioni dei database Azure SQL e delle istanze gestite Azure SQL su larga scala e tra più sottoscrizioni. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.

- [Come configurare il controllo per l'database SQL di Azure](/azure/sql-database/sql-database-auditing)

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](/azure/sql-database/sql-database-metrics-diag-logging)

- [Come trasmettere la diagnostica in Analisi SQL di Azure](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare il controllo nell'istanza del server database SQL di Azure e scegliere un percorso di archiviazione per i log di controllo (Archiviazione di Azure, Log Analytics o Hub eventi).

- [Come abilitare il controllo per Azure SQL Server](/azure/sql-database/sql-database-auditing)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** quando si archiviano database SQL di Azure log in un'area di lavoro Log Analytics, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Linee** guida: analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Centro sicurezza di Azure Advanced Threat Protection di Centro sicurezza di Azure per avvisare le attività insolite correlate all'database SQL di Azure istanza. In alternativa, configurare gli avvisi in base ai valori delle metriche o alle voci del log attività di Azure correlate alle istanze database SQL di Azure servizio.

- [Informazioni su Advanced Threat Protection e gli avvisi per Azure SQL Server](/azure/sql-database/sql-database-threat-detection-overview)

- [Come configurare avvisi personalizzati per database SQL di Azure](alerts-insights-configure-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare Centro sicurezza di Azure Advanced Threat Protection per Azure SQL database per il monitoraggio e l'avviso in caso di attività anoma. Abilitare Azure Defender per SQL per i database SQL. Azure Defender per SQL include funzionalità per l'individuazione e la classificazione di dati sensibili, l'individuazione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.

- [Informazioni su Advanced Threat Protection e gli avvisi per database SQL di Azure](/azure/sql-database/sql-database-threat-detection-overview)

- [Come abilitare Azure Defender per SQL per database SQL di Azure](azure-defender-for-sql.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee** guida: Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono queryable. Usare il Azure AD di PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee** guida: Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Quando si esegue il provisioning database SQL di Azure'istanza di , è consigliabile scegliere di integrare l'autenticazione con Azure AD.

- [Come configurare e gestire l'Azure AD autenticazione con Azure SQL](/azure/azure-sql/database/authentication-aad-configure)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee** guida: creare criteri e procedure per l'uso di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usare workstation protette gestite da Azure per attività amministrative

**Indicazioni:** usare una workstation PAW (Privileged Access Workstation) con autenticazione a più fattori configurata per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Usare Advanced Threat Protection per database SQL di Azure rilevare attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare le attività di identità e accesso degli utenti in Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

- [Esaminare Advanced Threat Protection e i potenziali avvisi](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** usare località denominate per l'accesso condizionale per consentire l'accesso al portale e a Gestione risorse di Azure solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** creare un amministratore Azure Active Directory (Azure AD) per le istanze database SQL di Azure server.

- [Come configurare e gestire l'Azure AD autenticazione con Azure SQL](authentication-aad-configure.md)

- [Come creare e configurare un'istanza di Azure AD](../../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. Usare anche le verifiche di accesso delle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere verificato a intervalli regolari per assicurarsi che l'accesso continui solo agli utenti autorizzati.

- [Come usare le verifiche di accesso alle identità di Azure](../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** configurare l'autenticazione Azure Active Directory (Azure AD) con Azure SQL e creare le impostazioni di diagnostica per gli account utente di Azure AD, inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come configurare e gestire l'Azure AD autenticazione con Azure SQL](authentication-aad-configure.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Linee** guida: usare Azure Active Directory (Azure AD) Identity Protection e i rilevamenti dei rischi per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inoltre, è possibile inserire dati in Azure Sentinel per un'ulteriore analisi.

- [Come visualizzare gli Azure AD di rischio](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee** guida: negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Azure Customer Lockbox offre un'interfaccia che consente ai clienti di esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

- [Informazioni Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette all'interno di un gruppo di sicurezza di rete o Firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. Usare il collegamento privato; distribuire Azure SQL Server all'interno della rete virtuale e connettersi privatamente usando endpoint privati.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

- [Come configurare Collegamento privato per il database SQL di Azure](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** per i database in database SQL di Azure l'archiviazione o l'elaborazione di informazioni riservate, contrassegnare il database e le risorse correlate come sensibili usando tag. Configurare il collegamento privato in combinazione con i tag di servizio del gruppo di sicurezza di rete nelle istanze database SQL di Azure per impedire l'esfiltrazione di informazioni riservate.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Come configurare il collegamento privato e i NSG per impedire l'esfiltrazione dei dati nelle istanze database SQL di Azure private](/azure/sql-database/sql-database-private-endpoint-overview)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee** guida: usare la funzionalità database SQL di Azure di individuazione e classificazione dei dati. L'individuazione e la classificazione dei dati forniscono funzionalità avanzate integrate database SQL di Azure per l'individuazione, la classificazione e l'etichettatura della protezione dei dati &amp; sensibili nei database.

- [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](/azure/sql-database/sql-database-data-discovery-and-classification)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee** guida: usare Azure Active Directory (Azure AD) per l'autenticazione e il controllo dell'accesso database SQL di Azure istanze.

- [Come integrare Azure SQL Server con Azure AD per l'autenticazione](/azure/sql-database/sql-database-aad-authentication)

- [Come controllare l'accesso in Azure SQL Server](/azure/sql-database/sql-database-control-access)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

Linee **guida:** Transparent Data Encryption (TDE) consente di proteggere database SQL di Azure, un'istanza gestita di Azure SQL e Azure Data Warehouse dalla minaccia di attività offline dannose crittografando i dati in pausa. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, TDE è abilitato per tutti i database appena distribuiti nel database SQL e in SQL Istanza gestita. La chiave di crittografia TDE può essere gestita da Microsoft o dal cliente.

- [Come gestire la crittografia dei dati trasparente e usare le proprie chiavi di crittografia](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di database SQL di Azure e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** abilitare Azure Defender per SQL per database SQL di Azure e seguire le raccomandazioni di Centro sicurezza di Azure sull'esecuzione di valutazioni della vulnerabilità nei Azure SQL server.

- [Come eseguire valutazioni della vulnerabilità in database SQL di Azure](/azure/sql-database/sql-vulnerability-assessment)

- [Come abilitare Azure Defender per SQL](azure-defender-for-sql.md)

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee** guida: abilitare analisi ricorrenti periodiche per le istanze database SQL di Azure periodiche. verrà configurata una valutazione della vulnerabilità per eseguire automaticamente un'analisi nel database una volta alla settimana. Verrà inviato un riepilogo dei risultati di analisi agli indirizzi di posta elettronica forniti. Confrontare i risultati per verificare che le vulnerabilità siano state corretti.

- [Come esportare un report di valutazione della vulnerabilità in Centro sicurezza di Azure](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni dei rischi predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure.

- [Informazioni Centro sicurezza di Azure punteggio di sicurezza](/azure/security-center/security-center-secure-score)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze di Azure SQL Server) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e sottoscrizioni separate, se necessario, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefiniti seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: usare Criteri di Azure o Centro sicurezza di Azure per Azure SQL server/database per mantenere le configurazioni di sicurezza per tutte le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di Criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.SQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Linee** guida: sfruttare Centro sicurezza di Azure per eseguire analisi di base per Azure SQL server e database.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](/azure/security-center/security-center-sql-service-recommendations)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare Azure Key Vault per archiviare le chiavi di crittografia per database SQL di Azure Transparent Data Encryption (TDE).

- [Come proteggere i dati sensibili archiviati in Azure SQL Server e archiviare le chiavi di crittografia in Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice.

- [Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure SQL](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Come configurare le identità gestite](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida:** implementare lo scanner delle credenziali per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Servizio app di Azure), ma non viene eseguito sul contenuto del cliente.

Eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB, Azure SQL Server e così via. Microsoft non può accedere ai dati in queste istanze.

- [Informazioni Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali](../../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** per proteggere l'azienda dalla perdita di dati, database SQL di Azure crea automaticamente backup completi del database settimanali, backup differenziali del database ogni 12 ore e backup del log delle transazioni ogni 5-10 minuti. I backup vengono archiviati nell'archiviazione RA-GRS per almeno 7 giorni per tutti i livelli di servizio. Tutti i livelli di servizio, ad supporto Basic periodo di conservazione del backup configurabile per il ripristino temporizzazione, fino a 35 giorni.

Per soddisfare requisiti di conformità diversi, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuale. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione.

- [Informazioni su backup e continuità aziendale con Azure SQL Server](/azure/sql-database/sql-database-business-continuity)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** database SQL di Azure crea automaticamente i backup del database mantenuti tra 7 e 35 giorni e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure (RA-GRS) per assicurarsi che siano conservati anche se il data center non è disponibile. Questi backup vengono creati automaticamente. Se necessario, abilitare i backup con ridondanza geografica a lungo termine per l'database SQL di Azure.

Se si usano chiavi gestite dal cliente per Transparent Data Encryption, assicurarsi che venga eseguito il backup delle chiavi.

- [Informazioni su backup in Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee** guida: garantire la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno Backup di Azure. Se necessario, testare il contenuto di ripristino in una VLAN isolata. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Come ripristinare database SQL di Azure backup con il ripristino temporato](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** abilitare l'eliminazione software Azure Key Vault proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione soft in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni:** assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono i ruoli del personale e le fasi di gestione/gestione degli eventi imprevisti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../../security-center/security-center-planning-and-operations-guide.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni:** il Centro sicurezza assegna una gravità agli avvisi, per consentire di classificare in ordine di priorità l'ordine in cui si partecipa a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile ottenerlo immediatamente. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o nell'analisi usata per l'emissione dell'avviso, nonché sul livello di attendibilità che si è verificato un intento dannoso dietro l'attività che ha generato l'avviso.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [È possibile fare riferimento alla pubblicazione di NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guida ai programmi di test, formazione ed esercizio per piani e funzionalità IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni:** le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che l'accesso ai dati è stato eseguito da parte di parti non autorizzate o non autorizzate.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
