---
title: Baseline di sicurezza di Azure per Monitoraggio di Azure
description: La Monitoraggio di Azure di sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600234"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Baseline di sicurezza di Azure per Monitoraggio di Azure

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Monitoraggio di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Monitoraggio di Azure. **I** controlli non applicabili Monitoraggio di Azure, o per i quali la responsabilità è di Microsoft, sono stati esclusi.

Per informazioni su come Monitoraggio di Azure completamente mappato ad Azure Security Benchmark, vedere il file di mapping completo Monitoraggio di Azure [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno di reti virtuali

**Indicazioni:** abilitare collegamento privato di Azure per consentire l'accesso ai servizi SaaS di Azure (ad esempio, Monitoraggio di Azure) e ai servizi clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica.

Per consentire al traffico di raggiungere Monitoraggio di Azure, usare i tag di servizio "AzureMonitor" per consentire il traffico in ingresso e in uscita attraverso i gruppi di sicurezza di rete. Per consentire al traffico di test di Monitoraggio disponibilità di raggiungere Monitoraggio di Azure, usare il tag di servizio "ApplicationInsightsAvailability" per tutto il traffico in ingresso attraverso i gruppi di sicurezza di rete. Per consentire alle notifiche di avviso di raggiungere gli endpoint dei clienti, usare il tag di servizio "ActionGroup" per consentire il traffico in ingresso attraverso i gruppi di sicurezza di rete.

Le regole di rete virtuale Monitoraggio di Azure accettare solo le comunicazioni inviate da subnet selezionate all'interno di una rete virtuale.

Usare il gateway di Log Analytics per inviare dati a un'area di lavoro Log Analytics in Monitoraggio di Azure per conto dei computer che non possono connettersi direttamente a Internet, impedendo la necessità di connettere i computer a Internet. 

- [Come configurare il collegamento privato per Monitoraggio di Azure](/azure/azure-monitor/platform/private-link-security)

- [Connettere i computer senza accesso a Internet usando il gateway di Log Analytics in Monitoraggio di Azure](/azure/azure-monitor/platform/gateway)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: Monitoraggio di Azure è un servizio di base e non supporta la distribuzione direttamente in una rete virtuale. L'infrastruttura sottostante viene gestita da Microsoft. Tuttavia, per le risorse che effettuano connessioni di rete all'offerta Monitoraggio di Azure, proteggere la rete con un gruppo di sicurezza di rete. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Quando si Monitoraggio di Azure con collegamento privato, si ottiene l'accesso alla registrazione di rete, ad esempio "Dati elaborati dall'endpoint privato (IN/OUT)".

- [Requisiti di rete per Monitoraggio di Azure di rete](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Connettere i computer senza accesso a Internet usando il gateway di Log Analytics in Monitoraggio di Azure](/azure/azure-monitor/platform/gateway)

- [Come abilitare i log di flusso del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** per consentire al traffico di raggiungere Monitoraggio di Azure, usare i tag del servizio "AzureMonitor" per consentire il traffico in ingresso e in uscita tramite i gruppi di sicurezza di rete. Per consentire al traffico di test di monitoraggio della disponibilità di raggiungere Monitoraggio di Azure, usare il tag di servizio "ApplicationInsightsAvailability" per tutto il traffico in ingresso attraverso i gruppi di sicurezza di rete. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee** guida: Monitoraggio di Azure fa parte dei servizi principali di Azure e non può essere distribuito come servizio separatamente. Monitoraggio di Azure componenti aggiuntivi, tra cui Monitoraggio di Azure Agent e Application Insights SDK, possono essere distribuiti con le risorse e ciò può influire sul livello di sicurezza di tali risorse.

- [Requisiti di rete per gli agenti Monitoraggio di Azure rete](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Connettere i computer senza accesso a Internet usando il gateway di Log Analytics in Monitoraggio di Azure](/azure/azure-monitor/platform/gateway) 

- [Vedere Introduzione a Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Come configurare i test Web di disponibilità](app/monitor-web-app-availability.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di rete correlate Monitoraggio di Azure. Creare avvisi entro Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** Monitoraggio di Azure usa i log attività per registrare le modifiche alle risorse. È possibile esportare questi log in Archiviazione di Azure, hub eventi o un'area di lavoro Log Analytics. Inserire i log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure è possibile eseguire query ed eseguire analisi sui dati, usare gli account Archiviazione di Azure per qualsiasi archiviazione a lungo termine o di archiviazione dei log.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-settings)

- [Come raccogliere i log dell'host interno della macchina virtuale di Azure con Monitoraggio di Azure](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** Monitoraggio di Azure usa i log attività, il log attività viene abilitato automaticamente e registra le operazioni eseguite sulle risorse Monitoraggio di Azure, ad esempio: chi ha avviato l'operazione, quando si è verificata l'operazione, lo stato dell'operazione e altre informazioni di controllo utili. 

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-settings)

- [Informazioni sulla registrazione e i diversi tipi di log in Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee** guida: in Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare Archiviazione di Azure account per qualsiasi archiviazione a lungo termine/di archiviazione dei log.

- [Modificare il periodo di conservazione dei dati in Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log Archiviazione di Azure account](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Linee** guida: analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati. Usare Monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel o un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introduzione alle query di Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Come eseguire query personalizzate in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: usare Centro sicurezza di Azure'area di lavoro Log Analytics per il monitoraggio e l'avviso sulle attività anomae presenti nei log di sicurezza e negli eventi. In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi in Centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come creare un avviso per i dati di log di Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, entità servizio dei gruppi e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure.

- [Come ottenere un ruolo della directory in Azure Active Directory (Azure AD) con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un accesso JUST-In-Time/Just-Enough usando i ruoli con privilegi di Azure Active Directory (Azure AD) Privileged Identity Management per i servizi Microsoft e Azure Resource Manager.

- [Azure AD Privileged Identity Management panoramica](../active-directory/privileged-identity-management/pim-configure.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** laddove possibile, usare Azure Active Directory (Azure AD) SSO invece di configurare singole credenziali autonome per ogni servizio. Usare le Centro sicurezza di Azure di gestione delle identità e degli accessi.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi Azure Active Directory basato su autenticazione

**Indicazioni:** abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori e seguire le raccomandazioni Centro sicurezza di Azure'identità e l'accesso.

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

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory report di sicurezza (Azure AD) e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare anche Verifiche di accesso alle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni Azure AD report](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** è possibile accedere alle origini Azure Active Directory (Azure AD) attività di accesso, controllo e registro eventi di rischio, che consentono di eseguire l'integrazione con qualsiasi strumento SIEM/Monitoraggio. È possibile semplificare questo processo creando impostazioni di diagnostica Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare Azure Active Directory funzionalità di rischio (Azure AD) e Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag quando possibile per tenere traccia Monitoraggio di Azure risorse che archiviano o elaborano informazioni riservate come le aree di lavoro Log Analytics.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure](/azure/azure-monitor/platform/manage-access)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare l'isolamento usando sottoscrizioni e gruppi di gestione separati per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di riservatezza dei dati. È possibile limitare il livello di accesso alle risorse Monitoraggio di Azure e alle risorse correlate richieste dalle applicazioni e dall'ambiente aziendale. È possibile controllare l'accesso alle Monitoraggio di Azure tramite il controllo degli accessi in base al ruolo di Azure.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** Monitoraggio di Azure negozia TLS 1.2 per impostazione predefinita. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1.2 o versione successiva. 

Application Insights e Log Analytics continuano a consentire l'inserimento dei dati TLS 1.1 e TLS 1.0. I dati possono essere limitati a TLS 1.2 configurando sul lato client.

- [Come inviare dati in modo sicuro usando TLS 1.2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Monitoraggio di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.
Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso Monitoraggio di Azure.

- [Ruoli, autorizzazioni e sicurezza in monitoraggio di Azure](/azure/azure-monitor/platform/roles-permissions-security)

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee** guida: Monitoraggio di Azure che tutti i dati e le query salvate siano crittografati in stato di inquieto usando chiavi gestite da Microsoft (MMK). Monitoraggio di Azure offre anche un'opzione per la crittografia usando la propria chiave archiviata nel Azure Key Vault e accessibile tramite l'archiviazione tramite l'autenticazione dell'identità gestita assegnata dal sistema. Questa chiave gestita dal cliente (CMK) può essere protetta da software o hardware-HSM.

- [Monitoraggio di Azure chiavi gestite dal cliente](/azure/azure-monitor/platform/customer-managed-keys)

- [Sicurezza dei dati di Log Analytics](/azure/azure-monitor/platform/data-security)

- [Raccolta, conservazione e archiviazione di dati in Application Insights](app/data-retention-privacy.md)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee** guida: usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche Monitoraggio di Azure risorse correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee** guida: usare un programma di assegnazione dei punteggi di rischio comune (ad esempio, Common Vulnerability Scoring System) o le classificazioni dei rischi predefinite fornite dallo strumento di analisi di terze parti.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni:** usare l'interfaccia della riga di comando di Azure per eseguire query e individuare Monitoraggio di Azure risorse all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Monitoraggio di Azure'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Ruoli, autorizzazioni e sicurezza in monitoraggio di Azure](/azure/azure-monitor/platform/roles-permissions-security)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle Monitoraggio di Azure fornendo metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia Monitoraggio di Azure risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni:** creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni:** riconciliare l'inventario a intervalli regolari e assicurarsi che Monitoraggio di Azure risorse correlate non autorizzate siano eliminate dalla sottoscrizione in modo corretto.  

- [Eliminare l'area di lavoro Azure Log Analytics](/azure/azure-monitor/platform/delete-workspace)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per limitare le risorse Monitoraggio di Azure cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resources Manager configurando "Blocca l'accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: usare definizioni Criteri di Azure personalizzate per controllare o applicare la configurazione Monitoraggio di Azure risorse correlate. È anche possibile usare definizioni di Criteri di Azure incorporate come disponibili.

Inoltre, Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare raccomandazioni di Centro sicurezza di Azure come baseline di configurazione sicura per le risorse di Azure.

Se si usano le funzionalità APM per lo streaming live, rendere sicuro il canale con una chiave API segreta oltre alla chiave di strumentazione.

- [Proteggere APM Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee** guida: usare i criteri di Azure [deny] e [distribuisci se non esistono] per applicare impostazioni sicure Monitoraggio di Azure risorse correlate.  È anche possibile usare modelli Azure Resource Manager per mantenere la configurazione di sicurezza Monitoraggio di Azure risorse correlate richieste dall'organizzazione.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee** guida: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati Azure Resource Manager modelli. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps o Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard Monitoraggio di Azure risorse correlate usando Criteri di Azure. Usare definizioni Criteri di Azure personalizzate per controllare o applicare la configurazione di sicurezza delle Monitoraggio di Azure correlate. È anche possibile usare definizioni di criteri incorporate correlate alle risorse specifiche.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri di Azure alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Centro sicurezza di Azure per eseguire analisi di base per Monitoraggio di Azure risorse correlate.  È anche possibile usare Criteri di Azure avvisi e controllare le configurazioni delle risorse di Azure.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare l'identità del servizio gestita in combinazione con Azure Key Vault per semplificare e proteggere la gestione dei segreti per le risorse correlate a Monitoraggio di Azure supportate.

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Come creare un Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come fornire l'Key Vault con un'identità gestita](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluse le Monitoraggio di Azure, senza credenziali nel codice.

- [Come configurare le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come configurare lo scanner delle credenziali](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee** guida: Microsoft Anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Monitoraggio di Azure risorse correlate), ma non viene eseguito sul contenuto. 

Eseguire la pre-analisi di tutti i file caricati in Monitoraggio di Azure risorse correlate, ad esempio l'area di lavoro Log Analytics.

Usare Centro sicurezza di Azure rilevamento delle minacce per i servizi dati per rilevare il malware caricato in account di archiviazione. 

- [Informazioni su Antimalware Microsoft per Servizi cloud di Azure e macchine virtuali](../security/fundamentals/antimalware.md)

- [Informazioni Centro sicurezza di Azure rilevamento delle minacce di Centro sicurezza di Azure per i servizi dati](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** usare Azure Resource Manager per esportare il Monitoraggio di Azure e le risorse correlate in un modello JavaScript Object Notation (JSON) che può essere usato come backup per Monitoraggio di Azure e le configurazioni correlate.  Usare Automazione di Azure per eseguire automaticamente gli script di backup. 

- [Gestire l'area di lavoro Log Analytics Azure Resource Manager modelli](/azure/azure-monitor/samples/resource-manager-workspace)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Informazioni su Automazione di Azure](../automation/automation-intro.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** usare Azure Resource Manager per esportare il Monitoraggio di Azure e le risorse correlate in un modello JavaScript Object Notation (JSON) che può essere usato come backup per Monitoraggio di Azure e le configurazioni correlate. Eseguire il backup delle chiavi gestite dal cliente Azure Key Vault se Monitoraggio di Azure risorse correlate usano chiavi gestite dal cliente,

- [Gestire l'area di lavoro Log Analytics Azure Resource Manager modelli](/azure/azure-monitor/platform/template-workspace-configuration)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** garantire la possibilità di eseguire periodicamente il ripristino usando Azure Resource Manager file modello supportati. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Gestire l'area di lavoro Log Analytics Azure Resource Manager modelli](/azure/azure-monitor/samples/resource-manager-workspace)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure Azure Resource Manager modelli. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD) se integrato con Azure DevOps o Active Directory se integrato con TFS.  Usare il controllo degli accessi in base al ruolo di Azure per proteggere le chiavi gestite dal cliente.

Abilitare inoltre l'Soft-Delete la protezione dall'eliminazione Key Vault proteggere le chiavi da eliminazioni accidentali o dannose. Se Archiviazione di Azure viene usato per archiviare i backup Azure Resource Manager modello, abilitare l'eliminazione soft per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot blob.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Come abilitare la Soft-Delete e la protezione dall'eliminazione in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o nell'analisi usata per l'emissione dell'avviso, nonché sul livello di attendibilità che si è verificato un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

- [Come configurare l'esportazione continua](../security-center/continuous-export.md) 

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee** guida: usare la funzionalità Automazione flusso di lavoro Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" sugli avvisi di sicurezza e sulle raccomandazioni per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Azure Security Benchmark: Penetration Tests and Red Team Exercises](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee** guida: seguire le regole microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft red teaming e i test di penetrazione del sito in tempo reale rispetto all'infrastruttura cloud gestita da Microsoft, ai servizi e alle applicazioni.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
