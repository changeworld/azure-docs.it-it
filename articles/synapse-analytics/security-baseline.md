---
title: Baseline della sicurezza di Azure per l'analisi delle sinapsi
description: La linea di base di sicurezza di sinapsi Analytics fornisce le informazioni e le procedure per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586854"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Baseline della sicurezza di Azure per l'analisi delle sinapsi

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) a sinapsi Analytics. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a sinapsi Analytics. I **controlli** non applicabili a sinapsi Analytics sono stati esclusi.

 
Per informazioni sul modo in cui le sinapsi Analytics sono completamente mappate al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza di sinapsi Analytics completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: proteggere il SQL Server di Azure in una rete virtuale tramite un collegamento privato. Il collegamento privato di Azure consente di accedere ai servizi PaaS di Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft.

In alternativa, quando ci si connette al pool SQL sinapsi, limitare l'ambito della connessione in uscita al database SQL usando un gruppo di sicurezza di rete. Disabilitare tutto il traffico del servizio di Azure nel database SQL tramite l'endpoint pubblico impostando Consenti i servizi di Azure su disattivato. Assicurarsi che non siano consentiti indirizzi IP pubblici nelle regole del firewall.

- [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

- [Informazioni sul collegamento privato per SQL sinapsi di Azure](../azure-sql/database/private-endpoint-overview.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: quando ci si connette al pool SQL dedicato e sono stati abilitati i log dei flussi del gruppo di sicurezza di rete (NSG), i log vengono inviati in un account di archiviazione di Azure per il controllo del traffico.

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection (ATP) per SQL sinapsi di Azure. ATP rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare diversi avvisi, ad esempio "potenziale attacco SQL injection" e "accesso da una posizione insolita". ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirlo tramite il portale SQL ADS centrale.

Abilitare protezione DDoS standard nelle reti virtuali associate ad Azure sinapsi SQL per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Informazioni su ATP per sinapsi SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Panoramica degli annunci](../azure-sql/database/azure-defender-for-sql.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: quando ci si connette al pool SQL dedicato e sono stati abilitati i log dei flussi del gruppo di sicurezza di rete (NSG), inviare i log in un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso a un'area di lavoro di Log Analytics o trasmetterli a hub eventi.  Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: usare Advanced Threat Protection (ATP) per SQL sinapsi di Azure. ATP rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare diversi avvisi, ad esempio "potenziale attacco SQL injection" e "accesso da una posizione insolita". ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirlo tramite il portale SQL ADS centrale. ATP integra anche gli avvisi con il Centro sicurezza di Azure.

- [Informazioni su ATP per sinapsi SQL di Azure](../azure-sql/database/threat-detection-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Quando si usa un endpoint di servizio per il pool SQL dedicato, è necessario aprire indirizzi IP pubblici in uscita per database SQL di Azure: i gruppi di sicurezza di rete (gruppi) devono essere aperti agli IP del database SQL di Azure per consentire la connettività. A tale scopo, è possibile usare i tag del servizio NSG per il database SQL di Azure.

- [Informazioni sui tag del servizio con gli endpoint di servizio per il database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza di rete per le risorse correlate al pool SQL dedicato con criteri di Azure. È possibile usare lo spazio dei nomi "Microsoft. SQL" per definire le definizioni dei criteri personalizzati o usare una qualsiasi delle definizioni di criteri predefinite progettate per la protezione di rete del server o del database SQL di Azure. Un esempio di criteri di sicurezza di rete incorporati applicabili per il server di database SQL di Azure è il seguente: "SQL Server usare un endpoint del servizio di rete virtuale".

USA i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC) e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al pool SQL dedicato. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: è possibile definire criteri di controllo per un database specifico o come criteri server predefiniti in Azure (che ospita la sinapsi di Azure). I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

Se il controllo del server è abilitato, viene sempre applicato al database. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

Quando si Abilita il controllo, è possibile scriverli in un log di controllo nell'account di archiviazione di Azure, Log Analytics area di lavoro o hub eventi.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come configurare il controllo per le risorse SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare il controllo nel livello server di Azure SQL per il pool SQL dedicato e scegliere un percorso di archiviazione per i log di controllo (archiviazione di azure, log Analytics o hub eventi).

È possibile abilitare il controllo a livello di database o di server ed è consigliabile abilitarlo solo a livello di server, a meno che non sia necessario configurare un sink di dati separato o una conservazione per un database specifico.

- [Come abilitare il controllo per il database SQL di Azure](../azure-sql/database/auditing-overview.md)

- [Come abilitare il controllo per il server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Differenze tra i criteri di controllo a livello di server e di database](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: quando si archiviano i log correlati al pool SQL dedicato in un account di archiviazione, log Analytics area di lavoro o hub eventi, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Come impostare i parametri di conservazione dei log in un'area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Acquisire eventi di streaming in hub eventi](../event-hubs/event-hubs-capture-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per i comportamenti anomali ed esaminare periodicamente i risultati. Usare Advanced Threat Protection per il database SQL di Azure insieme al centro sicurezza di Azure per segnalare attività insolite correlate al database SQL. In alternativa, configurare gli avvisi in base ai valori delle metriche o alle voci del log attività di Azure correlate al database SQL.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per il database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Come configurare gli avvisi personalizzati per il database SQL di Azure](../azure-sql/database/alerts-insights-configure-portal.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare Advanced Threat Protection (ATP) per il database SQL di Azure in combinazione con il Centro sicurezza di Azure per monitorare e inviare avvisi sulle attività anomale. ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirli tramite gli annunci SQL centrali nel portale. Gli annunci includono funzionalità per l'individuazione e la classificazione dei dati sensibili, l'emersione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

- [Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per il database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: gli utenti vengono autenticati con l'autenticazione di Azure Active Directory (Azure ad) o SQL.

Quando si distribuisce per la prima volta SQL di Azure, è necessario specificare un account di accesso amministratore e una password associata per tale account di accesso. Questo account amministrativo è denominato amministratore del server. È possibile identificare gli account amministratore per un database aprendo il portale di Azure e passando alla scheda proprietà del server o dell'istanza gestita. È anche possibile configurare un account amministratore Azure AD con autorizzazioni amministrative complete. questa operazione è necessaria se si desidera abilitare l'autenticazione Azure AD.

Per le operazioni di gestione, usare i ruoli predefiniti di Azure che devono essere assegnati in modo esplicito. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Autenticazione per il database SQL](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Creare account per utenti non amministratori](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Usare un account Azure AD per l'autenticazione](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Come gestire gli account di accesso e gli account amministratore esistenti in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory (Azure ad) non ha il concetto di password predefinite. Quando si esegue il provisioning di un pool SQL dedicato, è consigliabile scegliere di integrare l'autenticazione con Azure AD. Con questo metodo di autenticazione, l'utente invia un nome di account utente e richiede che il servizio utilizzi le informazioni sulle credenziali archiviate in Azure AD.

- [Come configurare e gestire l'autenticazione Azure AD con SQL di Azure](../azure-sql/database/authentication-aad-configure.md)

- [Informazioni sull'autenticazione in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare criteri e procedure per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi che accedono tramite Azure Active Directory (Azure AD).

Per identificare gli account amministratore per un database, aprire il portale di Azure e passare alla scheda proprietà del server o dell'istanza gestita.

- [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Come gestire gli account di accesso e gli account amministratore esistenti in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: usare una registrazione di app di Azure (entità servizio) per recuperare un token che può essere usato per interagire con il data warehouse nel piano di controllo (portale di Azure) tramite chiamate API.

- [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Come registrare l'applicazione client (entità servizio) con Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informazioni sull'API REST SQL di Azure sinapsi](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Informazioni sull'autenticazione a più fattori in SQL di Azure](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Linee guida**: usare una workstation con accesso con privilegi (Paw) con autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Usare Advanced Threat Protection per il database SQL di Azure insieme al centro sicurezza di Azure per rilevare e inviare avvisi su attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

SQL Server Audit consente di creare controlli del server che possono contenere specifiche di controllo del server per gli eventi a livello di server e specifiche di controllo del database per gli eventi a livello di database. Gli eventi controllati possono essere scritti nei log eventi o in file di controllo.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Esaminare Advanced Threat Protection e potenziali avvisi](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Informazioni sugli account di accesso e gli account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Informazioni sul controllo SQL Server](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale e alla gestione delle risorse di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: creare un amministratore Azure Active Directory (Azure ad) per il server di database SQL di Azure nel pool SQL dedicato.

- [Come configurare e gestire l'autenticazione Azure AD con SQL di Azure](../azure-sql/database/authentication-aad-configure.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

Materiale sussidiario **: Azure Active Directory**(Azure ad) fornisce log che consentono di individuare gli account obsoleti. Inoltre, è possibile utilizzare le verifiche di accesso Azure AD per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- [Come usare le verifiche di accesso](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sugli account di accesso e gli account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni**: configurare l'autenticazione Azure Active Directory (Azure ad) con SQL di Azure e abilitare le impostazioni di diagnostica per Azure ad account utente, inviando i log di controllo e i log di accesso a un'area di lavoro log Analytics. Configurare gli avvisi desiderati in Log Analytics.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- [Come usare le verifiche di accesso](../active-directory/governance/access-reviews-overview.md)

- [Come configurare e gestire l'autenticazione di Azure AD con il database SQL di Azure](../azure-sql/database/authentication-aad-configure.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Informazioni sugli account di accesso e gli account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di protezione delle identità Azure Active Directory (Azure ad) e di rilevamento del rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. Inoltre, il caricamento e l'inserimento dei dati in Sentinel di Azure per un'analisi più approfondita.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- [Come visualizzare gli accessi ai rischi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come caricare dati in Azure Sentinel](../sentinel/connect-data-sources.md)

- [Informazioni sugli account di accesso e gli account utente in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: in scenari di supporto in cui Microsoft deve accedere ai dati correlati al database SQL di Azure nel pool SQL dedicato, Azure Customer Lockbox fornisce un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

La classificazione di individuazione dei dati &amp; è incorporata in SQL sinapsi di Azure. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'etichettatura e la segnalazione dei dati sensibili nei database.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Informazioni sulla classificazione di individuazione dati &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete o in un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. USA collegamento privato; distribuire il SQL Server di Azure all'interno di una rete virtuale e connettersi in modo sicuro usando un collegamento privato.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come configurare Collegamento privato per il database SQL di Azure](../azure-sql/database/private-endpoint-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: per qualsiasi database SQL di Azure nel pool SQL dedicato che archivia o elabora informazioni riservate, contrassegnare il database e le risorse correlate come sensibili usando i tag. Configurare il collegamento privato insieme ai tag del servizio del gruppo di sicurezza di rete (NSG) nelle istanze del database SQL di Azure per impedire il exfiltration di informazioni riservate.

Inoltre, Advanced Threat Protection per il database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Come configurare il collegamento privato e gruppi per impedire il exfiltration dei dati nelle istanze del database SQL di Azure](../azure-sql/database/private-endpoint-overview.md)

- [Informazioni sulla protezione avanzata dalle minacce per il database SQL di Azure](../azure-sql/database/threat-detection-overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: usare la funzionalità di classificazione individuazione dati SQL di Azure sinapsi &amp; . &amp;La classificazione di individuazione dati fornisce funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette per &amp; la protezione dei dati sensibili nei database.

&amp;La classificazione di individuazione dati fa parte dell'offerta Advanced Data Security, che è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. &amp;È possibile accedere alla classificazione di individuazione dati e gestirla tramite il portale SQL ADS centrale.

Inoltre, è possibile configurare un criterio di maschera dati dinamica (DDM) nel portale di Azure. Il motore di raccomandazioni DDM contrassegna determinati campi dal database come campi potenzialmente sensibili che possono essere candidati validi per la maschera.

- [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Informazioni sulla maschera dati dinamica per SQL sinapsi di Azure](../azure-sql/database/dynamic-data-masking-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: usare il controllo di accesso RBAC di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso ai database SQL di Azure nel pool SQL dedicato.

L'autorizzazione viene controllata dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. È consigliabile concedere agli utenti i privilegi minimi necessari.

- [Come integrare SQL Server di Azure con Azure Active Directory (Azure AD) per l'autenticazione](../azure-sql/database/authentication-aad-overview.md)

- [Come controllare l'accesso in Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Informazioni su autorizzazione e autenticazione in SQL di Azure](../azure-sql/database/logins-create-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: Transparent Data Encryption (Transparent Data Encryption) consente di proteggere SQL sinapsi di Azure dalla minaccia di attività offline dannose mediante la crittografia dei dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. In Azure, l'impostazione predefinita per Transparent Data Encryption è che la chiave di crittografia è protetta da un certificato server incorporato. In alternativa, è possibile utilizzare Transparent Data Encryption gestito dal cliente, noto anche come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. In questo scenario, la protezione Transparent Data Encryption che crittografa la chiave di crittografia è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault gestito e di proprietà del cliente (sistema di gestione delle chiavi esterne basato sul cloud di Azure) senza mai lasciare l'insieme di credenziali delle chiavi.

- [Informazioni sulla crittografia Transparent Data Encryption gestita dal servizio](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Informazioni sulla crittografia trasparente dei dati gestita dal cliente](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Come attivare Transparent Data Encryption usando la propria chiave](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di pool SQL sinapsi e altre risorse critiche o correlate.

Inoltre, è possibile configurare gli avvisi per i database nel pool SQL sinapsi usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica all'utente o chiamare un webhook quando una o più metriche (ad esempio le dimensioni del database o l'utilizzo della CPU) raggiungono la soglia impostata.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Come creare avvisi per la sinapsi SQL di Azure](../azure-sql/database/alerts-insights-configure-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: abilitare la sicurezza dei dati avanzata e seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nei database SQL di Azure.

- [Come eseguire valutazioni delle vulnerabilità nei database SQL di Azure](../azure-sql/database/sql-vulnerability-assessment.md)

- [Come abilitare la sicurezza avanzata dei dati](../azure-sql/database/azure-defender-for-sql.md)

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: la valutazione della vulnerabilità è un servizio di analisi integrato in SQL sinapsi di Azure. Il servizio usa una Knowledge base di regole che contrassegnano le vulnerabilità della sicurezza. Evidenzia le deviazioni dalle procedure consigliate, ad esempio configurazioni errate, autorizzazioni eccessive e dati sensibili non protetti.  È possibile accedere alla valutazione della vulnerabilità e gestirla tramite il portale di SQL Advanced Data Security (ADS) centrale.

- [Gestire ed esportare le analisi della valutazione della vulnerabilità nel portale SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

La classificazione di individuazione dei dati &amp; è incorporata in SQL sinapsi di Azure. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'etichettatura e la segnalazione dei dati sensibili nei database.

- [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](../security-center/secure-score-security-controls.md)

- [Informazioni sulla classificazione di individuazione dati &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse correlate al pool SQL dedicato all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Azure Resource Graph, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

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

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: definire un elenco di risorse di Azure approvate correlate al pool SQL dedicato.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. SQL" per creare criteri personalizzati per controllare o applicare la configurazione delle risorse correlate al pool SQL dedicato. È anche possibile usare le definizioni di criteri predefinite per i database/server di Azure, ad esempio:

- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- I server SQL devono usare un endpoint servizio di rete virtuale

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: non applicabile; SQL sinapsi di Azure non dispone di impostazioni di sicurezza configurabili.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: sfruttare il Centro sicurezza di Azure per eseguire analisi di base per tutte le risorse correlate al pool SQL dedicato.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni**: Transparent Data Encryption (Transparent Data Encryption) con chiavi gestite dal cliente in Azure Key Vault consente la crittografia della chiave di crittografia del database generata automaticamente con una chiave asimmetrica gestita dal cliente denominata protezione Transparent Data Encryption. Questo è anche comunemente noto come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. Nello scenario BYOK, la protezione Transparent Data Encryption è archiviata in un Azure Key Vault gestito e di proprietà del cliente. Assicurarsi inoltre che l'eliminazione temporanea sia abilitata in Azure Key Vault.

- [Come abilitare Transparent Data Encryption con la chiave gestita dal cliente da Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Come abilitare l'eliminazione temporanea in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

- [Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, SQL sinapsi di Azure); Tuttavia, non viene eseguito sui contenuti del cliente.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio il servizio app, Data Lake Storage, archiviazione BLOB, Azure SQL Server e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

- [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: gli snapshot del pool SQL dedicato vengono eseguiti automaticamente durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL dedicato supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che, se necessario, è anche possibile attivare manualmente gli snapshot.

- [Eseguire il backup e il ripristino in un pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: gli snapshot del data warehouse vengono eseguiti automaticamente durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL dedicato supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che, se necessario, è anche possibile attivare manualmente gli snapshot.

Se si usa una chiave gestita dal cliente per crittografare la chiave di crittografia del database, assicurarsi che venga eseguito il backup della chiave.

- [Eseguire il backup e il ripristino in un pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

- [Come eseguire il backup di chiavi di Azure Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: testare periodicamente i punti di ripristino per assicurarsi che gli snapshot siano validi. Per ripristinare un pool SQL dedicato esistente da un punto di ripristino, è possibile usare il portale di Azure o PowerShell. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Come ripristinare chiavi di Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Eseguire il backup e il ripristino in un pool SQL dedicato](sql-data-warehouse/backup-and-restore.md)

- [Come ripristinare un pool SQL dedicato esistente](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: nel database SQL di Azure è possibile configurare un database singolo o in pool con criteri di conservazione dei backup a lungo termine per mantenere automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.

Per impostazione predefinita, i dati in un account di archiviazione vengono crittografati con chiavi gestite da Microsoft. È possibile utilizzare chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con chiavi personalizzate. Se si gestiscono chiavi personalizzate con Key Vault, assicurarsi che l'eliminazione temporanea sia abilitata.

- [Gestire la conservazione a lungo termine dei backup del database SQL di Azure](../azure-sql/database/long-term-backup-retention-configure.md)

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sulla metrica utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [È possibile fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati.

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

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
