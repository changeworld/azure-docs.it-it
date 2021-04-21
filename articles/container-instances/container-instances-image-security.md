---
title: Considerazioni sulla sicurezza per le istanze di contenitore
description: Raccomandazioni per proteggere immagini e segreti per Istanze di Azure Container e considerazioni generali sulla sicurezza per qualsiasi piattaforma di contenitori
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 40284c6e42cf1060906c6248495d08e133bda5bb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812661"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerazioni sulla sicurezza per Istanze di Azure Container

Questo articolo presenta alcune considerazioni sulla sicurezza per l'uso Istanze di Azure Container per eseguire le app contenitore. Gli argomenti includono:

> [!div class="checklist"]
> * **Raccomandazioni sulla sicurezza** per la gestione di immagini e segreti per Istanze di Azure Container
> * **Considerazioni sull'ecosistema di contenitori per** tutto il ciclo di vita dei contenitori, per qualsiasi piattaforma di contenitori

Per raccomandazioni complete che consentono di migliorare il livello di sicurezza della distribuzione, vedere Baseline di sicurezza di [Azure per Istanze di Container.](security-baseline.md)


## <a name="security-recommendations-for-azure-container-instances"></a>Raccomandazioni sulla sicurezza per Istanze di Azure Container

### <a name="use-a-private-registry"></a>Usare un registro privato

I contenitori sono costituiti da immagini archiviati in una o più repository. Questi repository possono appartenere a un registro pubblico, ad esempio [Docker Hub](https://hub.docker.com), o a un registro privato. Un esempio di un registro privato è il [registro attendibile Docker](https://docs.docker.com/datacenter/dtr/), che può essere installato in locale o in un cloud privato virtuale. È anche possibile usare servizi di registro contenitori privati basati sul cloud, tra cui [Registro Azure Container](../container-registry/container-registry-intro.md). 

Un'immagine del contenitore disponibile pubblicamente non garantisce la sicurezza. Le immagini del contenitore sono costituite da più livelli software e ogni livello software potrebbe avere vulnerabilità. Per ridurre la minaccia di attacchi, è consigliabile archiviare e recuperare immagini da un registro privato, ad esempio Registro Azure Container o Docker Trusted Registry. Oltre a fornire un registro privato gestito, [](../container-registry/container-registry-authentication.md) Registro Azure Container supporta l'autenticazione basata su entità servizio tramite Azure Active Directory per i flussi di autenticazione di base. Questa autenticazione include l'accesso in base al ruolo per le autorizzazioni di sola lettura (pull), scrittura (push) e altre autorizzazioni.

### <a name="monitor-and-scan-container-images"></a>Monitorare e analizzare le immagini del contenitore

Sfruttare le soluzioni per analizzare le immagini dei contenitori in un registro privato e identificare le potenziali vulnerabilità. È importante comprendere la profondità del rilevamento delle minacce fornito dalle diverse soluzioni.

Ad esempio, Registro Azure Container [facoltativamente](../security-center/defender-for-container-registries-introduction.md) si integra con Centro sicurezza di Azure per analizzare automaticamente tutte le immagini Linux di cui è stato fatto il push in un registro. Centro sicurezza di Azure dello scanner Qualys integrato di qualys rileva le vulnerabilità delle immagini, le classifica e fornisce indicazioni per la correzione.

Le soluzioni di monitoraggio della sicurezza e di analisi delle immagini, ad esempio [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sono disponibili anche tramite Azure Marketplace.  

### <a name="protect-credentials"></a>Proteggere le credenziali

I contenitori possono essere distribuiti tra diversi cluster e aree di Azure. È quindi necessario proteggere le credenziali necessarie per gli account di accesso o l'accesso alle API, ad esempio password o token. Assicurarsi che solo gli utenti con privilegi possano accedere a tali contenitori in transito e in pausa. Eseguire l'inventario di tutti i segreti delle credenziali e quindi richiedere agli sviluppatori di usare strumenti di gestione dei segreti emergenti progettati per le piattaforme contenitore.  Assicurarsi che la soluzione includa database crittografati, crittografia TLS per i dati segreti in transito e controllo degli accessi in base al ruolo di Azure con privilegi [minimi.](../role-based-access-control/overview.md) [Azure Key Vault](../key-vault/general/security-features.md) è un servizio cloud che protegge le chiavi di crittografia e i segreti (ad esempio certificati, stringhe di connessione e password) per le applicazioni in contenitori. Poiché questi dati sono sensibili e business critical, proteggere l'accesso agli insiemi di credenziali delle chiavi in modo che solo le applicazioni autorizzate e gli utenti possano accedervi.

## <a name="considerations-for-the-container-ecosystem"></a>Considerazioni sull'ecosistema di contenitori

Le misure di sicurezza seguenti, implementate correttamente e gestite in modo efficace, consentono di proteggere e proteggere l'ecosistema di contenitori. Queste misure si applicano per tutto il ciclo di vita dei contenitori, dallo sviluppo alla distribuzione di produzione e a una gamma di orchestratori di contenitori, host e piattaforme. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Usare la gestione delle vulnerabilità come parte del ciclo di vita dello sviluppo di contenitori 

Usando una gestione efficace delle vulnerabilità durante tutto il ciclo di vita di sviluppo dei contenitori, si migliorano le probabilità di identificare e risolvere i problemi di sicurezza prima che diventino un problema più grave. 

### <a name="scan-for-vulnerabilities"></a>Ricercare le vulnerabilità 

Le nuove vulnerabilità vengono individuate continuamente, quindi l'analisi e l'identificazione delle vulnerabilità è un processo continuo. Incorporare l'analisi delle vulnerabilità per tutto il ciclo di vita del contenitore:

* Come controllo finale nella pipeline di sviluppo, è necessario eseguire un'analisi della vulnerabilità sui contenitori prima di eseguire il push delle immagini in un registro pubblico o privato. 
* Continuare ad analizzare le immagini del contenitore nel registro per identificare eventuali difetti che in qualche modo sono stati persi durante lo sviluppo e per risolvere eventuali vulnerabilità appena individuate che potrebbero esistere nel codice usato nelle immagini del contenitore.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Eseguire il mapping delle vulnerabilità delle immagini ai contenitori in esecuzione 

È necessario disporre di un mezzo per eseguire il mapping delle vulnerabilità identificate nelle immagini dei contenitori ai contenitori in esecuzione, in modo che i problemi di sicurezza possano essere mitigati o risolti.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Assicurarsi che nell'ambiente siano usate solo immagini approvate 

Il cambiamento e la volatilità in un ecosistema di contenitori sono sufficienti senza consentire anche contenitori sconosciuti. Consentire solo immagini del contenitore approvate. Disporre di strumenti e processi per monitorare e impedire l'uso di immagini del contenitore non approvate. 

Un modo efficace per ridurre la superficie di attacco e impedire agli sviluppatori di commettere errori di sicurezza critici è controllare il flusso delle immagini del contenitore nell'ambiente di sviluppo. Ad esempio, è possibile sanzionare una singola distribuzione di Linux come immagine di base, preferibilmente un'immagine snella (Alpine o CoreOS anziché Ubuntu), per ridurre al minimo la superficie per potenziali attacchi. 

La firma di immagini o l'impronta digitale possono fornire una catena di custodia che consente di verificare l'integrità dei contenitori. Ad esempio, Registro Azure Container supporta il modello [](https://docs.docker.com/engine/security/trust/content_trust) di attendibilità dei contenuti di Docker, che consente agli editori di immagini di firmare le immagini di cui viene eseguito il push in un registro e agli utenti delle immagini di eseguire il pull solo delle immagini firmate.

### <a name="permit-only-approved-registries"></a>Consenti solo registri approvati 

Un'estensione per garantire che l'ambiente usi solo immagini approvate è consentire solo l'uso di registri contenitori approvati. L'uso di registri contenitori approvati riduce l'esposizione ai rischi limitando la possibilità di introduzione di vulnerabilità sconosciute o problemi di sicurezza. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantire l'integrità delle immagini durante tutto il ciclo di vita 

Parte della gestione della sicurezza per tutto il ciclo di vita del contenitore è garantire l'integrità delle immagini del contenitore nel Registro di sistema e quando vengono modificate o distribuite nell'ambiente di produzione. 

* Le immagini con vulnerabilità, anche minori, non devono essere consentite in un ambiente di produzione. Idealmente, tutte le immagini distribuite nell'ambiente di produzione devono essere salvate in un registro privato accessibile a pochi utenti. Mantenere il numero di immagini di produzione ridotte per assicurarsi che possano essere gestite in modo efficace.

* Poiché è difficile individuare l'origine del software da un'immagine del contenitore disponibile pubblicamente, creare immagini dall'origine per garantire la conoscenza dell'origine del livello. Quando una vulnerabilità emerge in un'immagine del contenitore generata automaticamente, i clienti possono trovare un percorso più rapido per la risoluzione. Con un'immagine pubblica, i clienti devono trovare la radice di un'immagine pubblica per correggerla o ottenere un'altra immagine sicura dall'editore. 

* Non è garantito che un'immagine accuratamente analizzata distribuita nell'ambiente di produzione sia aggiornata per la durata dell'applicazione. È possibile che vengano segnalate vulnerabilità della sicurezza per i livelli dell'immagine precedentemente ignoti o che sono stati introdotti dopo la distribuzione di produzione. 

  Controllare periodicamente le immagini distribuite nell'ambiente di produzione per identificare le immagini non aggiornate o non aggiornate in un intervallo di tempo. È possibile usare metodologie di distribuzione blu-verde e meccanismi di aggiornamento in sequenza per aggiornare le immagini dei contenitori senza tempi di inattività. È possibile analizzare le immagini usando gli strumenti descritti nella sezione precedente. 

* Usare una pipeline di integrazione continua con analisi integrata della sicurezza per creare immagini sicure ed eseguire il push nel registro privato. L'analisi delle vulnerabilità incorporata nella soluzione CI garantisce che le immagini che passano tutti i test vengano inserite nel registro privato dalla produzione da cui vengono distribuiti i carichi di lavoro della produzione. 

  Un errore della pipeline CI garantisce che non sia possibile eseguire il push delle immagini vulnerabili nel registro privato usato per le distribuzioni del carico di lavoro di produzione. Automatizza anche l'analisi della sicurezza delle immagini se è presente un numero significativo di immagini. Altrimenti il controllo manuale delle immagini per le vulnerabilità di sicurezza può risultare lungo e dettagliato e tendente all'errore. 

### <a name="enforce-least-privileges-in-runtime"></a>Applicare privilegi minimi in fase di esecuzione 

Il concetto di privilegi minimi è una procedura consigliata di sicurezza di base che si applica anche ai contenitori. Quando viene sfruttata una vulnerabilità, in genere concede all'utente malintenzionato l'accesso e privilegi uguali a quelli dell'applicazione o del processo compromesso. Assicurandosi che i contenitori funzionino con i privilegi e l'accesso più bassi necessari per eseguire il processo, si riduce l'esposizione ai rischi. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Ridurre la superficie di attacco del contenitore rimuovendo i privilegi non necessari 

È anche possibile ridurre al minimo la superficie di attacco potenziale rimuovendo dal runtime del contenitore eventuali processi o privilegi inutili o inutili. I contenitori con privilegi vengono eseguiti come radice. Se un utente malintenzionato o un carico di lavoro esegue l'escape in un contenitore con privilegi, il contenitore verrà quindi eseguito come radice in tale sistema.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Preapprovare file ed eseguibili a cui il contenitore può accedere o eseguire 

La riduzione del numero di variabili o sconosciuti consente di mantenere un ambiente stabile e affidabile. La limitazione dei contenitori in modo che possano accedere o eseguire solo file ed eseguibili preapprovati o in elenco di sicurezza è un metodo collaudato per limitare l'esposizione ai rischi.  

È molto più semplice gestire un elenco di indirizzi attendibili quando viene implementato dall'inizio. Un elenco di indirizzi attendibili offre una misura di controllo e gestibilità quando si apprende quali file ed eseguibili sono necessari per il corretto funzionamento dell'applicazione. 

Un elenco di sicurezza non solo riduce la superficie di attacco, ma può anche fornire una baseline per le anomalie e impedire i casi d'uso degli scenari di interruzione dei contenitori e dei "vicini rumorosi". 

### <a name="enforce-network-segmentation-on-running-containers"></a>Applicare la segmentazione di rete nei contenitori in esecuzione  

Per proteggere i contenitori in una subnet dai rischi per la sicurezza in un'altra subnet, mantenere la segmentazione di rete (o la segmentazione nano) o la separazione tra i contenitori in esecuzione. La gestione della segmentazione di rete può anche essere necessaria per usare i contenitori nei settori necessari per soddisfare i requisiti di conformità.  

Ad esempio, lo strumento partner [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) offre un approccio automatizzato per la segmentazione nano. Aqua monitora le attività di rete dei contenitori in fase di esecuzione. Identifica tutte le connessioni di rete in ingresso e in uscita da/verso altri contenitori, servizi, indirizzi IP e internet pubblico. La segmentazione nano viene creata automaticamente in base al traffico monitorato. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorare l'attività del contenitore e l'accesso degli utenti 

Come per qualsiasi ambiente IT, è consigliabile monitorare in modo coerente l'attività e l'accesso degli utenti all'ecosistema di contenitori per identificare rapidamente eventuali attività sospette o dannose. Azure offre soluzioni di monitoraggio dei contenitori, tra cui:

* [Monitoraggio di Azure per i contenitori](../azure-monitor/containers/container-insights-overview.md) monitora le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes ospitati in servizio Azure Kubernetes (AKS). Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. 

* La [soluzione Monitoraggio di Azure Container](../azure-monitor/containers/containers.md) consente di visualizzare e gestire altri host contenitore Docker e Windows in un'unica posizione. Ad esempio:

  * Visualizzare informazioni di controllo dettagliate che mostrano i comandi usati con i contenitori. 
  * Risolvere i problemi relativi ai contenitori visualizzando ed eseguendo ricerche nei log centralizzati senza dover visualizzare in remoto gli host Docker o Windows.  
  * Trovare contenitori che potrebbero essere rumorosi e utilizzare risorse in eccesso in un host.
  * Visualizzare le informazioni centralizzate su CPU, memoria, archiviazione e utilizzo della rete e sulle prestazioni per i contenitori.  

  La soluzione supporta gli orchestratori di contenitori, tra cui Docker Swarm, DC/OS, Kubernetes non gestiti, Service Fabric e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorare l'attività delle risorse del contenitore 

Monitorare l'attività delle risorse, ad esempio file, rete e altre risorse a cui accedono i contenitori. Il monitoraggio dell'attività e dell'utilizzo delle risorse è utile sia per il monitoraggio delle prestazioni che come misura di sicurezza. 

[Monitoraggio di Azure](../azure-monitor/overview.md) il monitoraggio di base per i servizi di Azure consentendo la raccolta di metriche, log attività e log di diagnostica. Il log attività, ad esempio, indica quando vengono create nuove risorse o quando vengono modificate risorse esistenti. 

  Sono disponibili metriche che forniscono statistiche sulle prestazioni per diverse risorse, nonché per il sistema operativo all'interno di una macchina virtuale. È possibile visualizzare questi dati con una delle utilità di esplorazione disponibili nel portale di Azure e creare avvisi basati su queste metriche. Monitoraggio di Azure fornisce la più veloce pipeline di metriche (da 5 minuti a 1 minuto), che è necessario usare per gestire notifiche e avvisi con particolari requisiti di tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registrare l'accesso utente amministrativo di tutti i contenitori per il controllo 

Mantenere un'audit trail di accesso amministrativo all'ecosistema di contenitori, inclusi il cluster Kubernetes, il registro contenitori e le immagini dei contenitori. Questi log potrebbero essere necessari a scopo di controllo e saranno utili come prova forense dopo qualsiasi evento imprevisto della sicurezza. Le soluzioni di Azure includono:

* [Integrazione di servizio Azure Kubernetes con Centro sicurezza di Azure](../security-center/defender-for-kubernetes-introduction.md) monitorare la configurazione della sicurezza dell'ambiente cluster e generare raccomandazioni sulla sicurezza
* [Soluzione Monitoraggio di Azure Container](../azure-monitor/containers/containers.md)
* Log delle risorse [per Istanze di Azure Container](container-instances-log-analytics.md) e [Registro Azure Container](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Passaggi successivi

* Per raccomandazioni [complete che](security-baseline.md) consentono di migliorare il livello di sicurezza della distribuzione, vedere Baseline di sicurezza di Azure per Istanze di Container.

* Altre informazioni sull'uso [Centro sicurezza di Azure](../security-center/container-security.md) rilevamento delle minacce in tempo reale negli ambienti in contenitori.

* Per altre informazioni sulla gestione delle vulnerabilità dei contenitori con le [soluzioni, vedere Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)