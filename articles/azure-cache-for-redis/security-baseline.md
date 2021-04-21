---
title: Baseline di sicurezza di Azure per cache di Azure per Redis
description: La baseline cache di Azure per Redis sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0397f53c54f488f6840e35212de3e51624f360d7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829999"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Baseline di sicurezza di Azure per cache di Azure per Redis

Questa baseline di sicurezza applica le linee guida [di Azure Security Benchmark versione 1.0](../security/benchmarks/overview-v1.md) cache di Azure per Redis. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto è raggruppato in base ai **controlli di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a cache di Azure per Redis. **I** controlli non applicabili cache di Azure per Redis sono stati esclusi.

 
Per informazioni su come cache di Azure per Redis completamente mappato ad Azure Security Benchmark, vedere il file di mapping completo cache di Azure per Redis [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Indicazioni:** distribuire l'istanza cache di Azure per Redis all'interno di una rete virtuale. Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale.

È anche possibile specificare regole del firewall con un intervallo di indirizzi IP iniziale e finale. Quando le regole del firewall sono configurate, solo le connessioni client degli intervalli di indirizzi IP specificati possono connettersi alla cache.

- [Come configurare il supporto della rete virtuale per un'cache di Azure per Redis](cache-how-to-premium-vnet.md)

- [Come configurare le regole cache di Azure per Redis firewall](./cache-configure.md#firewall)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni:** quando le macchine virtuali vengono distribuite nella stessa rete virtuale dell'istanza di cache di Azure per Redis, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log dei flussi del gruppo di sicurezza di rete e inviare i log in un account Archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** la distribuzione di Rete virtuale di Azure offre sicurezza e isolamento migliorati per il cache di Azure per Redis, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Quando viene distribuito in una rete virtuale, cache di Azure per Redis non è indirizzabile pubblicamente e può essere accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale.

Abilitare Protezione DDoS Standard nelle reti virtuali associate alle istanze cache di Azure per Redis per proteggersi da attacchi DDoS (Distributed Denial of Service). Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare il supporto della rete virtuale per un cache di Azure per Redis](cache-how-to-premium-vnet.md)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

**Indicazioni:** quando le macchine virtuali vengono distribuite nella stessa rete virtuale dell'istanza di cache di Azure per Redis, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account Archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** quando si usa cache di Azure per Redis con le applicazioni Web in esecuzione in istanze di Servizio app di Azure o di calcolo, distribuire tutte le risorse all'interno di una rete virtuale di Azure e proteggersi con un Web application firewall di Azure (WAF) nel gateway applicazione Web. Configurare waf per l'esecuzione in "modalità di prevenzione". La modalità di prevenzione blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

In alternativa, è possibile selezionare un'offerta dal Azure Marketplace che supporta la funzionalità IDS/IPS con le funzionalità di ispezione del payload e/o rilevamento anomalie.

- [Informazioni sulle funzionalità di Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) o Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni (ASG) per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione, consentendo di raggruppare le macchine virtuali e definire i criteri di sicurezza di rete in base a tali gruppi.

- [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

- [Gruppi di sicurezza delle applicazioni](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate alle istanze cache di Azure per Redis con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.Cache" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze cache di Azure per Redis rete. È anche possibile usare definizioni di criteri incorporate, ad esempio:
- Devono essere abilitate solo connessioni sicure alla Cache Redis

- Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio i modelli di Azure Resource Manager, il controllo degli accessi in base al ruolo di Azure e i criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per le risorse di rete associate cache di Azure per Redis distribuzione per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze cache di Azure per Redis rete. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring (Azure Security Benchmark: registrazione e monitoraggio).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni dettagliate sulle operazioni eseguite nelle istanze cache di Azure per Redis a livello del piano di controllo. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di controllo per le istanze cache di Azure per Redis.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni dettagliate sulle operazioni eseguite nelle istanze cache di Azure per Redis a livello del piano di controllo. Usando i dati del log attività di Azure, è possibile determinare cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di controllo per le istanze cache di Azure per Redis dati.

Anche se le metriche sono disponibili abilitando Le impostazioni di diagnostica, la registrazione di controllo nel piano dati non è ancora disponibile per cache di Azure per Redis.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee** guida: Monitoraggio di Azure periodo di conservazione dei log per le aree di lavoro Log Analytics associate alle istanze cache di Azure per Redis in base alle normative di conformità dell'organizzazione.

Si noti che la registrazione di controllo nel piano dati non è ancora disponibile per cache di Azure per Redis.

- [Come impostare i parametri di conservazione dei log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare i modelli e fornire molte altre informazioni dettagliate in base ai dati del log attività che potrebbero essere stati raccolti per cache di Azure per Redis.

Si noti che la registrazione di controllo nel piano dati non è ancora disponibile per cache di Azure per Redis.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/essentials/activity-log.md)

- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in Monitoraggio di Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Linee** guida: è possibile configurare per ricevere avvisi in base alle metriche e ai log attività correlati alle istanze cache di Azure per Redis personalizzate. Monitoraggio di Azure consente di configurare un avviso per inviare una notifica tramite posta elettronica, chiamare un webhook o richiamare un'app per la logica di Azure.

Anche se le metriche sono disponibili abilitando le impostazioni di diagnostica, la registrazione di controllo nel piano dati non è ancora disponibile per cache di Azure per Redis.

- [Come configurare gli avvisi per cache di Azure per Redis](./cache-how-to-monitor.md#alerts)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** Azure Active Directory (Azure AD) ha ruoli predefiniti che devono essere assegnati in modo esplicito e sono disponibili per query. Usare il Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni:** l'accesso al piano di cache di Azure per Redis è controllato tramite Azure Active Directory (Azure AD). Azure AD non ha il concetto di password predefinite.

L'accesso al piano dati cache di Azure per Redis è controllato tramite chiavi di accesso. Queste chiavi vengono usate dai client che si connettono alla cache e possono essere rigenerate in qualsiasi momento.

Non è consigliabile compilare password predefinite nell'applicazione. È invece possibile archiviare le password in Azure Key Vault e quindi usare Azure AD per recuperarle.

- [Come rigenerare le chiavi cache di Azure per Redis di accesso](./cache-configure.md#settings)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Per altre informazioni, vedere i riferimenti seguenti:

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** cache di Azure per Redis le chiavi di accesso per autenticare gli utenti e non supporta l'accesso Single Sign-On (SSO) a livello di piano dati. L'accesso al piano di controllo per cache di Azure per Redis è disponibile tramite l'API REST e supporta l'accesso SSO. Per eseguire l'autenticazione, impostare l'intestazione Authorization per le richieste su un token JSON Web che si ottiene da Azure Active Directory (Azure AD).

- [Informazioni cache di Azure per Redis'API REST](/rest/api/redis/)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato sull'accesso

**Linee** guida: abilitare Azure Active Directory(Azure AD) a più fattori e seguire Centro sicurezza di Azure indicazioni su Gestione identità e accessi.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni:** usare workstation di accesso con privilegi (PAW) con autenticazione a più fattori configurata per accedere e configurare le risorse di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare le attività sospette da account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** configurare le località denominate nell'accesso condizionale Azure Active Directory (Azure AD) per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee** guida: usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Azure AD'autenticazione non può essere usata per l'accesso diretto al piano dati di cache di Azure per Redis, tuttavia, le credenziali Azure AD possono essere usate per l'amministrazione a livello di piano di controllo (ad esempio portale di Azure) per controllare cache di Azure per Redis chiavi di accesso.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non obsoleti. Usare anche Verifiche di accesso alle identità di Azure per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni sulla creazione Azure AD report](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** si ha accesso alle origini dei log eventi di accesso di Azure Active Directory (Azure AD), di controllo e di rischio, che consentono di eseguire l'integrazione con Azure Sentinel o un SISTEMAEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente Azure AD e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. È possibile configurare gli avvisi di log desiderati in Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** per la deviazione del comportamento di accesso all'account nel piano di controllo, usare le funzionalità di Azure Active Directory (Azure AD) Identity Protection e di rilevamento dei rischi per configurare risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. cache di Azure per Redis istanze devono essere separate da rete virtuale/subnet e contrassegnate in modo appropriato. Facoltativamente, usare il firewall cache di Azure per Redis per definire regole in modo che solo le connessioni client da intervalli di indirizzi IP specificati possano connettersi alla cache.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come distribuire cache di Azure per Redis in una rete virtuale](cache-how-to-premium-vnet.md)

- [Come configurare le regole cache di Azure per Redis firewall](./cache-configure.md#firewall)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni:** non ancora disponibile; Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per cache di Azure per Redis.

Microsoft gestisce l'infrastruttura sottostante per cache di Azure per Redis e ha implementato controlli rigorosi per impedire la perdita o l'esposizione dei dati dei clienti.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni:** cache di Azure per Redis richiede comunicazioni con crittografia TLS per impostazione predefinita. Le versioni TLS 1.0, 1.1 e 1.2 sono attualmente supportate. Tuttavia, TLS 1.0 e 1.1 sono in un percorso di deprecazione a livello di settore, quindi usare TLS 1.2, se possibile. Se la libreria client o lo strumento non supporta TLS, l'abilitazione delle connessioni non crittografate può essere eseguita tramite le API di portale di Azure o di gestione. In questi casi in cui le connessioni crittografate non sono possibili, è consigliabile inserire la cache e l'applicazione client in una rete virtuale.

- [Informazioni sulla crittografia in transito per cache di Azure per Redis](cache-best-practices.md)

- [Informazioni sulle porte necessarie usate negli scenari di cache della rete virtuale](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.Cache**:

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per cache di Azure per Redis. Contrassegnare le istanze contenenti informazioni riservate come tali e implementare soluzioni di terze parti, se necessario per motivi di conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni:** usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso al piano di controllo cache di Azure per Redis (ad esempio, portale di Azure).

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee** guida: cache di Azure per Redis i dati dei clienti in memoria e, sebbene fortemente protetti da molti controlli implementati da Microsoft, la memoria non è crittografata per impostazione predefinita. Se richiesto dall'organizzazione, crittografare il contenuto prima di archiviare cache di Azure per Redis.

Se si usa cache di Azure per Redis funzionalità "Persistenza dati Redis", i dati vengono inviati a un account Archiviazione di Azure di cui si è proprietari e che si gestisce. È possibile configurare la persistenza dal pannello "Cache di Azure per Redis" durante la creazione della cache e dal menu Risorsa per le cache Premium esistenti.

I dati in Archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più potenti disponibili ed è conforme a FIPS 140-2. Archiviazione di Azure la crittografia non può essere disabilitata. È possibile usare chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con le proprie chiavi.

- [Come configurare la persistenza in cache di Azure per Redis](cache-how-to-premium-persistence.md)

- [Informazioni sulla crittografia per Archiviazione di Azure account](../storage/common/storage-service-encryption.md)

- [Informazioni sulla protezione dei dati dei clienti di Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare Monitoraggio di Azure log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle istanze di produzione di cache di Azure per Redis e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni:** seguire le raccomandazioni Centro sicurezza di Azure sulla protezione delle istanze cache di Azure per Redis e delle risorse correlate.

Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano cache di Azure per Redis.

- [Informazioni Centro sicurezza di Azure raccomandazioni](../security-center/recommendations-reference.md)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee** guida: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia cache di Azure per Redis istanze e risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefiniti seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag delle risorse](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefiniti seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

È anche possibile usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

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

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee** guida: definire e implementare configurazioni di sicurezza standard per le istanze cache di Azure per Redis con Criteri di Azure. Usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze cache di Azure per Redis personalizzate. È anche possibile usare definizioni di criteri predefinite correlate alle istanze cache di Azure per Redis, ad esempio:

- Devono essere abilitate solo connessioni sicure alla Cache Redis

Per altre informazioni, vedere i riferimenti seguenti:

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni Criteri di Azure personalizzate o modelli Azure Resource Manager per le istanze di cache di Azure per Redis e le risorse correlate, usare Azure Repos per archiviare e gestire in modo sicuro il codice.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per avvisare, controllare e applicare configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per avvisare, controllare e applicare configurazioni di sistema. Usare Criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le istanze cache di Azure per Redis e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione in Servizio app di Azure usate per accedere alle istanze di cache di Azure per Redis, usare l'identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti cache di Azure per Redis. Assicurarsi che Key Vault'eliminazione soft sia abilitata.

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare un Key Vault](../key-vault/general/quick-create-portal.md)

- [Come eseguire l'autenticazione Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione in Servizio app di Azure usate per accedere alle istanze di cache di Azure per Redis, usare l'identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti cache di Azure per Redis. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporta l Azure AD Azure Key Vault, senza credenziali nel codice.

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Come eseguire l'integrazione con identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sul contenuto del cliente.

Eseguire l'analisi preliminare del contenuto da caricare in risorse di Azure non di calcolo, ad esempio Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in questi casi.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Linee guida:** abilitare la persistenza Redis. La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

È anche possibile usare cache di Azure per Redis esporta. L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella macchina virtuale che ospita l'istanza del server cache di Azure per Redis e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

- [Come abilitare la persistenza Redis](cache-how-to-premium-persistence.md)

- [Come usare l'cache di Azure per Redis esportazione](cache-how-to-import-export-data.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida:** abilitare la persistenza Redis. La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

È anche possibile usare cache di Azure per Redis esporta. L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella macchina virtuale che ospita l'istanza del server cache di Azure per Redis e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

Se si Azure Key Vault per archiviare le credenziali per le istanze cache di Azure per Redis, assicurarsi di eseguire backup automatizzati regolari delle chiavi.

- [Come abilitare la persistenza Redis](cache-how-to-premium-persistence.md)

- [Come usare l'cache di Azure per Redis esportazione](cache-how-to-import-export-data.md)

- [Come eseguire il backup Key Vault chiavi](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** usare cache di Azure per Redis Importa. L'importazione può essere usata per importare file RDB compatibili con Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, incluso Redis in esecuzione in Linux, Windows o qualsiasi provider di servizi cloud, ad esempio Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

Testare periodicamente il ripristino dei dati Azure Key Vault segreti.

- [Come usare l'cache di Azure per Redis importazione](cache-how-to-import-export-data.md)

- [Come ripristinare i Key Vault segreti](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può anche sfruttare la Guida alla gestione degli eventi imprevisti per la sicurezza dei computer di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sul livello di attendibilità del Centro sicurezza nella ricerca o nell'analisi usata per l'emissione dell'avviso, nonché sul livello di attendibilità che si è verificato un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

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

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
