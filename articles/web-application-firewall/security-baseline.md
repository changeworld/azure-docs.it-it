---
title: Baseline di sicurezza di Azure per Web application firewall di Azure
description: La Web application firewall di Azure di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875964"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Baseline di sicurezza di Azure per Web application firewall di Azure

Questa baseline di sicurezza applica le linee guida di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) a Web application firewall di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili Web application firewall di Azure. 

> [!NOTE]
> **I** controlli non applicabili Web application firewall di Azure, o per i quali la responsabilità è di Microsoft, sono stati esclusi. Per informazioni su come Web application firewall di Azure completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Web application firewall di Azure **[baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee** guida: usare Microsoft Azure Web Application Firewall (WAF) per la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni, ad esempio sql injection e script tra siti. 

- Modalità di rilevamento: usare questa modalità per apprendere il traffico di rete, comprendere ed esaminare i falsi positivi. Monitora e registra tutti gli avvisi di minaccia. Assicurarsi che diagnostica e registro WAF siano selezionati e attivati. Si noti che waf non blocca le richieste in ingresso quando è in modalità di rilevamento.
- Modalità di prevenzione: blocca le intrusioni e gli attacchi rilevati dalle regole. Un utente malintenzionato riceve un'eccezione "403 accesso non autorizzato" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

Basare il traffico di rete con la modalità di rilevamento di WAF. Dopo aver determinato le esigenze di traffico, configurare WAF in modalità prevenzione per bloccare il traffico indesiderato.

Seguire le raccomandazioni di gravità elevata dal Centro sicurezza per tutte le risorse abilitate per il Web che non sono protette da WAF.  

- [Regole e gruppi di regole CRS di Web Application Firewall](ag/application-gateway-crs-rulegroups-rules.md) 

- [Modalità WAF nel gateway applicazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modalità WAF in Front door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** usare regole personalizzate con il Web application firewall di Azure (WAF) per consentire e bloccare il traffico. Ad esempio, tutto il traffico proveniente da un intervallo di indirizzi IP può essere bloccato. Configurare Azure WAF per l'esecuzione in modalità di prevenzione che blocca le intrusioni e gli attacchi rilevati dalle regole. Un utente malintenzionato riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Modalità WAF nel gateway applicazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modalità WAF in Front door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni:** Web application firewall di Azure (WAF) è un componente fondamentale delle protezioni delle applicazioni Web di Azure. Usare Azure WAF per fornire una protezione centralizzata per le applicazioni Web da exploit e vulnerabilità comuni con un set di regole gestito preconfigurato contro le firme di attacco note dalle prime 10 categorie OWASP.

Personalizzare Azure WAF con regole e gruppi di regole per soddisfare i requisiti delle applicazioni Web ed eliminare i falsi positivi. Associare un criterio waf di Azure per ogni sito dietro un WAF per consentire la configurazione specifica del sito. Azure WAF supporta il filtro geografico, la limitazione della frequenza e le regole del set di regole predefinite gestite da Azure. è possibile creare regole personalizzate per soddisfare le esigenze di un'applicazione. 

Configurare il WAF di Azure per l'esecuzione in modalità di prevenzione dopo aver configurato il traffico di rete in modalità rilevamento per un determinato periodo di tempo. Il WAF di Azure blocca le intrusioni e gli attacchi rilevati dalle regole in modalità prevenzione. Un utente malintenzionato riceve un'eccezione "403 accesso non autorizzato" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Modalità WAF nel gateway applicazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modalità WAF in Front door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Regole e gruppi di regole CRS di Web Application Firewall](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee** guida: creare, associare e organizzare in modo logico le risorse in una sottoscrizione di Azure con tag per rilevare errori di configurazione comuni dell'applicazione ,ad esempio Apache e IIS. 

Applicare regole e gruppi di regole Web application firewall di Azure (WAF) in base ai metadati dei tag applicati.

- [Criteri WAF nel gateway applicazione](/cli/azure/network/application-gateway/waf-policy)

- [Criteri WAF su Front door](/cli/azure/network/front-door/waf-policy)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per i gruppi di sicurezza di rete associati al Web application firewall di Azure (WAF) nella subnet gateway applicazione di Azure, nonché per qualsiasi altra risorsa correlata alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare esigenze aziendali, durata e così via, per tutte le regole che consentono il traffico da o verso una rete.

Usare una qualsiasi delle definizioni di Criteri di Azure incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse esistenti senza tag.

Scegliere Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni di Web application firewall di Azure (WAF). Creare avvisi all'interno Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni di rete o alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni:** creare una regola di rete per Web application firewall di Azure (WAF) per consentire l'accesso a un server NTP con la porta e il protocollo appropriati, ad esempio la porta 123 su UDP.

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** configurare i log di Web application firewall di Azure (WAF) da inviare a una soluzione centrale di gestione dei log di sicurezza, ad esempio Azure Sentinel o un siEM di terze parti. Questi log includono i log attività di Azure, diagnostica e WAF in tempo reale, che possono quindi essere visualizzati in diversi strumenti, ad esempio Monitoraggio di Azure, Excel e Power BI. Web application firewall di Azure log forniscono informazioni dettagliate sui dati che il WAF di Azure sta valutando, abbinando e bloccando.

Azure Sentinel include una cartella di lavoro di Azure WAF predefinita, che offre una panoramica degli eventi di sicurezza in Azure WAF. Questa cartella di lavoro include eventi, regole corrispondenti e bloccate e tutto il resto che viene registrato nei log del firewall. Questi dati di telemetria possono essere usati per avviare l'automazione dei playbook per inviare notifiche o eseguire azioni di correzione in base agli eventi WAF raccolti Azure Sentinel.

- [Visualizzare i log attività](../azure-resource-manager/management/view-activity-logs.md)

- [Log di diagnostica per il gateway applicazione](../application-gateway/application-gateway-diagnostics.md)

- [Connettere i dati da Microsoft web application firewall a Azure Sentinel](../sentinel/connect-azure-waf.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare la registrazione nelle risorse Web application firewall di Azure (WAF) per l'accesso ai log di controllo, sicurezza e diagnostica. Web application firewall di Azure fornisce report dettagliati su ognuna delle minacce rilevate che vengono rese disponibili nei log di diagnostica configurati. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Panoramica della registrazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Monitoraggio di Azure panoramica delle query di log](../azure-monitor/logs/log-query-overview.md)

- [Panoramica dei log della piattaforma Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** inviare i log Web application firewall di Azure (WAF) a un account di archiviazione personalizzato e definire i criteri di conservazione. Usare Monitoraggio di Azure per impostare il periodo di conservazione dell'area di lavoro Log Analytics in base ai requisiti di conformità dell'organizzazione.
- [Configurare il monitoraggio per un account di archiviazione](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Linee** guida: Web application firewall di Azure (WAF) fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con Diagnostica di Azure log che forniscono informazioni dettagliate su operazioni ed errori importanti per il controllo e la risoluzione dei problemi. 

Le istanze di Azure WAF sono integrate con il Centro sicurezza per inviare avvisi e informazioni sull'integrità per la creazione di report. Usare le raccomandazioni del Centro sicurezza per rilevare applicazioni Web non protette e proteggere queste risorse vulnerabili. 

Azure Sentinel include una cartella di lavoro predefinita waf - eventi firewall, che offre una panoramica degli eventi di sicurezza nel WAF. Questi includono eventi, regole corrispondenti e bloccate e tutto il resto che viene registrato nei log del firewall.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

- [Come abilitare le impostazioni di diagnostica per gateway applicazione di Azure](../application-gateway/application-gateway-diagnostics.md)

- [Monitoraggio di metriche e log in Frontdoor di Azure](../frontdoor/front-door-diagnostics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per azure WAF e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti. Creare avvisi per attività anomale in base alle metriche di WAF. Ad esempio, se il numero di richieste bloccate supera 'X', eseguire 'Y'.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Come creare avvisi in Azure](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee** guida: distribuire Web application firewall di Azure (WAF) davanti alle applicazioni Web critiche per un'ispezione aggiuntiva del traffico in ingresso. 

Azure WAF offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni e protegge le app controllando il traffico Web in ingresso per bloccare attacchi come attacchi SQL injection, cross-site scripting, caricamenti di malware e attacchi DDoS.

- [Come distribuire Azure WAF](ag/create-waf-policy-ag.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** Azure Active Directory (Azure AD) ha ruoli predefiniti che sono in grado di eseguire query e devono essere assegnati in modo esplicito. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** non sono disponibili assegnazioni di amministratore locale all'interno del WAF. Tuttavia, potrebbero essere presenti Azure Active Directory (Azure AD) nell'ambiente che potrebbero consentire il controllo della gestione sulle risorse di Azure WAF.
È consigliabile creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso alle Web application firewall di Azure (WAF). Usare le funzionalità di gestione delle identità e degli accessi del Centro sicurezza per monitorare il numero di account amministrativi.

- [Informazioni Centro sicurezza di Azure'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Informazioni su come creare utenti amministratori in Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi Azure Active Directory basato su autenticazione

**Indicazioni:** abilitare l Azure Active Directory a più fattori (Azure AD) e seguire le raccomandazioni di Gestione delle identità e degli accessi del Centro sicurezza.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare workstation PAW (Privileged Access Workstation) con autenticazione a più fattori configurata per accedere e configurare Web application firewall di Azure (WAF) e le risorse correlate.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Linee** guida: usare Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza per monitorare l'identità e l'attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** configurare la condizione di posizione di un criterio di accesso condizionale e gestire le località denominate.

Creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche con località denominate. Limitare l'accesso alle risorse sensibili, ad esempio Azure Key Vault segreti, alle posizioni denominate configurate.

- [Qual è la condizione di posizione nell'Azure Active Directory condizionale (Azure AD)](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati in stato di inquieto e in transito, oltre a salt, hash e archivia in modo sicuro le credenziali utente.
- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare Le verifiche di accesso alle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Esaminare regolarmente l'accesso degli utenti per assicurarsi che l'accesso continui solo agli utenti attivi.

- [Informazioni Azure AD report](/azure/active-directory/reports-monitoring)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Linee** guida: integrare Azure Active Directory (Azure AD) origini del registro eventi di accesso, controllo e rischio, con qualsiasi strumento di monitoraggio o SIEM, ad esempio Azure Sentinel.

Semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare le Azure Active Directory di rischio (Azure AD) di Azure AD e Identity Protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. Inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag per facilitare il rilevamento Web application firewall di Azure (WAF) e le risorse correlate che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare l'isolamento usando sottoscrizioni e gruppi di gestione separati per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di riservatezza dei dati, ad esempio gli ambienti di sviluppo, test e produzione. 

Controllare l'accesso alle risorse di Azure con Azure Active Directory (Azure AD) controllo degli accessi in base al ruolo di Azure.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle istanze di Web application firewall di Azure (WAF) e alle risorse correlate siano in grado di negoziare TLS 1.2 o versione successiva.

Se applicabile, seguire le raccomandazioni del Centro sicurezza per la crittografia dei dati in pausa e la crittografia in transito.

- [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse 

**Indicazioni:** controllare l'accesso alle risorse di Azure, ad esempio Web Application Firewall con il controllo degli accessi in base al ruolo di Azure.

- [Come configurare il controllo degli accessi in base al ruolo di Azure in Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** usare la crittografia in pausa per tutte le risorse di Azure, inclusi Web application firewall di Azure (WAF) e le risorse correlate. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia, tuttavia in alcuni casi è possibile gestire le proprie chiavi.

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee** guida: configurare Web application firewall di Azure (WAF) per l'esecuzione in modalità prevenzione dopo aver configurato il traffico di rete in modalità rilevamento per un periodo di tempo predeterminato. 

Il waf di Azure, in modalità prevenzione, blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Panoramica dell'integrazione tra il gateway applicazione e il Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [Modalità WAF nel gateway applicazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modalità WAF in Front door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query o individuare tutte le risorse, ad esempio calcolo, archiviazione, rete, porte e protocolli e così via all'interno delle sottoscrizioni.

Assicurarsi le autorizzazioni appropriate (lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni. Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** usare i tag nei Web application firewall di Azure (WAF). I tag possono essere associati alle risorse e applicati logicamente per organizzare l'accesso a queste risorse in una sottoscrizione del cliente. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia di Azure WAF e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni:** creare un inventario delle risorse approvate, inclusa la configurazione in base alle esigenze dell'organizzazione.

Usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.
Usare Azure Resource Graph per eseguire query o individuare Web application firewall di Azure (WAF) all'interno delle sottoscrizioni. Assicurarsi che tutte le risorse di Azure WAF e le risorse correlate presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni:** monitorare e rimuovere le risorse WAF di Azure non approvate con Criteri di Azure per negare la distribuzione di Azure WAF o un determinato tipo di WAF, ad esempio Azure WAF v1 e V2.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni:** usare l'accesso condizionale di Azure per limitare la capacità di un utente di interagire con Azure Resources Manager configurando "Blocca l'accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni:** Web application firewall di Azure (WAF) può essere associato a ambienti diversi tramite reti, gruppi di risorse o sottoscrizioni per separare le applicazioni ad alto rischio.

- [Panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)

- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)

- [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni di Web application firewall di Azure (WAF).

Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei gateway applicazione Azure, delle reti virtuali, dei gruppi di sicurezza di rete e usare le definizioni dei criteri predefiniti.

- [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee** guida: usare Criteri di Azure effetti [nega] e [distribuisci se non esiste] per applicare impostazioni sicure tra il Web application firewall di Azure (WAF) e le risorse correlate. 

Usare Azure Resource Manager modelli per mantenere la configurazione di sicurezza del waf di Azure e le risorse correlate richieste dall'organizzazione.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee** guida: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati Azure Resource Manager modelli.

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza predefiniti o gruppi definiti in Azure Active Directory (Azure AD), se integrati con Azure DevOps o Azure AD, se integrati con Team Foundation Server (TFS).

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informazioni su autorizzazioni e gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee** guida: usare definizioni Criteri di Azure predefinite e alias Criteri di Azure nello spazio dei nomi "Microsoft.Network" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Documentazione di Criteri di Azure](/azure/governance/policy)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare le definizioni Criteri di Azure predefinite e gli alias Criteri di Azure nello spazio dei nomi "Microsoft.Network" per creare criteri personalizzati per avvisare, controllare e applicare configurazioni di sistema. 

Usare Criteri di Azure effetti [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Documentazione di Criteri di Azure](/azure/governance/policy)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare Azure Key Vault per archiviare in modo sicuro i certificati. Azure Key Vault è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere segreti, chiavi e certificati SSL. 

Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. 

- [Come configurare la terminazione SSL con Key Vault certificati usando Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni:** implementare lo scanner delle credenziali per identificare le credenziali all'interno del codice, in modo da incoraggiare anche lo spostamento delle credenziali individuate in posizioni più sicure, ad esempio Azure Key Vault.
- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** assicurarsi che l'eliminazione soft sia abilitata per Azure Key Vault. L'eliminazione soft consente il ripristino di insiemi di credenziali delle chiavi e oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati.

- [Come usare l Azure Key Vault di eliminazione](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni:** sviluppare una guida alla risposta agli eventi imprevisti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono tutti i ruoli del personale, nonché le fasi di gestione e gestione degli eventi imprevisti, dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Usare la Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni:** il Centro sicurezza assegna una gravità a ogni avviso per definire la priorità degli avvisi da ricercare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sulla metrica usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

Contrassegnare chiaramente le sottoscrizioni (ad esempio, produzione, non di produzione) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.
- [Fare riferimento alla Guida alla pubblicazione di NIST per i programmi di test, training ed esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.
- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni:** Esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità Esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. Usare il connettore Centro sicurezza di Azure dati per trasmettere gli avvisi Azure Sentinel in base ai requisiti dell'organizzazione.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Linee** guida: seguire le regole microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft red teaming e i test di penetrazione del sito in tempo reale rispetto all'infrastruttura cloud gestita da Microsoft, ai servizi e alle applicazioni. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
