---
title: Procedure di sicurezza consigliate per gli asset di Azure
titleSuffix: Azure security
description: Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altri asset in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717424"
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altri asset in Azure.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato a intervalli regolari per riflettere tali modifiche.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definire e distribuire procedure di sicurezza operative solide
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Azure. La sicurezza operativa di Azure si basa su un framework che incorpora le conoscenze acquisite tramite funzionalità univoche di Microsoft, tra cui Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), il programma [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

## <a name="manage-and-monitor-user-passwords"></a>Gestire e monitorare le password utente
La tabella seguente elenca alcune procedure consigliate relative alla gestione delle password utente:

**Procedura consigliata:** assicurarsi di avere il livello corretto di protezione password nel cloud.   
**Dettagli:** seguire le indicazioni fornite in Linee guida per le [password Microsoft,](https://www.microsoft.com/research/publication/password-guidance/)che hanno come ambito gli utenti delle piattaforme di gestione delle identità Microsoft (Azure Active Directory, Active Directory e account Microsoft).

**Procedura consigliata:** monitorare le azioni sospette correlate agli account utente.   
**Dettagli:** monitorare gli [utenti a rischio](../../active-directory/identity-protection/overview-identity-protection.md) e gli accesso [a](../../active-directory/identity-protection/overview-identity-protection.md) rischio usando i report Azure AD sicurezza.

**Procedura consigliata:** rilevare e correggere automaticamente le password ad alto rischio.   
**Dettagli:** [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) è una funzionalità dell'Azure AD Premium P2 che consente di:

- Rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione
- Configurare risposte automatizzate alle azioni sospette rilevate correlate alle identità dell'organizzazione
- Analizzare gli eventi imprevisti sospetti e intraprendere le azioni appropriate per risolverli

## <a name="receive-incident-notifications-from-microsoft"></a>Ricevere notifiche di eventi imprevisti da Microsoft
Assicurarsi che il team delle operazioni di sicurezza riceva le notifiche degli eventi imprevisti di Azure da Microsoft. Una notifica degli eventi imprevisti consente al team di sicurezza di sapere che le risorse di Azure sono state compromesse in modo che possano rispondere rapidamente e correggere i potenziali rischi per la sicurezza.

Nel portale di registrazione di Azure è possibile assicurarsi che le informazioni di contatto dell'amministratore includano dettagli che notificano le operazioni di sicurezza. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizzare le sottoscrizioni di Azure in gruppi di gestione
Se l'organizzazione ha molte sottoscrizioni, potrebbe essere necessario trovare una modalità di gestione efficiente dell'accesso, dei criteri e della conformità per tali sottoscrizioni. [I gruppi di gestione di Azure](../../governance/management-groups/create-management-group-portal.md) forniscono un livello di ambito superiore alle sottoscrizioni. Le sottoscrizioni vengono organizzate in contenitori denominati gruppi di gestione e le condizioni di governance vengono applicate ai gruppi di gestione. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione.

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni in una directory. A ogni directory viene assegnato un singolo gruppo di gestione di primo livello denominato gruppo di gestione radice. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente l'applicazione di criteri globali e assegnazioni di ruolo di Azure a livello di directory.

Ecco alcune procedure consigliate per l'uso dei gruppi di gestione:

**Procedura consigliata:** assicurarsi che le nuove sottoscrizioni applino elementi di governance, ad esempio criteri e autorizzazioni, non appena vengono aggiunte.   
**Dettagli:** usare il gruppo di gestione radice per assegnare elementi di sicurezza a livello aziendale applicabili a tutti gli asset di Azure. I criteri e le autorizzazioni sono esempi di elementi.

**Procedura consigliata:** allineare i livelli principali dei gruppi di gestione alla strategia di segmentazione per fornire un punto di controllo e coerenza dei criteri all'interno di ogni segmento.   
**Dettagli:** creare un singolo gruppo di gestione per ogni segmento nel gruppo di gestione radice. Non creare altri gruppi di gestione nella radice.

**Procedura consigliata:** limitare la profondità del gruppo di gestione per evitare confusione che ostacola sia le operazioni che la sicurezza.   
**Dettagli:** limitare la gerarchia a tre livelli, inclusa la radice.

**Procedura consigliata:** selezionare con attenzione gli elementi da applicare all'intera organizzazione con il gruppo di gestione radice.   
**Dettagli:** assicurarsi che gli elementi del gruppo di gestione radice siano chiaramente da applicare a ogni risorsa e che siano a basso impatto.

I candidati buoni includono:

- Requisiti normativi che hanno un impatto aziendale chiaro (ad esempio, restrizioni relative alla sovranità dei dati)
- Requisiti con un potenziale effetto negativo quasi zero sulle operazioni, ad esempio criteri con effetto di controllo o assegnazioni di autorizzazioni del controllo degli accessi in base al ruolo di Azure che sono state attentamente esaminate

**Procedura consigliata:** pianificare e testare attentamente tutte le modifiche a livello aziendale nel gruppo di gestione radice prima di applicarle (criteri, modello di controllo degli accessi in base al ruolo di Azure e così via).   
**Dettagli:** le modifiche apportate al gruppo di gestione radice possono influire su ogni risorsa in Azure. Anche se offrono un modo efficace per garantire la coerenza nell'organizzazione, gli errori o l'utilizzo non corretto possono influire negativamente sulle operazioni di produzione. Testare tutte le modifiche al gruppo di gestione radice in un lab di test o in un progetto pilota di produzione.

## <a name="streamline-environment-creation-with-blueprints"></a>Semplificare la creazione dell'ambiente con i progetti
[Il servizio Azure Blueprints](../../governance/blueprints/overview.md) consente ai cloud architect e ai gruppi centrali di information technology di definire un set ripetibile di risorse di Azure che implementa e rispetta gli standard, i modelli e i requisiti di un'organizzazione. Azure Blueprints consente ai team di sviluppo di creare e configurare rapidamente nuovi ambienti con un set di componenti predefiniti e la certezza che creino tali ambienti entro la conformità dell'organizzazione.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorare i servizi di archiviazione per rilevare cambiamenti inattesi nel comportamento
La diagnosi e la risoluzione dei problemi in un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse di quanto lo siano in ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi tipi di ambienti. Il traffico di rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione.

È consigliabile monitorare continuamente i servizi di archiviazione usati dall'applicazione per individuare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta più lunghi. Usare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che si ottengono con il monitoraggio e la registrazione aiutano a determinare la causa radice del problema incontrato dall'applicazione. È possibile quindi identificare il problema e determinare le misure appropriate per correggerlo.

Il servizio [Analisi archiviazione di Azure](../../storage/common/storage-analytics.md) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione di Azure. È consigliabile usare questi dati per tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi relativi al proprio account di archiviazione.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
[Centro sicurezza di Azure](../../security-center/security-center-introduction.md) consente di impedire, rilevare e rispondere alle minacce offrendo maggiore visibilità (e controllo) sulla sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, aiuta il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre una sicurezza limitata solo per le risorse di Azure. Il livello Standard estende queste funzionalità in locale e in altri cloud. Il Centro sicurezza Standard consente di individuare e correggere le vulnerabilità di sicurezza, applicare controlli di accesso e applicazioni per bloccare le attività dannose, rilevare le minacce usando analisi e intelligence e rispondere rapidamente in caso di attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. È consigliabile aggiornare [la sottoscrizione di Azure al livello Standard del Centro sicurezza.](../../security-center/security-center-get-started.md)

Usare il Centro sicurezza per ottenere una visualizzazione centralizzata dello stato di sicurezza di tutte le risorse di Azure. Il Centro sicurezza consente di verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e di identificare rapidamente le risorse che richiedono attenzione.

Il Centro sicurezza si integra anche [con Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)che offre funzionalità complete di rilevamento e risposta degli endpoint (EDR). Con l'integrazione di Microsoft Defender ATP è possibile individuare le anomalie. È anche possibile rilevare e rispondere agli attacchi avanzati sugli endpoint server monitorati dal Centro sicurezza.

Quasi tutte le organizzazioni aziendali hanno un sistema siem (Security Information and Event Management) per identificare le minacce emergenti consolidando le informazioni di log provenienti da diversi dispositivi di raccolta dei segnali. I log vengono quindi analizzati da un sistema di analisi dei dati per identificare ciò che è "interessante" dal rumore che è inevitabile in tutte le soluzioni di raccolta e analisi dei log.

[Azure Sentinel](../../sentinel/overview.md) è una soluzione DIA (Security Information and Event Management) e SOAR (Security Orchestration Automated Response) scalabile, nativa del cloud. Azure Sentinel l'analisi della sicurezza intelligente e l'intelligence per le minacce tramite il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta automatica alle minacce.

Ecco alcune procedure consigliate per prevenire, rilevare e rispondere alle minacce:

**Procedura consigliata:** aumentare la velocità e la scalabilità della soluzione SIEM usando un SIEM basato sul cloud.   
**Dettagli:** esaminare le funzionalità [](../../sentinel/overview.md) e le Azure Sentinel e confrontarle con le funzionalità di ciò che si sta usando in locale. Valutare la possibilità Azure Sentinel se soddisfa i requisiti SIEM dell'organizzazione.

**Procedura consigliata:** individuare le vulnerabilità di sicurezza più gravi in modo da poter classificare in ordine di priorità l'analisi.   
**Dettagli:** esaminare il [punteggio di](../../security-center/secure-score-security-controls.md) sicurezza di Azure per visualizzare le raccomandazioni risultanti dai criteri e dalle iniziative di Azure Centro sicurezza di Azure. Queste raccomandazioni consentono di risolvere i principali rischi, ad esempio aggiornamenti della sicurezza, protezione degli endpoint, crittografia, configurazioni di sicurezza, WAF mancante, macchine virtuali connesse a Internet e molti altri.

Il punteggio di sicurezza, basato sui controlli Center for Internet Security (CIS), consente di eseguire il benchmark della sicurezza di Azure dell'organizzazione rispetto a origini esterne. La convalida esterna consente di convalidare e arricchire la strategia di sicurezza del team.

**Procedura consigliata:** monitorare il percorso di sicurezza di computer, reti, servizi di archiviazione e dati e applicazioni per individuare e classificare in ordine di priorità i potenziali problemi di sicurezza.  
**Dettagli:** seguire le [raccomandazioni sulla sicurezza](../../security-center/security-center-recommendations.md) nel Centro sicurezza a partire da , con gli elementi con la priorità più alta.

**Procedura consigliata:** integrare gli avvisi del Centro sicurezza nella soluzione siem (Security Information and Event Management).   
**Dettagli:** la maggior parte delle organizzazioni con un sistema SIEM lo usa come centro di controllo per gli avvisi di sicurezza che richiedono una risposta dell'analista. Gli eventi elaborati generati dal Centro sicurezza vengono pubblicati nel log attività di Azure, uno dei log disponibili tramite Monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Per [istruzioni, vedere Trasmettere avvisi a una soluzione SIEM, SOAR](../../security-center/export-to-siem.md) o gestione dei servizi IT. Se si usa un Azure Sentinel, vedere [Connettersi Centro sicurezza di Azure](../../sentinel/connect-azure-security-center.md).

**Procedura consigliata:** integrare i log di Azure con le informazioni di sicurezza e gestione degli eventi.   
**Dettagli:** usare Monitoraggio di Azure [per raccogliere ed esportare i dati.](../../azure-monitor/overview.md#integrate-and-export-data) Questa procedura è fondamentale per abilitare l'analisi degli eventi imprevisti della sicurezza e la conservazione dei log online è limitata. Se si usa un'Azure Sentinel, vedere [Connettere le origini dati.](../../sentinel/connect-data-sources.md)

**Procedura consigliata:** velocizzare i processi di indagine e ricerca e ridurre i falsi positivi integrando le funzionalità di rilevamento e risposta degli endpoint nell'analisi degli attacchi.   
**Dettagli:** [abilitare l'integrazione di Microsoft Defender per gli endpoint](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) tramite i criteri di sicurezza del Centro sicurezza. È consigliabile usare Azure Sentinel per la ricerca di minacce e la risposta agli eventi imprevisti.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure, i clienti combinano varie risorse di rete, ad esempio una rete virtuale, ExpressRoute, un gateway applicazione e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher di Azure](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio a livello di area, dotato di strumenti di diagnostica e di visualizzazione che consentono di monitorare e diagnosticare le condizioni a livello di scenario di rete in Azure, verso e da Azure.

Di seguito vengono descritte le procedure consigliate per il monitoraggio della rete e gli strumenti disponibili.

**Procedura consigliata**: automatizzare il monitoraggio della rete in remoto con l'acquisizione pacchetti.  
**Dettaglio**: monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali usando Network Watcher. Attivare l'[acquisizione dei pacchetti](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

**Procedura consigliata**: acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.  
**Dettaglio**: ottenere informazioni approfondite sui modelli di traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Le informazioni contenute nei log dei flussi aiutano a raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

**Procedura consigliata**: diagnosticare i problemi di connettività della VPN.  
**Dettaglio**: usare Network Watcher per [diagnosticare i problemi di connessione e di Gateway VPN più comuni](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Non è solo possibile identificare il problema ma si può anche usare i log dettagliati per altre indagini.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Usare le seguenti procedure consigliate di DevOps per assicurarsi che i team e l'azienda siano produttivi ed efficienti.

**Procedura consigliata**: automatizzare la creazione e la distribuzione di servizi.  
**Dettaglio**: l'[infrastruttura come codice](/azure/devops/learn/what-is-infrastructure-as-code) rappresenta un set di tecniche e procedure che aiutano i professionisti IT a evitare il carico di lavoro associato alla creazione e alla gestione quotidiana dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori di software per creare e gestire il codice delle applicazioni.

È possibile usare [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) per effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Lo stesso modello viene usato per distribuire ripetutamente l'applicazione in ogni fase del ciclo di vita dell'applicazione.

**Procedura consigliata**: compilare e distribuire automaticamente app Web o servizi cloud di Azure.  
**Dettagli:** è possibile configurare l'Azure DevOps Projects per  [compilare](/azure/devops/pipelines/index) e distribuire automaticamente app Web o servizi cloud di Azure. Azure DevOps distribuisce automaticamente i file binari dopo l'esecuzione di una compilazione in Azure dopo ogni archiviazione del codice. Il processo di compilazione del pacchetto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.

**Procedura consigliata**: automatizzare la gestione del rilascio.  
**Dettaglio**: [Azure Pipelines](/azure/devops/pipelines/index) è una soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Azure Pipelines è possibile automatizzare il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.

**Procedura consigliata**: verificare le prestazioni dell'app prima di implementarla o di distribuirne gli aggiornamenti nell'ambiente di produzione.  
**Dettagli:** eseguire test di carico [basati sul](/azure/devops/test/load-test/overview#alternatives) cloud per:

- Individuare problemi di prestazioni nell'app.
- Migliorare la qualità della distribuzione.
- Assicurarsi che l'app sia sempre disponibile.
- Assicurarsi che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing.

[Apache JMeter è](https://jmeter.apache.org/) uno strumento gratuito e diffuso open source con un forte aiuto della community.

**Procedura consigliata**: monitorare le prestazioni dell'applicazione.  
**Dettaglio**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni e include strumenti di analisi che consentono di diagnosticare i problemi e di conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="mitigate-and-protect-against-ddos"></a>Attenuazione e protezione da attacchi Distributed Denial of Service (DDoS)
Il Distributed Denial of Service (DDoS) è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati con dimensioni e impatto maggiori. Possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

La progettazione e la creazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiedono pianificazione e progettazione per un'ampia gamma di modalità di errore. Di seguito vengono descritte le procedure consigliate per la creazione di servizi resilienti a DDoS in Azure.

**Procedura consigliata**: assicurarsi che la sicurezza sia un aspetto prioritario durante l'intero ciclo di vita di un'applicazione, dalla progettazione e l'implementazione alla distribuzione e al funzionamento. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità elevata di risorse, provocando un'interruzione del servizio.  
**Dettaglio**: per proteggere un servizio in esecuzione in Microsoft Azure, è consigliabile avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](/azure/architecture/guide/pillars). I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

**Procedura consigliata**: progettare le applicazioni con [scalabilità orizzontale](/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.  
**Dettaglio**: per [Servizio app di Azure](../../app-service/overview.md) selezionare un [piano di servizio app](../../app-service/overview-hosting-plans.md) che offra più istanze.

Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../../cloud-services/cloud-services-choose-me.md).

Per [Macchine virtuali di Microsoft Azure](../../virtual-machines/windows/overview.md), verificare che l'architettura di VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../../virtual-machines/windows/tutorial-availability-sets.md). Si consiglia di usare set di scalabilità di macchine virtuali per le funzionalità di scalabilità automatica.

**Procedura consigliata**: la disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. Implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.  
**Dettaglio**: il rischio di attacco aumenta con le dimensioni (superficie di attacco) dell'applicazione. È possibile ridurre la superficie di attacco usando un elenco di approvazione per chiudere lo spazio indirizzi IP esposto e le porte di ascolto che non sono necessarie nei servizi di bilanciamento del carico ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) [e gateway applicazione di Azure](../../application-gateway/application-gateway-create-probe-portal.md)).

I [gruppi di sicurezza di rete](../../virtual-network/network-security-groups-overview.md) rappresentano un altro modo per ridurre la superficie di attacco. È possibile usare [tag di servizio](../../virtual-network/network-security-groups-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../../virtual-network/network-security-groups-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita.

Tramite gli [endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica.

Azure prevede due [offerte di servizio](../../ddos-protection/ddos-protection-overview.md) contro gli attacchi DDoS per la protezione della rete:

- La protezione di base è integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base non richiede alcuna modifica della configurazione utente o dell'applicazione e aiuta a proteggere tutti i servizi di Azure, inclusi i servizi PaaS come DNS di Azure.
- La protezione standard offre funzionalità avanzate di attenuazione degli attacchi DDoS contro gli attacchi alla rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

## <a name="enable-azure-policy"></a>Abilitare Criteri di Azure
[Criteri di Azure](../../governance/policy/overview.md) è un servizio di Azure che consente di creare, assegnare e gestire i criteri. Questi criteri applicano regole ed effetti sulle risorse, in modo che tali risorse rimangano conformi agli standard aziendali e ai contratti di servizio. Criteri di Azure soddisfa questa esigenza valutando la mancata conformità delle risorse ai criteri assegnati.

Abilitare Criteri di Azure monitorare e applicare i criteri scritti dell'organizzazione. Ciò garantisce la conformità ai requisiti di sicurezza aziendali o normativi gestendo centralmente i criteri di sicurezza nei carichi di lavoro cloud ibridi. Informazioni su come [creare e gestire i criteri per applicare la conformità.](../../governance/policy/tutorials/create-and-manage.md) Vedere [Criteri di Azure struttura delle definizioni](../../governance/policy/concepts/definition-structure.md) per una panoramica degli elementi di un criterio.

Ecco alcune procedure consigliate per la sicurezza da seguire dopo l'adozione Criteri di Azure:

**Procedura consigliata:** i criteri supportano diversi tipi di effetti. Per altre informazioni, vedere struttura [Criteri di Azure definizione.](../../governance/policy/concepts/definition-structure.md#policy-rule) Le operazioni aziendali possono essere influenzate  negativamente dall'effetto deny  e dall'effetto di correzione, quindi iniziare con l'effetto audit per limitare il rischio di impatto negativo dai criteri.    
**Dettagli:** [avvia le distribuzioni di criteri in modalità di controllo](../../governance/policy/concepts/definition-structure.md#policy-rule) e quindi procede in un secondo momento per **rifiutare** **o correggere**. Testare ed esaminare i risultati dell'effetto di controllo prima di passare a **rifiutare** **o correggere**.

Per altre informazioni, vedere [Creare e gestire criteri per applicare la conformità.](../../governance/policy/tutorials/create-and-manage.md)

**Procedura consigliata:** identificare i ruoli responsabili del monitoraggio delle violazioni dei criteri e assicurarsi che l'azione correttiva corretta sia eseguita rapidamente.   
**Dettagli:** fare in modo che il ruolo assegnato monitori la conformità [tramite portale di Azure](../../governance/policy/how-to/get-compliance-data.md#portal) o tramite la riga [di comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Procedura consigliata:** Criteri di Azure è una rappresentazione tecnica dei criteri scritti di un'organizzazione. Eseguire il mapping Criteri di Azure definizioni dei criteri aziendali per ridurre la confusione e aumentare la coerenza.   
**Dettagli:** mapping dei documenti nella documentazione dell'organizzazione o nella definizione Criteri di Azure aggiungendo un [](../../governance/policy/concepts/definition-structure.md#display-name-and-description) riferimento ai criteri aziendali nella definizione dei criteri o nella descrizione della definizione [dell'iniziativa.](../../governance/policy/concepts/initiative-definition-structure.md#metadata)

## <a name="monitor-azure-ad-risk-reports"></a>Monitorare i Azure AD di rischio
La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure AD usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato [rilevamento dei rischi](../../active-directory/identity-protection/overview-identity-protection.md). I rilevamenti dei rischi vengono registrati nei report Azure AD sicurezza. Per altre informazioni, leggere il report [sulla](../../active-directory/identity-protection/overview-identity-protection.md) sicurezza degli utenti a rischio e il report sulla sicurezza [degli accesso a rischio](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](/archive/blogs/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com