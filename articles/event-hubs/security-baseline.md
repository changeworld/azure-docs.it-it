---
title: Baseline di sicurezza di Azure per Hub eventi
description: La baseline di sicurezza di Hub eventi fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587154"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Baseline di sicurezza di Azure per Hub eventi

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) a Hub eventi. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai **controlli di sicurezza** definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Hub eventi. **I** controlli non applicabili a Hub eventi sono stati esclusi.

 
Per informazioni sul mapping completo di Hub eventi ad Azure Security Benchmark, vedere il file di mapping completo della baseline di [sicurezza di Hub eventi.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno di reti virtuali

**Indicazioni:** l'integrazione di Hub eventi di Azure con gli endpoint servizio di rete virtuale consente l'accesso sicuro alle funzionalità di messaggistica da carichi di lavoro quali macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Una volta associato ad almeno un endpoint servizio della subnet di rete virtuale, il rispettivo spazio dei nomi di Hub eventi non accetta più il traffico da qualsiasi posizione, ma da subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione dello spazio dei nomi di Hub eventi a un endpoint di servizio configura un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

È anche possibile creare un endpoint privato, ovvero un'interfaccia di rete che connette l'utente privatamente e in modo sicuro al servizio Hub eventi usando il collegamento privato di Azure servizio. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando in modo efficace il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. 

È anche possibile proteggere lo spazio dei nomi Hub eventi di Azure usando i firewall. Hub eventi supporta i controlli di accesso basati su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando i modelli portale di Azure, Azure Resource Manager o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Come usare gli endpoint del servizio di rete virtuale con Hub eventi di Azure](event-hubs-service-endpoints.md)

- [Integrare Hub eventi di Azure con Collegamento privato di Azure](private-link-service.md)

- [Come configurare le regole del firewall IP per Hub eventi di Azure spazi dei nomi](event-hubs-ip-filtering.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione di rete per proteggere le risorse di Hub eventi in Azure. Se si usano macchine virtuali di Azure per accedere agli hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP noti dannosi

**Indicazioni:** abilitare Protezione DDoS Standard nelle reti virtuali associate agli hub eventi per la protezione dagli attacchi DDoS (Distributed Denial of Service). Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Per altre informazioni sull'Centro sicurezza di Azure integrated threat intelligence](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** se si usano macchine virtuali di Azure per accedere agli hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Se necessario per analizzare l'attività anomaa, abilitare Network Watcher'acquisizione di pacchetti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** se si usano macchine virtuali di Azure per accedere agli hub eventi, selezionare un'offerta dall'Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload. Se il rilevamento e/o la prevenzione delle intrusioni in base all'ispezione del payload non sono necessari per l'organizzazione, è possibile usare Hub eventi di Azure predefinita del firewall. È possibile limitare l'accesso allo spazio dei nomi di Hub eventi per un intervallo limitato di indirizzi IP o per un indirizzo IP specifico usando le regole del firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come aggiungere una regola del firewall in Hub eventi per un indirizzo IP specificato](event-hubs-ip-filtering.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate Hub eventi di Azure spazi dei nomi con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi **Microsoft.EventHub** e **Microsoft.Network** per creare definizioni di criteri personalizzate per controllare o applicare la configurazione di rete degli spazi dei nomi di Hub eventi. È anche possibile usare le definizioni di criteri predefiniti correlate Hub eventi di Azure, ad esempio:

- Hub eventi deve usare un endpoint servizio di rete virtuale.

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri predefiniti di Azure per lo spazio dei nomi di Hub eventi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Criteri di Azure esempi per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per le reti virtuali e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati agli hub eventi.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate Hub eventi di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** in Monitoraggio di Azure configurare i log correlati agli hub eventi all'interno delle impostazioni di diagnostica del log attività e dell'hub eventi per inviare i log in un'area di lavoro Log Analytics su cui eseguire query o in un account di archiviazione per l'archiviazione a lungo termine.

- [Come configurare le impostazioni di diagnostica per Hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Informazioni sul log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee** guida: abilitare le impostazioni di diagnostica per lo spazio Hub eventi di Azure dati. Esistono tre categorie di impostazioni di diagnostica per Hub eventi di Azure: log di archiviazione, log operativi e log di scalabilità automatica. Abilitare i log operativi per acquisire informazioni su ciò che accade durante le operazioni di Hub eventi, in particolare il tipo di operazione, tra cui la creazione dell'hub eventi, le risorse usate e lo stato dell'operazione.

È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle a un account Archiviazione di Azure, a un hub eventi o a un'area di lavoro Log Analytics. I log attività forniscono informazioni dettagliate sulle operazioni eseguite sul Hub eventi di Azure e altre risorse. Usando i log attività, è possibile determinare "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sugli spazi dei nomi Hub eventi di Azure dati.

- [Come abilitare le impostazioni di diagnostica per Hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** nell'Monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione per acquisire ed esaminare gli eventi imprevisti correlati all'hub eventi.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati correlati agli hub eventi. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel una soluzione di gestione di eventi e informazioni di sistema di terze parti.

- [Per altre informazioni sull'area di lavoro Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** in Monitoraggio di Azure configurare i log correlati a Hub eventi di Azure all'interno del log attività e le impostazioni di diagnostica di Hub eventi per inviare i log in un'area di lavoro Log Analytics su cui eseguire query o in un account di archiviazione per l'archiviazione a lungo termine. Usare l'area di lavoro Log Analytics per creare avvisi per le attività anomae trovate nei log di sicurezza e negli eventi.

In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel. 

- [Informazioni sul log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Come configurare le impostazioni di diagnostica per Hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Come creare un avviso per i dati di log dell'area di lavoro Log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono disponibili per query. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee** guida: l'accesso del piano di controllo a Hub eventi è controllato tramite Azure Active Directory (Azure AD). Azure AD non ha il concetto di password predefinite.

L'accesso al piano dati a Hub eventi è controllato tramite Azure AD con identità gestite o Registrazioni app nonché firme di accesso condiviso. Le firme di accesso condiviso vengono usate dai client che si connettono agli hub eventi e possono essere rigenerate in qualsiasi momento.

- [Informazioni sulla firma di accesso condiviso per Hub eventi](authenticate-shared-access-signature.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Linee** guida: Microsoft Azure fornisce la gestione integrata del controllo di accesso per risorse e applicazioni basate Azure Active Directory (Azure AD). Un vantaggio principale dell'Azure AD con Hub eventi di Azure è che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma Microsoft Identity. Il nome della risorsa per richiedere un token è https: \/ /eventhubs.azure.net/. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta Hub eventi di Azure risorse.

- [Come autenticare un'applicazione con Azure AD accedere alle risorse di Hub eventi](authenticate-application.md)

- [Informazioni sull'accesso SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Indicazioni:** abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori e seguire le raccomandazioni di gestione delle identità e degli accessi di Centro sicurezza di Azure per proteggere le risorse abilitate per l'hub eventi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare workstation con accesso con privilegi (PAW) con l'autenticazione a più fattori configurata per accedere e configurare le risorse abilitate per Hub eventi.

- [Informazioni sulle workstation con accesso con privilegi](/security/compass/privileged-access-devices)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare in caso di attività sospette da account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) Privileged Identity Management per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare Azure AD rilevamento dei rischi per visualizzare avvisi e report sul comportamento degli utenti rischiosi. Per una registrazione aggiuntiva, inviare Centro sicurezza di Azure avvisi di rilevamento dei rischi Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le risorse di Azure, ad esempio Hub eventi. Ciò consente il controllo degli accessi in base al ruolo di Azure alle risorse sensibili amministrative.

- [ Come creare e configurare un'istanza Azure AD servizio](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure AD](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare anche le verifiche di accesso delle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Inoltre, ruotare regolarmente le firme di accesso condiviso di Hub eventi.

- [Informazioni Azure AD report](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulle firme di accesso condiviso per Hub eventi](authenticate-shared-access-signature.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** è possibile accedere alle origini del log eventi di Azure Active Directory (Azure AD), di controllo e di rischio, che consentono di integrarsi con qualsiasi soluzione di gestione degli eventi e delle informazioni di sistema di terze parti o con uno strumento di monitoraggio.

È possibile semplificare questo processo creando impostazioni di diagnostica Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi di log desiderati in Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autorizzare l'accesso alle risorse di Hub eventi usando Azure AD](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Linee** guida: usare Identity Protection e le funzionalità di rilevamento dei rischi all'interno di Azure Active Directory (Azure AD) per configurare risposte automatiche alle azioni sospette rilevate correlate alle risorse abilitate per Hub eventi. È consigliabile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida:** attualmente non disponibile; Customer Lockbox non è ancora supportato per Hub eventi.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag nelle risorse correlate a Hub eventi per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee** guida: implementare sottoscrizioni separate, facoltativamente con gruppi di gestione per lo sviluppo, il test e la produzione. Gli spazi dei nomi di Hub eventi devono essere separati dalla rete virtuale con endpoint di servizio abilitati e contrassegnati in modo appropriato.

È anche possibile proteggere lo spazio dei nomi Hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basati su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando i modelli portale di Azure, Azure Resource Manager o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Configurare le regole del firewall IP per Hub eventi di Azure spazi dei nomi](event-hubs-ip-filtering.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** quando si usano macchine virtuali per accedere agli hub eventi, usare reti virtuali, endpoint di servizio, firewall di Hub eventi, gruppi di sicurezza di rete e tag di servizio per ridurre la possibilità di esfiltrazione dei dati.

Microsoft gestisce l'infrastruttura sottostante per Hub eventi di Azure e ha implementato rigorosi controlli per impedire la perdita o l'esposizione dei dati dei clienti.

- [Configurare le regole del firewall IP per Hub eventi di Azure spazi dei nomi](event-hubs-ip-filtering.md)

- [Informazioni su endpoint servizio di rete virtuale con Hub eventi di Azure](event-hubs-service-endpoints.md)

- [Integrare Hub eventi di Azure con Collegamento privato di Azure](private-link-service.md)

- [Informazioni su gruppi di sicurezza di rete e tag di servizio](../virtual-network/network-security-groups-overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Hub eventi di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** Hub eventi di Azure supporta l'Azure Active Directory (Azure AD) per autorizzare le richieste alle risorse di Hub eventi. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure e sui ruoli disponibili per Hub eventi di Azure](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni:** Hub eventi di Azure supporta la possibilità di crittografare i dati in stato di inquieto con chiavi gestite da Microsoft o chiavi gestite dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare Hub eventi di Azure dati inattivi.

- [Come configurare le chiavi gestite dal cliente per la crittografia Hub eventi di Azure](configure-customer-managed-key.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di Hub eventi di Azure e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Linee** guida: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi Hub eventi di Azure spazi dei nomi) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

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

**Linee** guida: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, ove appropriato, per organizzare e tenere traccia Hub eventi di Azure spazi dei nomi e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

È anche possibile usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Configurazione sicura.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per Hub eventi di Azure distribuzione. Usare Criteri di Azure alias nello spazio dei **nomi Microsoft.EventHub** per creare criteri personalizzati per controllare o applicare le configurazioni. È anche possibile usare definizioni di criteri predefiniti per Hub eventi di Azure ad esempio:

- È consigliabile abilitare i log di diagnostica in Hub eventi

- Gli hub eventi devono usare un endpoint servizio di rete virtuale

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Criteri predefiniti di Azure per lo spazio dei nomi di Hub eventi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Come visualizzare gli alias Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni:** usare Criteri di Azure effetti [nega] e [distribuisci se non esiste] per applicare impostazioni sicure tra le risorse abilitate per Hub eventi. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

 
- [Per altre informazioni sugli effetti Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare Criteri di Azure effetti [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le distribuzioni Hub eventi di Azure e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione in Servizio app di Azure usate per accedere agli hub eventi, usare l'identità del servizio gestita in combinazione con Azure Key Vault per semplificare e proteggere la gestione delle firme di accesso condiviso per le distribuzioni Hub eventi di Azure. Assicurarsi Key Vault sia configurata con l'eliminazione soft abilitata.

- [Autenticare un'identità gestita con Azure Active Directory (Azure AD) per accedere alle risorse di Hub eventi](./authenticate-managed-identity.md?tabs=latest)

- [Configurare le chiavi gestite dal cliente per Hub eventi](configure-customer-managed-key.md)

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare un Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come fornire l'Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee** guida: per le macchine virtuali di Azure o le applicazioni Web in esecuzione in Servizio app di Azure usate per accedere agli hub eventi, usare l'identità del servizio gestita in combinazione con Azure Key Vault per semplificare e proteggere le Hub eventi di Azure. Assicurarsi Key Vault configurata con l'eliminazione soft abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice.

- [Autenticare un'identità gestita con Azure AD accedere alle risorse di Hub eventi](./authenticate-managed-identity.md?tabs=latest)

- [Configurare le chiavi gestite dal cliente per Hub eventi](configure-customer-managed-key.md)

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee** guida: eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio Hub eventi di Azure, Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non può accedere ai dati in queste istanze.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sul contenuto del cliente.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Indicazioni:** configurare il ripristino di emergenza geografico per Hub eventi di Azure. In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate zone di disponibilità), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il ripristino di emergenza geografico e la replica geografica sono quindi funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

- [Informazioni sul ripristino di emergenza geografico per Hub eventi di Azure](./event-hubs-geo-dr.md#availability-zones)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** Hub eventi di Azure la crittografia dei dati in pausa con Archiviazione di Azure Service Encryption (Azure SSE). Hub eventi si basa Archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft. Se si usa Azure Key Vault per archiviare le chiavi gestite dal cliente, assicurarsi di eseguire backup automatici regolari delle chiavi.

Assicurarsi di eseguire backup automatici regolari dei segreti Key Vault con il comando powershell seguente: Backup-AzKeyVaultSecret

- [Come configurare le chiavi gestite dal cliente per crittografare Hub eventi di Azure dati in stato di inquieto](configure-customer-managed-key.md)

- [Come eseguire il backup Key Vault segreti](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** abilitare l'eliminazione Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Hub eventi di Azure richiede che le chiavi gestite dal cliente siano configurate per l'eliminazione e la non ripulitura.

Configurare l'eliminazione soft per l'account di archiviazione di Azure usato per l'acquisizione dei dati di Hub eventi. Si noti che questa funzionalità non è ancora supportata Azure Data Lake Storage generazione 2.

- [Come abilitare l'eliminazione soft in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurare un insieme di credenziali delle chiavi con le chiavi](configure-customer-managed-key.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](/azure/storage/blobs/soft-delete-blob-overview)

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

**Linee** guida: il Centro sicurezza assegna una gravità agli avvisi, per consentire di classificare in ordine di priorità l'ordine in cui si partecipa a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile ottenerla immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o nell'analisi usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee** guida: eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi in base a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee** guida: seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia e l'esecuzione di Microsoft red teaming e i test di penetrazione del sito in tempo reale rispetto all'infrastruttura cloud gestita da Microsoft, ai servizi e alle applicazioni. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
