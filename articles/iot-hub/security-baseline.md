---
title: Baseline di sicurezza di Azure per hub IoT di Azure
description: La hub IoT di Azure di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587630"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Baseline di sicurezza di Azure per hub IoT di Azure

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) Microsoft Azure Hub IoT. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a hub IoT di Azure. **I** controlli non applicabili hub IoT di Azure sono stati esclusi.

 
Per informazioni sul hub IoT di Azure completamente mappato a Azure Security Benchmark, vedere il file di mapping completo hub IoT di Azure [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Linee** guida: l'hub IoT è una piattaforma multi-tenant Platform-as-a-Service (PaaS), clienti diversi condividono lo stesso pool di risorse hardware di calcolo, rete e archiviazione. I nomi host dell'hub IoT vengono mappati a un endpoint pubblico con un indirizzo IP instradabile pubblicamente tramite Internet. Diversi clienti possono condividere questo endpoint pubblico dell'hub IoT e consentire l'accesso a tutti i dispositivi IoT su reti WAN (Wide Area Network) e su reti locali. Microsoft ha progettato il servizio per l'isolamento completo tra i dati di ogni tenant e funziona continuamente per garantire questo risultato.

Le funzionalità dell'hub IoT, tra cui routing dei messaggi, caricamento dei file e importazione/esportazione in blocco dei dispositivi, richiedono anche la connettività dall'hub IoT a una risorsa di Azure di proprietà del cliente tramite il rispettivo endpoint pubblico. Questi percorsi di connettività generano collettivamente il traffico in uscita dall'hub IoT alle risorse dei clienti.

È consigliabile limitare la connettività alle risorse di Azure (incluso hub IoT di Azure) tramite una rete virtuale di cui si è proprietari e che si opera per ridurre l'esposizione alla connettività in una rete isolata e abilitare la connettività di rete locale direttamente alla rete backbone di Azure. Usare collegamento privato di Azure endpoint privato di Azure, se possibile, per abilitare l'accesso privato ai servizi da altre reti virtuali. 

Dopo aver stabilito l'accesso privato, disabilitare l'accesso alla rete pubblica per l'hub IoT per una sicurezza aggiuntiva. Questo controllo a livello di rete viene applicato a una risorsa hub IoT specifica, garantendo l'isolamento. Per mantenere attivo il servizio per altre risorse del cliente usando il percorso pubblico, l'endpoint pubblico rimane risolvibile, gli indirizzi IP individuabili e le porte rimangono aperte. Questo non è un motivo di preoccupazione perché Microsoft integra più livelli di sicurezza per garantire l'isolamento completo tra i tenant.

Mantenere le porte hardware aperte nei dispositivi al minimo per evitare accessi indesiderati. È anche possibile creare meccanismi per impedire o rilevare manomissioni fisiche del dispositivo.

- [Supporto delle reti virtuali IoT](virtual-network-support.md)

- [Gestire l'accesso alla rete pubblica per l'hub IoT](iot-hub-public-network-access.md)

- [Isolamento dei tenant in Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [Procedura consigliata per la rete loT](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [collegamento privato di Azure panoramica](../private-link/private-link-overview.md)

- [Gruppo di sicurezza di rete di Azure](../virtual-network/network-security-groups-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare le Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione della rete per proteggere le risorse di rete di Azure. Abilitare i log dei flussi dei gruppi di sicurezza di rete e inviare i log a un account Archiviazione di Azure per il controllo. È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure. Alcuni vantaggi delle Analisi del traffico sono la possibilità di visualizzare l'attività di rete, identificare aree sensibili e minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare errori di configurazione della rete.
 
- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)
 
- [Informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee** guida: Bloccare indirizzi IP dannosi noti con regole di filtro IP dell'hub IoT. Anche i tentativi dannosi vengono registrati e avvisati tramite Centro sicurezza di Azure per IoT.

Protezione DDoS di Azure Basic è già abilitato e disponibile senza costi aggiuntivi come parte dell'hub IoT. Il monitoraggio del traffico always-on e la mitigazione in tempo reale degli attacchi comuni a livello di rete offrono le stesse difese utilizzate dai servizi Servizi online Microsoft. L'intera scala della rete globale di Azure può essere usata per distribuire e mitigare il traffico di attacco tra le aree.

- [Filtro IP dell'hub IoT](iot-hub-ip-filtering.md)

- [Centro sicurezza di Azure per la comunicazione di indirizzi IP sospetti IoT](/azure/asc-for-iot/concept-security-alerts)

- [Gestire Protezione DDoS di Azure Basic](../ddos-protection/ddos-protection-overview.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Linee guida:** non applicabile; questa raccomandazione è destinata alle offerte che producono pacchetti di rete che possono essere registrati e visualizzati dai clienti. L'hub IoT non produce pacchetti di rete destinati ai clienti e non è progettato per la distribuzione diretta nelle reti virtuali di Azure.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** selezionare un'offerta da Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload.  Quando l'ispezione del payload non è un requisito, Firewall di Azure l'intelligence sulle minacce. Firewall di Azure filtro basato sull'intelligence sulle minacce viene usato per avvisare e/o bloccare il traffico da e verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall preferita in ogni limite di rete dell'organizzazione per rilevare e/o bloccare il traffico dannoso. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire i Firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con Firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere al hub IoT di Azure, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio ,ad esempio AzureIoTHub, nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Come usare i tag di servizio per Azure IoT](iot-hub-understand-ip-address.md)
- [Per altre informazioni sull'uso dei tag di servizio](../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate hub IoT di Azure spazi dei nomi con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.Devices" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi Machine Learning rete. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per le risorse di rete associate hub IoT di Azure distribuzione per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate hub IoT di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire i log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati hub IoT di Azure. In Monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione per l'archiviazione a lungo termine/archiviazione. In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel eventi imprevisti e gestione eventi di sicurezza (SIEM) di terze parti.

- [Configurare i Azure IoT di lavoro](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee** guida: abilitare Azure IoT di diagnostica nelle risorse di Azure per l'accesso ai log di controllo, sicurezza e risorse. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Configurare i log hub IoT di Azure dati](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Informazioni sulla registrazione e i diversi tipi di log in Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Devices**:

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** in Monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro Log Analytics associate alle istanze hub IoT di Azure in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee** guida: analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati dell'hub IoT di Azure. Usare Monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e eseguire l'on board dei dati per Azure Sentinel o un SIEM di terze parti. 

- [Monitorare l Azure IoT integrità](monitor-iot-hub.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Introduzione alle query di Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** usare Centro sicurezza di Azure ioT con un'area di lavoro Log Analytics per il monitoraggio e la creazione di avvisi per le attività anomae presenti nei log di sicurezza e negli eventi. In alternativa, è possibile abilitare e eseguire l'on board dei dati Azure Sentinel. È anche possibile definire avvisi operativi con Monitoraggio di Azure che possono avere implicazioni sulla sicurezza, ad esempio quando il traffico scende in modo imprevisto.

- [Monitorare hub IoT di Azure integrità](monitor-iot-hub.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Centro sicurezza di Azure per gli avvisi IoT](/azure/asc-for-iot/concept-security-alerts)

- [Come creare un avviso per i dati di log di Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni:** non applicabile; hub IoT di Azure non elabora né produce log correlati all'antimalware.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni:** non applicabile; hub IoT di Azure non elabora né produce log correlati a DNS.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso Azure IoT hub tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, entità servizio dei gruppi e identità gestite. Esistono ruoli predefiniti predefiniti per determinate risorse e questi ruoli possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure o Azure PowerShell o l'portale di Azure.

- [Come ottenere un ruolo di directory in Azure Active Directory (Azure AD) con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee** guida: la gestione degli accessi hub IoT di Azure risorse è controllata tramite Azure Active Directory (Azure AD). Azure AD non ha il concetto di password predefinite.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati.

È anche possibile abilitare l'accesso JUST-In-Time agli account amministrativi usando Azure Active Directory (Azure AD) Privileged Identity Management e Azure Resource Manager.

- [Altre informazioni sulle Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee** guida: per gli utenti che accedono all'hub IoT, usare Azure Active Directory (Azure AD) SSO. Usare le Centro sicurezza di Azure per l'identità e l'accesso.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee** guida: abilitare Azure Active Directory (Azure AD) a più fattori per proteggere il tenant di Azure complessivo, a vantaggio di tutti i servizi. Il servizio hub IoT non dispone del supporto dell'autenticazione a più fattori.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare una workstation PAW (Secure Privileged Access Workstation) per le attività amministrative che richiedono privilegi elevati.

- [Informazioni sulle workstation con accesso sicuro e con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare in caso di attività sospette da account amministrativi

**Indicazioni:** usare i report Azure Active Directory (Azure AD) e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gestire le risorse di Azure solo da località approvate

**Indicazioni:** per gli utenti che accedono all'hub IoT, l'accesso condizionale non è supportato. Per attenuare questo problema, usare Azure Active Directory (Azure AD) denominate per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche per il tenant di Azure complessivo, a vantaggio di tutti i servizi, incluso l'hub IoT.

- [Come configurare Azure AD posizioni denominate](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** per l'accesso utente all'hub IoT, usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Per l'accesso a dispositivi e servizi, l'hub IoT usa token di sicurezza e token di firma di accesso condiviso (SAS) per autenticare dispositivi e servizi per evitare l'invio di chiavi in rete. 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Token di sicurezza dell'hub IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce i log per individuare gli account non obsoleti. È anche possibile usare le Azure AD di identità e di accesso per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere verificato regolarmente per assicurarsi che solo gli utenti autorizzati possano continuare ad accedere.

Usare Azure AD Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

- [Informazioni sulla creazione Azure AD report](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuire Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorare i tentativi di accesso alle credenziali disattivate

**Indicazioni:** è possibile accedere alle origini del log eventi di Azure Active Directory (Azure AD) di accesso, controllo e rischio, che consentono di eseguire l'integrazione con qualsiasi strumento SIEM/monitoraggio.

È possibile semplificare questo processo creando impostazioni di diagnostica Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

L Monitoraggio di Azure log delle risorse per monitorare i tentativi di connessione non autorizzati nella categoria Connessioni.

- [Come integrare i log attività di Azure con Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Configurare i log delle risorse per l'hub IoT](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee** guida: usare Azure Active Directory (Azure AD) identity protection per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [ Come visualizzare Azure AD accesso rischioso](../active-directory/identity-protection/overview-identity-protection.md)

- [ Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ Come eseguire l'onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni:** negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, questi verranno richiesti direttamente dal cliente.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.
 
- [ Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** implementare l'isolamento usando sottoscrizioni e gruppi di gestione separati per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di riservatezza dei dati. È possibile limitare il livello di accesso alle risorse di Azure richiesto dalle applicazioni e dall'ambiente aziendale. È possibile controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure.
  
- [ Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [ Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [ Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** usare una soluzione di terze parti di Azure Marketplace nei perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti, inviando avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** l'hub IoT usa Transport Layer Security (TLS) per proteggere le connessioni da dispositivi e servizi IoT. Attualmente sono supportate tre versioni del protocollo TLS, ovvero le versioni 1.0, 1.1 e 1.2. È consigliabile usare TLS 1.2 come versione preferita di TLS durante la connessione all'hub IoT.

Seguire Centro sicurezza di Azure per la crittografia dei dati in stato di inalter e la crittografia in transito, se applicabile.

- [Supporto TLS nell'hub IoT](iot-hub-tls-support.md)
- [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per hub IoT di Azure. Implementare una soluzione di terze parti, se necessario per motivi di conformità.

Per la piattaforma Azure sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e va a grandi lunghezze per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Indicazioni:** per l'accesso utente del piano di controllo all'hub IoT, usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso. Per l'accesso del piano dati all'hub IoT, usare i criteri di accesso condiviso per l'hub IoT.

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

- [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di hub IoT di Azure e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuire una soluzione di gestione automatica delle patch per titoli software di terze parti

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare la soluzione di individuazione automatica degli asset

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee** guida: applicare tag alle risorse di Azure (non tutte le risorse supportano i tag, ma la maggior parte lo fanno) per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.
  
- [ Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni:** creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

Ogni hub IoT ha un registro delle identità che può essere usato per creare risorse per dispositivo nel servizio. È possibile aggiungere singoli o gruppi di identità dei dispositivi a un elenco elementi consentiti o a un elenco di elementi non consentiti, consentendo il controllo completo sull'accesso al dispositivo.

- [Registro delle identità dell'hub IoT](iot-hub-devguide-identity-registry.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. 

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che possono essere create nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

* Tipi di risorse non consentiti
* Tipi di risorse consentiti

Inoltre, usare il Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee** guida: usare l'accesso condizionale Azure Active Directory (Azure AD) per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca l'accesso" per l'app "Microsoft Azure Management".

- [ Come configurare l'accesso condizionale per bloccare l'accesso Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard per il servizio Hub Iot di Azure con Criteri di Azure. Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Devices" per creare criteri personalizzati per controllare o applicare la configurazione hub IoT di Azure servizi.

Azure Resource Manager la possibilità di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni di Centro sicurezza di Azure come baseline di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di singole e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure. È anche possibile usare i modelli Azure Resource Manager per mantenere la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione.  
 
- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)
- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager dei modelli](../azure-resource-manager/templates/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni di Criteri di Azure personalizzate per il hub IoT di Azure o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Devices" per creare criteri personalizzati per avvisare, controllare e applicare configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure. È anche possibile usare Criteri di Azure avvisi e controllare le configurazioni delle risorse di Azure. 
 
- [ Come correggere le raccomandazioni in Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità:** non applicabile

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** l'hub IoT usa token di sicurezza e token di firma di accesso condiviso (SAS) per autenticare dispositivi e servizi per evitare l'invio di chiavi in rete. 

Usare le identità gestite in combinazione con Azure Key Vault per semplificare la gestione dei segreti per le applicazioni cloud.

- [Token di sicurezza dell'hub IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Come usare le identità gestite per l'hub IoT](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Come creare un insieme di credenziali delle chiavi](../key-vault/general/quick-create-portal.md)

- [Come fornire l'Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** l'hub IoT usa token di sicurezza e token di firma di accesso condiviso (SAS) per autenticare dispositivi e servizi per evitare l'invio di chiavi in rete.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Key Vault, senza credenziali nel codice.

- [Token di sicurezza dell'hub IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Come configurare le identità gestite per l'hub IoT](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 
 
- [  Come configurare lo scanner delle credenziali](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** Microsoft Anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, hub IoT di Azure), ma non viene eseguito sul contenuto del cliente.

È responsabilità dell'utente eseguire la pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware del contenuto del cliente per conto dell'utente.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Ripristino dei dati.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni:** hub IoT di Azure fornisce metodi e framework per rendere i servizi dell'hub IoT a disponibilità elevata e ripristinabili in caso di emergenza in base a obiettivi aziendali specifici. 

- [Disponibilità elevata e ripristino di emergenza dell'hub IoT](iot-hub-ha-dr.md)

- [Come clonare l'hub IoT](iot-hub-how-to-clone.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** hub IoT di Azure consiglia che l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è utile in questo contesto.

- [Disponibilità elevata e ripristino di emergenza dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Esportazione dell'identità del dispositivo dell'hub IoT](iot-hub-bulk-identity-mgmt.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** hub IoT di Azure consiglia che l'hub IoT secondario deve contenere tutte le identità dei dispositivi che possono connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è utile in questo contesto.

Eseguire periodicamente il ripristino dei dati del contenuto nel backup. Assicurarsi di poter ripristinare le chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Disponibilità elevata e ripristino di emergenza dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Esportazione dell'identità del dispositivo dell'hub IoT](iot-hub-bulk-identity-mgmt.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** abilitare l'eliminazione e la protezione dall'eliminazione Key Vault proteggere le chiavi da eliminazioni accidentali o dannose. Se Archiviazione di Azure viene usato per archiviare i backup, abilitare l'eliminazione soft per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot blob.

 
- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Eliminazione soft per l'archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Linee guida:** sviluppare una guida alla risposta agli eventi imprevisti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli eventi imprevisti scritti che definiscono tutti i ruoli del personale, nonché le fasi di gestione e gestione degli eventi imprevisti, dal rilevamento alla revisione post-evento imprevisto. 
  
- [ Linee guida per la creazione di un proprio processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Center'anatomia di un evento imprevisto](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Usare la Guida alla gestione degli eventi imprevisti per la sicurezza del computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee** guida: Centro sicurezza di Azure una gravità a ogni avviso per definire la priorità degli avvisi da ricercare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'uso analitico per l'emissione dell'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso.

  
 Contrassegnare inoltre le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili. È responsabilità dell'utente assegnare la priorità alla correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.
  
- [ Avvisi di sicurezza in Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)
  
- [ Usare i tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee** guida: eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi in base a una cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e i gap e quindi rivedere il piano di risposta in base alle esigenze.
  
- [ Pubblicazione di NIST: Guida ai programmi di test, training ed esercizio per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.
  
- [ Come impostare il contatto di Centro sicurezza di Azure sicurezza](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni:** esportare gli avvisi Centro sicurezza di Azure e le raccomandazioni usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore Centro sicurezza di Azure dati per trasmettere gli avvisi a Azure Sentinel.
  
- [ Come configurare l'esportazione continua](../security-center/continuous-export.md)
 
- [ Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità di automazione del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi di sicurezza e alle raccomandazioni per proteggere le risorse di Azure.
  
- [ Come configurare l'automazione del flusso di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
