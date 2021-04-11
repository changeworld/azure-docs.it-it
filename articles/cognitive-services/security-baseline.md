---
title: Baseline della sicurezza di Azure per servizi cognitivi
description: La linea di base di sicurezza di servizi cognitivi fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a7f9570a68102eb20c84942ddf9ac73de033f01c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076599"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Baseline della sicurezza di Azure per servizi cognitivi

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview-v1.md) ai servizi cognitivi. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ai servizi cognitivi. I **controlli** non applicabili ai servizi cognitivi, o per i quali la responsabilità è Microsoft, sono stati esclusi.

Per informazioni sul modo in cui i servizi cognitivi sono completamente mappati al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza dei servizi cognitivi](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Indicazioni**: Microsoft Azure servizi cognitivi fornisce un modello di sicurezza a più livelli. Questo modello consente di proteggere gli account di Servizi cognitivi in un subset specifico di reti. Quando le regole di rete sono configurate, solo le applicazioni che richiedono dati sul set di reti specificato possono accedere all'account. È possibile limitare l'accesso alle risorse con il filtro delle richieste, consentendo solo le richieste che hanno origine da indirizzi IP, intervalli IP o da un elenco di subnet in reti virtuali di Azure specificati.

Il supporto per la rete virtuale e l'endpoint di servizio per servizi cognitivi è limitato a un set specifico di aree.

- [Come configurare le reti virtuali dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Panoramica delle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore di servizi cognitivi, è possibile usare i gruppi di sicurezza di rete per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso dei gruppi di sicurezza di rete e inviare log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso dei gruppi di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro. 

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

- [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore di servizi cognitivi, definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. CognitiveServices" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete della cache di Azure per le istanze di Redis. È anche possibile usare le definizioni di criteri predefinite, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

USA i progetti di Azure per semplificare le distribuzioni su larga scala di Azure con la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e i criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web front-end che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

- [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore di servizi cognitivi, è possibile usare i gruppi di sicurezza di rete per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso dei gruppi di sicurezza di rete e inviare log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso dei gruppi di sicurezza di rete a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro.  È possibile selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con la possibilità di disabilitare l'ispezione del payload.

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

- [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro. 

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

- [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi.

- [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

- [Gruppi di sicurezza delle applicazioni](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate al contenitore di servizi cognitivi con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. CognitiveServices" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete della cache di Azure per le istanze di Redis.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, il controllo degli accessi in base al ruolo (RBAC di Azure) e i criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per le risorse di rete associate al contenitore di servizi cognitivi per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al contenitore di servizi cognitivi. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni approfondite sulle operazioni eseguite sul contenitore di servizi cognitivi a livello di piano di controllo. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per la cache di Azure per le istanze di Redis.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Inoltre, servizi cognitivi invia eventi di diagnostica che possono essere raccolti e usati a scopo di analisi, avvisi e report. È possibile configurare le impostazioni di diagnostica per un contenitore di servizi cognitivi tramite il portale di Azure. È possibile inviare uno o più eventi di diagnostica a un account di archiviazione, a un hub eventi o a un'area di lavoro Log Analytics.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Uso delle impostazioni di diagnostica per servizi cognitivi di Azure](diagnostic-logging.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: In monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics. Questi log forniscono dati avanzati e frequenti sul funzionamento di una risorsa che vengono usati per l'identificazione e il debug dei problemi. È possibile eseguire query Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività raccolti per servizi cognitivi di Azure.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/activity-log)

- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](/azure/azure-monitor/platform/activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: è possibile generare avvisi sulle metriche supportate in Servizi cognitivi passando alla sezione avvisi e metriche in monitoraggio di Azure.

Configurare le impostazioni di diagnostica per il contenitore di servizi cognitivi e inviare i log a un'area di lavoro Log Analytics. All'interno dell'area di lavoro di Log Analytics, configurare gli avvisi in modo che vengano visualizzati quando viene soddisfatto un set predefinito di condizioni. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-log)

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

**Linee guida**: l'accesso del piano di controllo ai servizi cognitivi viene controllato tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati ai servizi cognitivi viene controllato tramite chiavi di accesso. Queste chiavi vengono usate dai client che si connettono alla cache e possono essere rigenerate in qualsiasi momento.

Non è consigliabile creare password predefinite nell'applicazione. È invece possibile archiviare le password in Azure Key Vault e quindi usare Azure AD per recuperarle.

- [Come rigenerare la cache di Azure per le chiavi di accesso Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza o i criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: Servizi cognitivi usa le chiavi di accesso per autenticare gli utenti e non supporta Single Sign-on (SSO) a livello di piano dati. L'accesso al piano di controllo per servizi cognitivi è disponibile tramite l'API REST e supporta SSO. Per eseguire l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token Web JSON (JavaScript Object Notation) ottenuto da Azure Active Directory (Azure AD).

- [Informazioni sull'API REST dei servizi cognitivi di Azure](/rest/api/cognitiveservices/)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni relative alla gestione delle identità e degli accessi nel centro sicurezza.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure. 

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: configurare i percorsi denominati nell'accesso condizionale Azure Active Directory (Azure ad) per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi e aree.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito, oltre a Salt, hash e archivia in modo sicuro le credenziali utente. Se il caso d'uso supporta l'autenticazione di Active Directory, usare Azure AD per autenticare le richieste all'API servizi cognitivi. 

Attualmente, solo il API Visione artificiale, API Viso, API Analisi del testo, il lettore immersivo, il riconoscitore del modulo, il rilevatore di anomalie e tutti i servizi Bing tranne Ricerca personalizzata Bing supportano l'autenticazione con Azure AD.

- [Come autenticare le richieste ai servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Inoltre, il cliente può usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti attivi abbiano accesso continuo.

Il cliente deve mantenere l'inventario degli account utente di gestione API, risolvendo l'accesso in base alle esigenze. In Gestione API gli sviluppatori sono gli utenti delle API esposte con Gestione API. Per impostazione predefinita, i nuovi account sviluppatore creati sono attivi e associati al gruppo Sviluppatori. Gli account sviluppatore con stato attivo possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni.

- [Come gestire gli account utente in Gestione API di Azure](../api-management/api-management-howto-create-or-invite-developers.md)

- [Come ottenere un elenco di utenti di Gestione API](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini del registro eventi di attività di accesso Azure Active Directory (Azure ad), controllo e rischio, che consentono di eseguire l'integrazione con Azure Sentinel o con Siem di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: per la deviazione del comportamento dell'account di accesso nel piano di controllo, usare le funzionalità di protezione delle identità Azure Active Directory (Azure ad) e di rilevamento dei rischi per configurare le risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non disponibile per servizi cognitivi. Customer Lockbox non è ancora supportato per servizi cognitivi.

- [Elenco dei servizi Customer Lockbox supportati](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da reti virtuali o subnet, contrassegnate in modo appropriato e protette da un gruppo di sicurezza di rete o da un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare avvisi o avvisi e negare con il firewall di Azure](../firewall/threat-intel.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: non ancora disponibili. Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per servizi cognitivi.

Microsoft gestisce l'infrastruttura sottostante per servizi cognitivi e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: tutti gli endpoint di servizi cognitivi vengono esposti tramite HTTP applica TLS 1,2. Con un protocollo di sicurezza applicato, i consumer che tentano di chiamare un endpoint di servizi cognitivi devono rispettare queste linee guida:

- Il sistema operativo client deve supportare TLS 1,2.
- Il linguaggio e la piattaforma usati per eseguire la chiamata HTTP devono specificare TLS 1,2 come parte della richiesta. A seconda del linguaggio e della piattaforma, la specifica di TLS viene eseguita in modo implicito o esplicito.

- [Informazioni su Transport Layer Security per servizi cognitivi di Azure](cognitive-services-security.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per servizi cognitivi. Istanze di tag che contengono informazioni riservate come tali e implementano una soluzione di terze parti, se necessario ai fini della conformità.

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di controllo dei servizi cognitivi, ad esempio portale di Azure. 

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni**: la crittografia dei servizi cognitivi per i servizi cognitivi dipende dal servizio specifico usato. Nella maggior parte dei casi, i dati vengono crittografati e decrittografati usando la crittografia AES a 256 bit conforme a FIPS 140-2. La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per i clienti da Microsoft. I dati dei clienti sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

È inoltre possibile utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi.

- [Elenco di servizi che crittografano le informazioni inattive](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di servizi cognitivi e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query o individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

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

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia della cache di Azure per le istanze di redis e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare inoltre Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

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

**Linee guida**: definire e implementare configurazioni di sicurezza standard per il contenitore di servizi cognitivi con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. CognitiveServices" per creare criteri personalizzati per controllare o applicare la configurazione della cache di Azure per le istanze di Redis.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate o modelli Azure Resource Manager per i contenitori di servizi cognitivi e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. cache" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. CognitiveServices" per creare definizioni di criteri di Azure personalizzate per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e gli effetti [Distribuisci se non esistono] per applicare automaticamente le configurazioni per la cache di Azure per le istanze di redis e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere all'API servizi cognitivi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle chiavi di servizi cognitivi. Verificare Key Vault l'eliminazione temporanea sia abilitata.

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Key Vault](/azure/key-vault/quick-create-portal)

- [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere all'API servizi cognitivi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle chiavi di servizi cognitivi. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

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

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sui contenuti dei clienti.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB, database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: i dati nell'account di archiviazione Microsoft Azure vengono sempre replicati automaticamente per assicurare durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente. 

È anche possibile usare la funzionalità di gestione del ciclo di vita per eseguire il backup dei dati nel livello archivio. Inoltre, abilitare l'eliminazione temporanea per i backup archiviati nell'account di archiviazione.

- [Informazioni sulla ridondanza di archiviazione di Azure e sui contratti di Service-Level](../storage/common/storage-redundancy.md)

- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: usare Azure Resource Manager per distribuire servizi cognitivi e risorse correlate. Azure Resource Manager offre la possibilità di esportare modelli, che consentono di ridistribuire la soluzione nel corso del ciclo di vita di sviluppo e di avere la certezza che le risorse vengano distribuite in uno stato coerente. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari. Eseguire il backup delle chiavi precondivise all'interno Azure Key Vault.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Come creare una risorsa di servizi cognitivi usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione ad automazione di Azure](../automation/automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari con una sottoscrizione isolata, se necessario. Testare il ripristino delle chiavi precondivise di cui è stato eseguito il backup.

- [Distribuire le risorse con i modelli ARM e portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro i modelli di Azure Resource Manager. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con Team Foundation Server.

Usare il controllo degli accessi in base al ruolo di Azure per proteggere le chiavi gestite dal cliente. Abilitare Soft-Delete ed eliminare la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. 

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [È anche possibile sfruttare la guida alla gestione degli eventi imprevisti di sicurezza del NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.

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
