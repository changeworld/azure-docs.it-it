---
title: Baseline della sicurezza di Azure per database di Azure per MySQL
description: La linea di base di sicurezza del database di Azure per MySQL fornisce le informazioni e le risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62501d6174b9ef8806de0bbdae0dfedfe70da03a
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968077"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Baseline della sicurezza di Azure per database di Azure per MySQL

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) al database di Azure per MySQL. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a database di Azure per MySQL. I **controlli** non applicabili a database di Azure per MySQL o per i quali la responsabilità è Microsoft sono stati esclusi.

Per informazioni sul modo in cui il database di Azure per MySQL è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base per la sicurezza del database di Azure per MySQL](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Indicazioni**: configurare Collegamento privato per Database di Azure per MySQL con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Collegamento privato di Azure trasferisce in pratica i servizi di Azure nella rete virtuale privata. Il traffico tra la rete virtuale e l'istanza di MySQL viaggia attraverso la rete backbone Microsoft.

In alternativa, è possibile usare gli endpoint servizio di rete virtuale per proteggere e limitare l'accesso in rete alle implementazioni di Database di Azure per MySQL. Le regole di rete virtuale rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Database di Azure per MySQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali.

È anche possibile proteggere il server Database di Azure per MySQL con regole del firewall. Il firewall del server impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

- [Come configurare un collegamento privato per database di Azure per MySQL](howto-configure-privatelink-portal.md)

- [Come creare e gestire gli endpoint di servizio VNet e le regole VNet nel database di Azure per MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Come configurare le regole del firewall di database di Azure per MySQL](howto-manage-firewall-using-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni**: quando l'istanza di Database di Azure per MySQL è protetta nei confronti di un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. Per ridurre il rischio di esfiltrazione di dati, è possibile usare un gruppo di sicurezza di rete (NSG). Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come configurare un collegamento privato per database di Azure per MySQL](howto-configure-privatelink-portal.md)

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare lo standard Protezione DDoS nelle reti virtuali associate alle istanze di Database di Azure per MySQL per proteggersi da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare Advanced Threat Protection per database di Azure per MySQL](howto-database-threat-protection-portal.md)

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Indicazioni**: quando l'istanza di Database di Azure per MySQL è protetta nei confronti di un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione di dati. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

- [Come configurare Advanced Threat Protection per database di Azure per MySQL](howto-database-threat-protection-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 Ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: per le risorse che devono accedere alle istanze di Database di Azure per MySQL, usare tag di servizio Rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, SQL.WestUs) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

Nota: Database di Azure per MySQL usa i tag di servizio "Microsoft.Sql".

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](../virtual-network/service-tags-overview.md)

- [Informazioni sull'utilizzo dei tag di servizio per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate alle istanze di Database di Azure per MySQL con Criteri di Azure. Usare gli alias di Criteri di Azure negli spazi dei nomi "Microsoft.DBforMySQL" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alle funzionalità di rete o alle istanze di Database di Azure per MySQL, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

Per altre informazioni, vedere i riferimenti seguenti:

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples/)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10 Documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Database di Azure per MySQL per fornire i metadati e l'organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio **Richiedi tag e il relativo valore** per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Database di Azure per MySQL. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 Configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica e i log del server, quindi inserire i log per aggregare i dati di sicurezza generati dalle istanze di Database di Azure per MySQL. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione a lungo termine. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Informazioni sui log del server per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica nelle istanze di Database di Azure per MySQL per l'accesso ai log di controllo, query lente e metriche MySQL. Verificare di abilitare in modo specifico il log di controllo di MySQL. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o allo stesso account di archiviazione.

- [Informazioni sui log del server per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Come configurare e accedere ai log di query lente per database di Azure per MySQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per MySQL](howto-configure-audit-logs-portal.md)

- [Come configurare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5 Configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: in Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per conservare i log di Database di Azure per MySQL, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Indicazioni**: analizzare e monitorare i log delle istanze di Database di Azure per MySQL per individuare un comportamento anomalo. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Per ulteriori informazioni sull'Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

È anche possibile abilitare i log del server e le impostazioni di diagnostica per MySQL e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione risposta automatica di orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarle per risolvere i problemi di sicurezza.

- [Come abilitare Advanced Threat Protection per database di Azure per MySQL (anteprima)](howto-database-threat-protection-portal.md)

- [Informazioni sui log del server per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Come configurare e accedere ai log di query lente per database di Azure per MySQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per MySQL](howto-configure-audit-logs-portal.md)

- [Come configurare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Gestire un inventario degli account amministrativi

**Indicazioni**: mantenere un inventario degli account utente con accesso amministrativo al piano di controllo (ad esempio il portale di Azure) delle istanze di Database di Azure per MySQL. Mantenere inoltre un inventario degli account amministrativi che hanno accesso al piano dati (all'interno del database stesso) delle istanze di Database di Azure per MySQL. Quando si crea il server MySQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di MySQL.

Database di Azure per MySQL non supporta il controllo degli accessi in base al ruolo predefinito, ma è possibile creare ruoli personalizzati basati su operazioni specifiche del provider di risorse.

- [Informazioni sui ruoli personalizzati per la sottoscrizione di Azure](../role-based-access-control/custom-roles.md) 

- [Informazioni sulle operazioni del provider di risorse per database di Azure per MySQL](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql)

- [Informazioni sulla gestione dell'accesso per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-security#access-management)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory (Azure ad) non ha il concetto di password predefinite.

Al momento della creazione della risorsa di Database di Azure per MySQL, Azure forza la creazione di un utente amministratore con una password complessa. Dopo aver creato l'istanza di MySQL, tuttavia, è possibile usare il primo account amministratore del server creato per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

- [Come creare account aggiuntivi per database di Azure per MySQL](howto-create-users.md)

- [Come aggiornare la password amministratore](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 Usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'uso di account amministrativi dedicati che abbiano accesso alle istanze di Database di Azure per MySQL. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

- [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Informazioni su come creare utenti amministratori in database di Azure per MySQL](howto-create-users.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: l'accesso a database di Azure per MySQL è supportato sia con nome utente/password configurato direttamente nel database, sia con un'identità di Azure Active Directory (Azure ad) e utilizzando un token Azure ad per la connessione. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database.

Separatamente, l'accesso al piano di controllo per MySQL è disponibile tramite l'API REST e supporta SSO. Per eseguire l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token Web JSON ottenuto da Azure AD.

- [Usare Azure AD per l'autenticazione con database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Informazioni sull'API REST di database di Azure per MySQL](/rest/api/mysql/)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure. Quando si usano token di Azure AD per accedere al database, è possibile richiedere l'autenticazione a più fattori per gli accessi al database.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Usare Azure AD per l'autenticazione con database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL per generare avvisi per le attività sospette.

Inoltre, è possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Usare i rilevamenti dei rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come configurare Advanced Threat Protection per database di Azure per MySQL](howto-database-threat-protection-portal.md)

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale e ad Azure Resource Manager solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Per l'accesso a Database di Azure per MySQL, è consigliabile usare Azure AD e avvalersi di un token Azure AD per connettersi. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database.

Le credenziali di Azure AD possono essere usate anche per l'amministrazione a livello di piano di gestione (ad esempio, il portale di Azure) per controllare gli account amministratore MySQL.

- [Usare Azure AD per l'autenticazione con database di Azure per MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i registri di Azure Active Directory (Azure ad) per individuare gli account obsoleti che possono includere quelli con i ruoli amministrativi di database di Azure per MySQL. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a Database di Azure per MySQL e le assegnazioni di ruoli. È consigliabile verificare regolarmente l'accesso degli utenti, ad esempio ogni 90 giorni, per assicurarsi che solo gli utenti appropriati dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: abilitare le impostazioni di diagnostica per database di Azure per MySQL e Azure Active Directory (Azure ad), inviando tutti i log a un'area di lavoro log Analytics. Configurare gli avvisi desiderati, ad esempio per i tentativi di autenticazione non riusciti, all'interno di Log Analytics.

- [Come configurare e accedere ai log di query lente per database di Azure per MySQL](/Azure/mysql/howto-configure-server-logs-in-portal)

- [Come configurare e accedere ai log di controllo per database di Azure per MySQL](/Azure/mysql/howto-configure-audit-logs-portal)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL per generare avvisi per le attività sospette.

Usare le funzionalità di protezione dell'identità e di rilevamento dei rischi di Azure Active Directory (Azure AD) per configurare risposte automatiche a azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

È anche possibile inserire i log in Azure Sentinel per un'analisi più approfondita.

- [Come configurare Advanced Threat Protection per database di Azure per MySQL](howto-database-threat-protection-portal.md)

- [Panoramica di Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il rilevamento delle istanze di Database di Azure per MySQL o delle risorse correlate che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Usare una combinazione di Collegamento privato, endpoint di servizio e/o regole del firewall per isolare e limitare l'accesso in rete alle istanze di Database di Azure per MySQL.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come configurare un collegamento privato per database di Azure per MySQL](concepts-data-access-security-private-link.md)

- [Come creare e gestire gli endpoint di servizio VNet e le regole VNet nel database di Azure per MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Come configurare le regole del firewall di database di Azure per MySQL](concepts-firewall-rules.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3 Monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: quando si usano macchine virtuali di Azure per accedere alle istanze di Database di Azure per MySQL, usare Collegamento privato, le configurazioni di rete MySQL, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di esfiltrazione di dati.

Microsoft gestisce l'infrastruttura sottostante per Database di Azure per MySQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Come attenuare i dati exfiltration per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Crittografare tutte le informazioni riservate in transito

**Indicazioni**: il Database di Azure per MySQL supporta la connessione del server MySQL alle applicazioni client tramite il protocollo SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Nel portale di Azure verificare che l'opzione "Imponi connessione SSL" sia abilitata per impostazione predefinita per tutte le istanze di Database di Azure per MySQL.

Attualmente le versioni di TLS supportate per database di Azure per MySQL sono TLS 1,0, TLS 1,1, TLS 1,2.

- [Come configurare la crittografia in transito per database di Azure per MySQL](concepts-ssl-connection-security.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Database di Azure per MySQL. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di controllo del database di Azure per MySQL, ad esempio portale di Azure. Per l'accesso al piano dati (all'interno del database stesso), usare query SQL per creare gli utenti e configurare le autorizzazioni utente. Il controllo degli accessi in base al ruolo di Azure non influisce sulle autorizzazioni dell'utente

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Come configurare l'accesso utente con SQL per il database di Azure per MySQL](howto-create-users.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni**: usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per il momento in cui le modifiche vengono apportate alle istanze di produzione di Database di Azure per MySQL e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per proteggere il database di Azure per MySQL e le risorse correlate.

Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano database di Azure per MySQL.

- [Informazioni sulle raccomandazioni del Centro sicurezza di Azure](../security-center/recommendations-reference.md)

- [Copertura delle funzionalità per i servizi PaaS di Azure nel centro sicurezza di Azure](../security-center/features-paas.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, incluse le istanze di database di Azure per MySQL, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2 Gestire i metadati degli asset

**Indicazioni**: applicare i tag alle istanze di Database di Azure per MySQL e ad altre risorse correlate, fornendo i metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 Eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze di Database di Azure per MySQL e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/index)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad elevata sicurezza, ad esempio le istanze di Database di Azure per MySQL contenenti informazioni riservate.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Database di Azure per MySQL con Criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi **Microsoft. DBforMySQL** per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alle istanze del database di Azure per MySQL, ad esempio:

Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

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

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 Archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: se si usano definizioni di Criteri di Azure personalizzate per le istanze di Database di Azure per MySQL e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare anche un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare gli alias di criteri di Azure nello spazio dei nomi **Microsoft. DBforMySQL** per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le istanze di Database di Azure per MySQL e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11 Gestire i segreti di Azure in modo sicuro

**Indicazioni**: per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure usato per accedere alle istanze di Database di Azure per MySQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per semplificare e proteggere la gestione dei segreti di Database di Azure per MySQL. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Key Vault](../key-vault/general/quick-create-portal.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: l'istanza di database di Azure per MySQL supporta l'autenticazione Azure Active Directory (Azure ad) per accedere ai database. Durante la creazione dell'istanza di Database di Azure per MySQL, è possibile fornire le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di database.

Per Macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure usato per accedere alle istanze di Database di Azure per MySQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per archiviare e recuperare le credenziali per tale istanza. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Database di Azure per MySQL, ma non viene eseguito sui contenuti del cliente.

Eseguire l'analisi preliminare del contenuto da caricare in risorse di Azure non di calcolo, ad esempio Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per MySQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Indicazioni**: Database di Azure per MySQL esegue il backup dei file di dati e del log delle transazioni. A seconda delle massime dimensioni di archiviazione supportate, vengono eseguiti backup completi e differenziali (server di archiviazione da 4 TB al massimo) o backup di snapshot (server di archiviazione fino a 16 TB al massimo). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

- [Informazioni sui backup per database di Azure per MySQL](concepts-backup.md)

- [Informazioni sulla configurazione iniziale di database di Azure per MySQL](tutorial-design-database-using-portal.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: Database di Azure per MySQL crea automaticamente backup del server e li inserisce in un archivio con ridondanza locale o geografica, in base alla scelta dell'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. 

Se si usa Azure Key Vault per archiviare le credenziali per le istanze di Database di Azure per MySQL, assicurarsi che i backup automatizzati delle chiavi siano eseguiti con regolarità. 

- [Informazioni sui backup per database di Azure per MySQL](howto-restore-server-portal.md) 

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: In Database di Azure per MySQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originario. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Testare periodicamente il ripristino delle istanze di Database di Azure per MySQL.

- [Informazioni su backup e ripristino nel database di Azure per MySQL](concepts-backup.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: Database di Azure per MySQL crea backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

- [Informazioni su backup e ripristino nel database di Azure per MySQL](concepts-backup.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

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

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
