---
title: Baseline di sicurezza di Azure per Synapse Analytics
description: La baseline Synapse Analytics sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c2fad393479f0ffce81a272919b8d12024001991
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565458"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Baseline di sicurezza di Azure per Synapse Analytics

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Synapse Analytics. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Synapse Analytics. **I** controlli non applicabili Synapse Analytics sono stati esclusi.

 
Per informazioni su come Synapse Analytics completamente mappato ad Azure Security Benchmark, vedere il [file di mapping](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)completo Synapse Analytics baseline di sicurezza .

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno di reti virtuali

**Indicazioni:** proteggere il Azure SQL Server in una rete virtuale tramite collegamento privato. collegamento privato di Azure consente di accedere ai servizi PaaS di Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft.

In alternativa, quando ci si connette al pool Synapse SQL, limitare l'ambito della connessione in uscita al database SQL usando un gruppo di sicurezza di rete. Disabilitare tutto il traffico del servizio di Azure verso il database SQL tramite l'endpoint pubblico impostando Consenti servizi di Azure su OFF. Assicurarsi che non siano consentiti indirizzi IP pubblici nelle regole del firewall.

- [Informazioni collegamento privato di Azure](../private-link/private-link-overview.md)

- [Informazioni sul collegamento privato per Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni:** quando ci si connette al pool SQL dedicato ed è stato abilitato il gruppo di sicurezza di rete (NSG), i log vengono inviati in un account Archiviazione di Azure per il controllo del traffico.

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP noti dannosi

**Indicazioni:** usare Advanced Threat Protection (ATP) per Azure Synapse SQL. ATP rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database e può attivare vari avvisi, ad esempio "Potenziale sql injection" e "Accesso da una posizione insolita". ATP fa parte dell'offerta Advanced Data Security (ADS) ed è accessibile e gestito tramite il portale centrale di SQL ADS.

Abilitare Protezione DDoS Standard nelle reti virtuali associate a Azure Synapse SQL per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Informazioni su ATP per Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare Advanced Data Security per database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Panoramica di ADS](../azure-sql/database/azure-defender-for-sql.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Linee** guida: quando ci si connette al pool SQL dedicato ed è stato abilitato il gruppo di sicurezza di rete (NSG), inviare i log in un account Archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics o inviarli in streaming a Hub eventi.  Se necessario per analizzare l'attività anomaa, abilitare Network Watcher'acquisizione di pacchetti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** usare Advanced Threat Protection (ATP) per Azure Synapse SQL. ATP rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database e può attivare vari avvisi, ad esempio "Potenziale attacco SQL injection" e "Accesso da una posizione insolita". ATP fa parte dell'offerta Sicurezza dei dati avanzata ed è accessibile e gestito tramite il portale centrale di SQL ADS. ATP integra anche gli avvisi con Centro sicurezza di Azure.

- [Informazioni su ATP per Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Quando si usa un endpoint di servizio per il pool SQL dedicato, sono necessari indirizzi IP pubblici in uscita verso il database Azure SQL: i gruppi di sicurezza di rete (NSG) devono essere aperti agli indirizzi IP database SQL di Azure per consentire la connettività. A tale scopo, è possibile usare i tag di servizio del gruppo di database SQL di Azure.

- [Informazioni su tag di servizio con endpoint di servizio per database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Comprendere e usare i tag del servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza di rete per le risorse correlate al pool SQL dedicato con Criteri di Azure. È possibile usare lo spazio dei nomi "Microsoft.Sql" per definire definizioni di criteri personalizzate o usare una qualsiasi delle definizioni di criteri incorporate progettate per la protezione di rete Azure SQL database/server. Un esempio di un criterio di sicurezza di rete predefinito applicabile per database SQL di Azure server è: "SQL Server deve usare un endpoint servizio di rete virtuale".

Usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio i modelli di Azure Resource Management, il controllo degli accessi in base al ruolo di Azure e i criteri, in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una qualsiasi delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al pool SQL dedicato. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** è possibile definire criteri di controllo per un database specifico o come criteri server predefiniti in Azure (che ospita Azure Synapse). I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

Se il controllo del server è abilitato, si applica sempre al database. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

Quando si abilita il controllo, è possibile scriverli in un log di controllo nell'account Archiviazione di Azure, nell'area di lavoro Log Analytics o in Hub eventi.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come configurare il controllo per le risorse Azure SQL lavoro](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare il controllo Azure SQL livello di server per il pool SQL dedicato e scegliere un percorso di archiviazione per i log di controllo (Archiviazione di Azure, Log Analytics o Hub eventi).

Il controllo può essere abilitato sia a livello di database che di server e viene suggerito di essere abilitato solo a livello di server, a meno che non sia necessario configurare un sink di dati o una conservazione separata per un database specifico.

- [Come abilitare il controllo per database SQL di Azure](../azure-sql/database/auditing-overview.md)

- [Come abilitare il controllo per il server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Differenze nei criteri di controllo a livello di server e a livello di database](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** quando si archiviano i log correlati al pool SQL dedicato in un account di archiviazione, in un'area di lavoro Log Analytics o in hub eventi, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Come impostare i parametri di conservazione dei log in un'area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Acquisire eventi di streaming in Hub eventi](../event-hubs/event-hubs-capture-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Advanced Threat Protection per database SQL di Azure in combinazione con Centro sicurezza di Azure avvisi in caso di attività insolite correlate al database SQL. In alternativa, configurare gli avvisi in base ai valori delle metriche o alle voci del log attività di Azure correlate al database SQL.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Informazioni su Advanced Threat Protection e gli avvisi per database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare Advanced Data Security per database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Come configurare avvisi personalizzati per database SQL di Azure](../azure-sql/database/alerts-insights-configure-portal.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare Advanced Threat Protection (ATP) per database SQL di Azure in combinazione con Centro sicurezza di Azure per monitorare e avvisare le attività anoma le. ATP fa parte dell'offerta Advanced Data Security (ADS) ed è accessibile e gestito tramite SQL ADS centrale nel portale. ADS include funzionalità per l'individuazione e la classificazione di dati sensibili, l'individuazione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.

In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel.

- [Informazioni su Advanced Threat Protection e gli avvisi per database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare Advanced Data Security per database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** gli utenti vengono autenticati con Azure Active Directory (Azure AD) o con l'autenticazione SQL.

Quando si distribuisce per la Azure SQL, si specifica un account di accesso amministratore e una password associata per tale account di accesso. Questo account amministrativo è denominato amministratore del server. È possibile identificare gli account amministratore per un database aprendo il portale di Azure e passando alla scheda delle proprietà del server o dell'istanza gestita. È anche possibile configurare un account Azure AD amministratore con autorizzazioni amministrative complete. Questa operazione è necessaria se si vuole abilitare Azure AD autenticazione.

Per le operazioni di gestione, usare i ruoli predefiniti di Azure che devono essere assegnati in modo esplicito. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Autenticazione per il database SQL](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Creare account per utenti non amministrativi](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Usare un account Azure AD per l'autenticazione](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Come gestire gli account di accesso e gli account amministratore esistenti in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Quando si effettua il provisioning di un pool SQL dedicato, è consigliabile scegliere di integrare l'autenticazione con Azure AD. Con questo metodo di autenticazione, l'utente invia un nome di account utente e richiede che il servizio usi le informazioni sulle credenziali archiviate in Azure AD.

- [Come configurare e gestire l'Azure AD autenticazione con Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Comprendere l'autenticazione in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** creare criteri e procedure per l'uso di account amministrativi dedicati. Usare Centro sicurezza di Azure Identity and Access Management per monitorare il numero di account amministrativi che accedono tramite Azure Active Directory (Azure AD).

Per identificare gli account amministratore per un database, aprire il portale di Azure e passare alla scheda Proprietà del server o dell'istanza gestita.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Come gestire gli account di accesso e gli account amministratore esistenti in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** usare la registrazione di un'app di Azure (entità servizio) per recuperare un token che può essere usato per interagire con il data warehouse nel piano di controllo (portale di Azure) tramite chiamate API.

- [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Come registrare l'applicazione client (entità servizio) con Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse informazioni sull'API REST SQL](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato su autenticazione

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Comprendere l'autenticazione a più fattori in Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usare workstation protette gestite da Azure per attività amministrative

**Indicazioni:** usare una workstation PAW (Privileged Access Workstation) con autenticazione a più fattori configurata per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Usare Advanced Threat Protection per database SQL di Azure in combinazione con Centro sicurezza di Azure per rilevare e avvisare le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

SQL Server Audit consente di creare controlli del server che possono contenere specifiche di controllo del server per gli eventi a livello di server e specifiche di controllo del database per gli eventi a livello di database. Gli eventi controllati possono essere scritti nei log eventi o in file di controllo.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e l'accesso degli utenti in Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Esaminare Advanced Threat Protection e i potenziali avvisi](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Informazioni su account di accesso e account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Informazioni SQL Server controllo](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** usare località denominate per l'accesso condizionale per consentire l'accesso al portale e a Gestione risorse di Azure solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** creare un Azure Active Directory (Azure AD) per il server database SQL di Azure nel pool SQL dedicato.

- [Come configurare e gestire l'Azure AD autenticazione con Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. È anche possibile usare le Azure AD di accesso per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che l'accesso continui solo agli utenti autorizzati.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti del database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per tale gruppo di utenti.

- [Come usare le verifiche di accesso](../active-directory/governance/access-reviews-overview.md)

- [Informazioni su account di accesso e account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** configurare l'autenticazione Azure Active Directory (Azure AD) con Azure SQL e abilitare le impostazioni di diagnostica per gli account utente di Azure AD, inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati in Log Analytics.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti del database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- [Come usare le verifiche di accesso](../active-directory/governance/access-reviews-overview.md)

- [Come configurare e gestire l'Azure AD autenticazione con database SQL di Azure](../azure-sql/database/authentication-aad-configure.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Informazioni su account di accesso e account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare Azure Active Directory (Azure AD) Identity Protection e le funzionalità di rilevamento dei rischi per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inoltre, l'on board e l'inserimento di dati Azure Sentinel per un'ulteriore analisi.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti del database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- [Come visualizzare gli Azure AD di rischio](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come caricare dati in Azure Sentinel](../sentinel/connect-data-sources.md)

- [Informazioni su account di accesso e account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** negli scenari di supporto in cui Microsoft deve accedere ai dati correlati al database SQL di Azure nel pool SQL dedicato, Azure Customer Lockbox offre un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

La classificazione &amp; dell'individuazione dati è incorporata Azure Synapse SQL. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'etichettatura e la segnalazione dei dati sensibili nei database.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Informazioni sulla classificazione dell'individuazione &amp; dati](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette all'interno di un gruppo di sicurezza di rete o di Firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. Usare il collegamento privato; distribuire il server Azure SQL all'interno di una rete virtuale e connettersi in modo sicuro tramite collegamento privato.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come configurare Collegamento privato per il database SQL di Azure](../azure-sql/database/private-endpoint-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** per qualsiasi database SQL di Azure nel pool SQL dedicato che archivia o elabora informazioni riservate, contrassegnare il database e le risorse correlate come sensibili usando i tag. Configurare collegamento privato insieme ai tag di servizio del gruppo di sicurezza di rete (NSG) nelle istanze di database SQL di Azure per impedire l'esfiltrazione di informazioni riservate.

Inoltre, Advanced Threat Protection per database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Come configurare il collegamento privato e i NSG per impedire l'esfiltrazione dei dati nelle istanze database SQL di Azure rete](../azure-sql/database/private-endpoint-overview.md)

- [Informazioni su Advanced Threat Protection per database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** usare la funzionalità Azure Synapse di individuazione dati &amp; SQL. Classificazione individuazione dati offre funzionalità avanzate integrate in database SQL di Azure per l'individuazione, la classificazione e l'etichettatura della protezione dei dati &amp; &amp; sensibili nei database.

La classificazione &amp; dell'individuazione dati fa parte dell'offerta Sicurezza dei dati avanzata, un pacchetto unificato per le funzionalità di sicurezza avanzate di SQL. La classificazione &amp; dell'individuazione dati è accessibile e gestita tramite il portale centrale di SQL ADS.

È anche possibile configurare un criterio DDM (Dynamic Data Masking) nel portale di Azure. Il motore delle raccomandazioni DDM contrassegna alcuni campi del database come campi potenzialmente sensibili che possono essere candidati validi per la maschera.

- [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Informazioni sulla maschera dati dinamica per Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso Azure SQL database nel pool SQL dedicato.

L'autorizzazione è controllata dalle appartenenze ai ruoli del database dell'account utente e dalle autorizzazioni a livello di oggetto. È consigliabile concedere agli utenti i privilegi minimi necessari.

- [Come integrare Azure SQL Server con Azure Active Directory (Azure AD) per l'autenticazione](../azure-sql/database/authentication-aad-overview.md)

- [Come controllare l'accesso in Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Informazioni su autorizzazione e autenticazione in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** Transparent Data Encryption (TDE) consente di proteggere Azure Synapse SQL dalla minaccia di attività offline dannose crittografando i dati inalteati. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. In Azure l'impostazione predefinita per TDE è che la chiave DEK è protetta da un certificato server predefinito. In alternativa, è possibile usare TDE gestito dal cliente, noto anche come Bring Your Own Key (BYOK) per TDE. In questo scenario, la protezione TDE che crittografa la chiave dek è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault gestito e di proprietà del cliente (il sistema di gestione delle chiavi esterne basato sul cloud di Azure) e non lascia mai l'insieme di credenziali delle chiavi.

- [Informazioni sulla crittografia trasparente dei dati gestita dal servizio](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Informazioni sulla crittografia trasparente dei dati gestita dal cliente](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Come attivare TDE usando la propria chiave](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di Synapse SQL pool e altre risorse critiche o correlate.

È anche possibile configurare gli avvisi per i database nel pool di SQL Synapse usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica all'utente o chiamare un webhook quando una o più metriche (ad esempio le dimensioni del database o l'utilizzo della CPU) raggiungono la soglia impostata.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Come creare avvisi per Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee** guida: abilitare Advanced Data Security e seguire le raccomandazioni Centro sicurezza di Azure sull'esecuzione di valutazioni della vulnerabilità Azure SQL database.

- [Come eseguire valutazioni della vulnerabilità nei Azure SQL database](../azure-sql/database/sql-vulnerability-assessment.md)

- [Come abilitare Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

- [Come implementare le raccomandazioni Centro sicurezza di Azure valutazione della vulnerabilità](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni:** Valutazione della vulnerabilità è un servizio di analisi integrato Azure Synapse SQL. Il servizio usa una knowledge base di regole che contrassegnano le vulnerabilità di sicurezza. Evidenzia le deviazioni dalle procedure consigliate, ad esempio errori di configurazione, autorizzazioni eccessive e dati sensibili non protetti.  Valutazione della vulnerabilità è accessibile e gestito tramite il portale centrale di Sicurezza dei dati avanzata (ADS) di SQL.

- [Gestire ed esportare analisi di valutazione della vulnerabilità nel portale di SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni di rischio predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure.

La classificazione &amp; dell'individuazione dati è incorporata Azure Synapse SQL. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'etichettatura e la segnalazione dei dati sensibili nei database.

- [Informazioni Centro sicurezza di Azure Secure Score](../security-center/secure-score-security-controls.md)

- [Informazioni sulla classificazione dell'individuazione &amp; dati](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse correlate al pool SQL dedicato all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Anche se le risorse classiche di Azure possono essere individuate tramite Azure Resource Graph, è consigliabile creare e usare Azure Resource Manager in futuro.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se necessario, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Linee** guida: definire un elenco di risorse di Azure approvate correlate al pool SQL dedicato.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefiniti seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Sql" per creare criteri personalizzati per controllare o applicare la configurazione delle risorse correlate al pool SQL dedicato. È anche possibile usare definizioni di criteri predefiniti per database/server di Azure, ad esempio:

- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- I server SQL devono usare un endpoint servizio di rete virtuale

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di Criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** non applicabile; Azure Synapse SQL non dispone di impostazioni di sicurezza configurabili.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** sfruttare Centro sicurezza di Azure per eseguire analisi di base per tutte le risorse correlate al pool SQL dedicato.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** Transparent Data Encryption (TDE) con chiavi gestite dal cliente in Azure Key Vault consente la crittografia della chiave di crittografia del database (DEK) generata automaticamente con una chiave asimmetrica gestita dal cliente denominata protezione TDE. Questo è anche comunemente noto come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. Nello scenario BYOK la protezione TDE viene archiviata in un ambiente di proprietà del cliente e gestito Azure Key Vault. Assicurarsi inoltre che l'eliminazione soft sia abilitata in Azure Key Vault.

- [Come abilitare TDE con chiave gestita dal cliente da Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Come abilitare l'eliminazione Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice.

- [Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida:** implementare lo scanner delle credenziali per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Synapse SQL); tuttavia, non viene eseguito sul contenuto del cliente.

Eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB, Azure SQL Server e così via. Microsoft non può accedere ai dati in queste istanze.

- [Informazioni Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali](../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** gli snapshot del pool SQL dedicato vengono automaticamente evasi durante il giorno creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL dedicato supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che è anche possibile attivare manualmente gli snapshot, se necessario.

- [Backup e ripristino nel pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** gli snapshot dei data warehouse automaticamente durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL dedicato supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che è anche possibile attivare manualmente gli snapshot, se necessario.

Se si usa una chiave gestita dal cliente per crittografare la chiave di crittografia del database, assicurarsi che venga eseguito il backup della chiave.

- [Backup e ripristino in un pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

- [Come eseguire il backup Azure Key Vault chiavi](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark è](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) [l'iniziativa](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** testare periodicamente i punti di ripristino per assicurarsi che gli snapshot siano validi. Per ripristinare un pool SQL dedicato esistente da un punto di ripristino, è possibile usare il portale di Azure o PowerShell. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare le Azure Key Vault chiave](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Backup e ripristino in un pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

- [Come ripristinare un pool SQL dedicato esistente](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** in database SQL di Azure è possibile configurare un database singolo o in pool con criteri di conservazione dei backup a lungo termine per conservare automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. I dati in Archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più potenti disponibili ed è conforme a FIPS 140-2.

Per impostazione predefinita, i dati in un account di archiviazione vengono crittografati con chiavi gestite da Microsoft. È possibile usare chiavi gestite da Microsoft per la crittografia dei dati oppure gestire la crittografia con chiavi proprie. Se si gestiscono le proprie chiavi con Key Vault, assicurarsi che l'eliminazione soft sia abilitata.

- [Gestire la conservazione a lungo termine dei backup del database SQL di Azure](../azure-sql/database/long-term-backup-retention-configure.md)

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione soft in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Linee** guida: assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono i ruoli del personale e le fasi di gestione/gestione degli eventi imprevisti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee** guida: il Centro sicurezza assegna una gravità agli avvisi, per consentire di classificare in ordine di priorità l'ordine in cui si partecipa a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile ottenerla immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sulla metrica usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee** guida: eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi in base a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [È possibile fare riferimento alla pubblicazione di NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guida ai programmi di test, training ed esercizio per i piani e le funzionalità IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Linee** guida: le informazioni di contatto degli eventi imprevisti di sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che i dati sono stati accessibili da parte di una parte non autorizzata o illecita.

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

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni:** seguire le Regole di engagement di Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Altre informazioni sulla strategia e sull'esecuzione di Microsoft per red teaming e test di penetrazione del sito in tempo reale sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni, sono disponibili qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
