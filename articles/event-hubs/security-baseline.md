---
title: Baseline della sicurezza di Azure per hub eventi
description: La linea di base di sicurezza di hub eventi fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57862a450d313f5d8850b14047ecc3d25d6ba5c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563685"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Baseline della sicurezza di Azure per hub eventi

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) a hub eventi. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a hub eventi. I **controlli** non applicabili a hub eventi sono stati esclusi.

 
Per informazioni sul modo in cui gli hub eventi sono completamente mappati al benchmark di sicurezza di Azure, vedere il [file di mapping Baseline della sicurezza di hub eventi](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: l'integrazione di hub eventi di Azure con gli endpoint di servizio di rete virtuale consente di accedere in modo sicuro alle funzionalità di messaggistica da carichi di lavoro, ad esempio macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Una volta associato ad almeno un endpoint di servizio della subnet di rete virtuale, il rispettivo spazio dei nomi di hub eventi non accetta più il traffico da qualsiasi posizione, ma le subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, il binding dello spazio dei nomi di hub eventi a un endpoint di servizio configura un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

È anche possibile creare un endpoint privato, che è un'interfaccia di rete che si connette privatamente e in modo sicuro al servizio Hub eventi usando il servizio di collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. 

È anche possibile proteggere lo spazio dei nomi di hub eventi di Azure usando i firewall. Hub eventi supporta i controlli di accesso basato su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Come usare gli endpoint del servizio rete virtuale con hub eventi di Azure](event-hubs-service-endpoints.md)

- [Integrare Hub eventi di Azure con Collegamento privato di Azure](private-link-service.md)

- [Come configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure](event-hubs-ip-filtering.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di hub eventi in Azure. Se si usano macchine virtuali di Azure per accedere a hub eventi, abilitare i log di flusso dei gruppi di sicurezza di rete e inviare i log a un account di archiviazione per il controllo del traffico.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate agli hub eventi per prevenire gli attacchi di tipo Denial of Service (DDoS) distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

- [Per altre informazioni sul centro sicurezza di Azure-Intelligence per le minacce integrata](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: se si usano macchine virtuali di Azure per accedere a hub eventi, abilitare i log di flusso dei gruppi di sicurezza di rete e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log di flusso dei gruppi di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: se si usano macchine virtuali di Azure per accedere a hub eventi, selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con le funzionalità di ispezione del payload. Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è necessario per l'organizzazione, è possibile usare la funzionalità firewall incorporata di hub eventi di Azure. È possibile limitare l'accesso allo spazio dei nomi di hub eventi per un intervallo limitato di indirizzi IP o un indirizzo IP specifico usando le regole del firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come aggiungere una regola del firewall in hub eventi per un indirizzo IP specifico](event-hubs-ip-filtering.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate agli spazi dei nomi di hub eventi di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi **Microsoft. EventHub** e **Microsoft. Network** per creare definizioni di criteri personalizzate per controllare o applicare la configurazione di rete degli spazi dei nomi di hub eventi. È anche possibile usare le definizioni di criteri predefinite correlate a hub eventi di Azure, ad esempio:

- Hub eventi deve usare un endpoint di servizio di rete virtuale.

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri predefiniti di Azure per lo spazio dei nomi di hub eventi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le reti virtuali e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati all'hub eventi.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate a hub eventi di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: all'interno di monitoraggio di Azure, configurare i log correlati a hub eventi nelle impostazioni di diagnostica del log attività e dell'hub eventi per inviare i log in un'area di lavoro di log Analytics per eseguire query o in un account di archiviazione per l'archiviazione a lungo termine dell'archivio.

- [Come configurare le impostazioni di diagnostica per hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Informazioni sul log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica per lo spazio dei nomi di hub eventi di Azure. Sono disponibili tre categorie di impostazioni di diagnostica per hub eventi di Azure: log di archivio, log operativi e log di ridimensionamento automatico. Consentire ai log operativi di acquisire informazioni su ciò che accade durante le operazioni di hub eventi, in particolare il tipo di operazione, tra cui la creazione dell'hub eventi, le risorse usate e lo stato dell'operazione.

Inoltre, è possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle a un account di archiviazione di Azure, a un hub eventi o a un'area di lavoro Log Analytics. I log attività forniscono informazioni approfondite sulle operazioni eseguite sull'hub eventi di Azure e altre risorse. Usando i log attività, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sugli spazi dei nomi di hub eventi di Azure.

- [Come abilitare le impostazioni di diagnostica per hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: all'interno di monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione per acquisire e verificare gli eventi imprevisti correlati all'hub eventi.

- [Come impostare i parametri di conservazione dei log per aree di lavoro Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati correlati all'hub eventi. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e delle informazioni di sistema di terze parti.

- [Per ulteriori informazioni sull'area di lavoro Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: all'interno di monitoraggio di Azure, configurare i log correlati all'hub eventi di Azure nel log attività e le impostazioni di diagnostica di hub eventi per inviare i log in un'area di lavoro di log Analytics per eseguire query o in un account di archiviazione per l'archiviazione a lungo termine dell'archivio. Utilizzare Log Analytics area di lavoro per creare avvisi per attività anomale rilevate negli eventi e nei registri di sicurezza.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure. 

- [Informazioni sul log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Come configurare le impostazioni di diagnostica per hub eventi di Azure](event-hubs-diagnostic-logs.md)

- [Come inviare un avviso sui dati del log dell'area di lavoro Log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'accesso del piano di controllo a hub eventi è controllato tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati a hub eventi è controllato tramite Azure AD con identità gestite o Registrazioni app, nonché con firme di accesso condiviso. Le firme di accesso condiviso vengono usate dai client che si connettono a hub eventi e possono essere rigenerate in qualsiasi momento.

- [Informazioni sulle firme di accesso condiviso per hub eventi](authenticate-shared-access-signature.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: Microsoft Azure offre la gestione integrata del controllo degli accessi per le risorse e le applicazioni basate su Azure Active Directory (Azure ad). Un vantaggio fondamentale dell'uso di Azure AD con hub eventi di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Il nome della risorsa per richiedere un token è https: \/ /Eventhubs.Azure.NET/. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse di hub eventi di Azure.

- [Come autenticare un'applicazione con Azure AD per accedere alle risorse di hub eventi](authenticate-application.md)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure per proteggere le risorse abilitate per hub eventi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per accedere e configurare le risorse abilitate per hub eventi.

- [Informazioni sulle workstation con accesso con privilegi](/security/compass/privileged-access-devices)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory (Azure AD) Privileged Identity Management per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso. Per la registrazione aggiuntiva, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/alerts/action-groups.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio hub eventi. Questo consente il controllo degli accessi in base al ruolo di Azure (RBAC) per le risorse amministrative riservate.

- [ Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizzare l'accesso alle risorse di hub eventi usando Azure AD](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

Materiale sussidiario **: Azure Active Directory**(Azure ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Inoltre, ruotare regolarmente le firme di accesso condiviso di hub eventi.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulle firme di accesso condiviso per hub eventi](authenticate-shared-access-signature.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini dei registri eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con qualsiasi soluzione di sistema di gestione degli eventi e informazioni di sistema di terze parti o con uno strumento di monitoraggio.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autorizzare l'accesso alle risorse di hub eventi usando Azure AD](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e rilevamento dei rischi all'interno Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse abilitate per hub eventi. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: attualmente non disponibile; Customer Lockbox non è ancora supportato per gli hub eventi.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag sulle risorse correlate all'hub eventi per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni separate, facoltativamente con i gruppi di gestione per lo sviluppo, il test e la produzione. Gli spazi dei nomi di hub eventi devono essere separati da una rete virtuale con gli endpoint di servizio abilitati e contrassegnati in modo appropriato.

È anche possibile proteggere lo spazio dei nomi di hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basato su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure](event-hubs-ip-filtering.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usano le macchine virtuali per accedere a hub eventi, usare le reti virtuali, gli endpoint di servizio, il firewall di hub eventi, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di exfiltration di dati.

Microsoft gestisce l'infrastruttura sottostante per hub eventi di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure](event-hubs-ip-filtering.md)

- [Informazioni sugli endpoint del servizio rete virtuale con hub eventi di Azure](event-hubs-service-endpoints.md)

- [Integrare Hub eventi di Azure con Collegamento privato di Azure](private-link-service.md)

- [Informazioni sui gruppi di sicurezza di rete e sui tag di servizio](../virtual-network/network-security-groups-overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per hub eventi di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: Hub eventi di Azure supporta l'uso di Azure Active Directory (Azure ad) per autorizzare le richieste alle risorse di hub eventi. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

- [Informazioni sui ruoli disponibili e RBAC di Azure per hub eventi di Azure](authorize-access-azure-active-directory.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: Hub eventi di Azure supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia dei dati inattivi di hub eventi di Azure.

- [Come configurare le chiavi gestite dal cliente per la crittografia di hub eventi di Azure](configure-customer-managed-key.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di hub eventi di Azure e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, inclusi gli spazi dei nomi di hub eventi di Azure, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

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

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli spazi dei nomi e delle risorse correlate di hub eventi di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Inoltre, usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

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

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le distribuzioni di hub eventi di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi **Microsoft. EventHub** per creare criteri personalizzati per il controllo o l'applicazione delle configurazioni. È anche possibile usare le definizioni di criteri predefinite per hub eventi di Azure, ad esempio:

- È consigliabile abilitare i log di diagnostica in Hub eventi

- Gli hub eventi devono usare un endpoint servizio di rete virtuale

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Criteri predefiniti di Azure per lo spazio dei nomi di hub eventi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse abilitate per hub eventi. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

 
- [Per altre informazioni sugli effetti dei criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare gli effetti criteri di Azure [audit], [deny] e [Distribuisci se non esistono] per applicare automaticamente le configurazioni per le distribuzioni di hub eventi di Azure e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere a hub eventi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle firme di accesso condiviso per le distribuzioni di hub eventi di Azure. Assicurarsi che Key Vault sia configurato con l'eliminazione temporanea abilitata.

- [Autenticare un'identità gestita con Azure Active Directory (Azure AD) per accedere alle risorse di hub eventi](./authenticate-managed-identity.md?tabs=latest)

- [Configurare le chiavi gestite dal cliente per gli hub eventi](configure-customer-managed-key.md)

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere a hub eventi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere Hub eventi di Azure. Assicurarsi che Key Vault sia configurato con l'eliminazione temporanea abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

- [Autenticare un'identità gestita con Azure AD per accedere alle risorse di hub eventi](./authenticate-managed-identity.md?tabs=latest)

- [Configurare le chiavi gestite dal cliente per gli hub eventi](configure-customer-managed-key.md)

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

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: pre-analizzare i contenuti caricati in risorse di Azure non di calcolo, ad esempio hub eventi di Azure, servizio App, data Lake storage, archiviazione BLOB, database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sui contenuti dei clienti.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: configurare il ripristino di emergenza geografico per hub eventi di Azure. In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate zone di disponibilità), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il ripristino di emergenza geografico e la replica geografica sono quindi funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

- [Informazioni sul ripristino di emergenza geografico per hub eventi di Azure](./event-hubs-geo-dr.md#availability-zones)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: Hub eventi di Azure fornisce la crittografia dei dati inattivi con Azure crittografia del servizio di archiviazione (Azure SSE). Hub eventi si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati usando le chiavi gestite da Microsoft. Se si usano Azure Key Vault per archiviare le chiavi gestite dal cliente, assicurarsi che i backup automatici delle chiavi siano regolari.

Verificare la regolarità dei backup automatici dei segreti di Key Vault con il comando di PowerShell seguente: Backup-AzKeyVaultSecret

- [Come configurare le chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure](configure-customer-managed-key.md)

- [Come eseguire il backup di Key Vault segreti](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino di test delle chiavi gestite dal cliente sottoposte a backup.

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Hub eventi di Azure richiede chiavi gestite dal cliente per l'eliminazione temporanea e non ripulisce la configurazione.

Configurare l'eliminazione temporanea per l'account di archiviazione di Azure usato per l'acquisizione dei dati di hub eventi. Si noti che questa funzionalità non è ancora supportata per Azure Data Lake Storage generazione 2.

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurare un insieme di credenziali delle chiavi con chiavi](configure-customer-managed-key.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

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

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

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

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
