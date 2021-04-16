---
title: Materiale sussidiario e procedure consigliate
description: Scoprire le procedure consigliate e le linee guida per il backup del carico di lavoro cloud e locale nel cloud
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519064"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Eseguire il backup di carichi di lavoro cloud e locali nel cloud

## <a name="introduction"></a>Introduzione

Backup di Azure protegge in modo completo gli asset di dati in Azure tramite una soluzione semplice, sicura ed economica che richiede zero infrastrutture. È la soluzione di protezione dei dati incorporata di Azure per un'ampia gamma di carichi di lavoro. Consente di proteggere i carichi di lavoro cruciali in esecuzione nel cloud e garantisce che i backup siano sempre disponibili e gestiti su larga scala nell'intero ambiente di backup.

### <a name="intended-audience"></a>Destinatari

I destinatari principali di questo articolo sono gli amministratori IT e delle applicazioni e gli implementatori di organizzazioni di grandi e medie dimensioni, che vogliono conoscere le funzionalità della tecnologia di protezione dei dati integrata di Azure, Backup di Azure, e come implementare in modo efficiente soluzioni che proteggono meglio le distribuzioni. L'articolo presuppone che si abbia familiarità con le principali tecnologie di Azure, i concetti di protezione dei dati e si abbia esperienza nell'uso di una soluzione di backup. Le indicazioni fornite in questo articolo possono semplificare la progettazione della soluzione di backup in Azure usando modelli stabiliti ed evitare insidie note.

### <a name="how-this-article-is-organized"></a>Come è organizzato questo articolo

Anche se è facile iniziare a proteggere l'infrastruttura e le applicazioni in Azure, quando si garantisce che le risorse di Azure sottostanti siano configurate correttamente e che vengano usate in modo ottimale, è possibile accelerare il time to value. Questo articolo illustra una breve panoramica delle considerazioni di progettazione e delle linee guida per la configurazione ottimale della Backup di Azure distribuzione. Vengono esaminati i componenti principali (ad esempio l'insieme di credenziali di Servizi di ripristino, i criteri di backup) e i concetti (ad esempio, governance) e come esaminarli e le relative funzionalità con collegamenti alla documentazione dettagliata del prodotto.

## <a name="architecture"></a>Architettura

![Architettura di Backup di Azure](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Carichi di lavoro

Backup di Azure la protezione dei dati per vari carichi di lavoro (in locale e cloud). Si tratta di un meccanismo di protezione dei dati predefinito sicuro e affidabile in Azure. Consente di ridimensionare facilmente la protezione tra più carichi di lavoro senza alcun sovraccarico di gestione. Sono disponibili anche più canali di automazione per abilitare questa funzionalità (tramite PowerShell, l'interfaccia della riga di comando, i Azure Resource Manager e le API REST).

* **Archiviazione scalabile, durevole e sicura:** Backup di Azure usa l'archiviazione BLOB affidabile con funzionalità di sicurezza e disponibilità elevata integrate. È possibile scegliere le risorse di archiviazione con accesso in locale, con archiviazione con accesso in base al nome e all'account di archiviazione per i dati di backup.  

* **Integrazione del carico di lavoro nativo:** Backup di Azure offre l'integrazione nativa con i carichi di lavoro di Azure (VM, SAP HANA, SQL nelle macchine virtuali di Azure e anche File di Azure) senza dover gestire l'automazione o l'infrastruttura per distribuire gli agenti, scrivere nuovi script o effettuare il provisioning dell'archiviazione.

### <a name="data-plane"></a>Piano dati

* **Gestione automatica dell'archiviazione:** Backup di Azure il provisioning e la gestione degli account di archiviazione per i dati di backup per garantire la scalabilità con l'aumentare dei dati di backup.

* **Protezione dall'eliminazione dannosa:** Proteggersi da eventuali tentativi accidentali e dannosi di eliminazione dei backup tramite l'eliminazione software dei backup. I dati di backup eliminati vengono archiviati gratuitamente per 14 giorni e possono essere ripristinati da questo stato.

* **Proteggere i backup crittografati:** Backup di Azure garantisce che i dati di backup siano archiviati in modo sicuro, sfruttando le funzionalità di sicurezza integrate della piattaforma Azure come il controllo degli accessi in base al ruolo di Azure e la crittografia.

* **Gestione del ciclo di vita dei dati di backup -** Backup di Azure elimina automaticamente i dati di backup meno recenti per garantire la conformità ai criteri di conservazione. È anche possibile creare livelli dei dati dall'archiviazione operativa all'archiviazione dell'insieme di credenziali.

### <a name="management-plane"></a>Piano di gestione

*  Controllo di accesso: gli insiemi di credenziali (servizi di ripristino e insiemi di credenziali di backup) forniscono le funzionalità di gestione e sono accessibili tramite portale di Azure, Centro backup, dashboard dell'insieme di credenziali, SDK, interfaccia della riga di comando e anche LE API REST. È anche un limite del controllo degli accessi in base al ruolo di Azure, che consente di limitare l'accesso ai backup solo agli amministratori di backup autorizzati.

* **Gestione dei criteri:** Backup di Azure criteri all'interno di ogni insieme di credenziali definiscono quando attivare i backup e per quanto tempo devono essere conservati. È anche possibile gestire questi criteri e applicarli a più elementi.

* **Monitoraggio e creazione di** report: Backup di Azure si integra con Log Analytics e offre la possibilità di visualizzare i report anche tramite cartelle di lavoro.

* **Gestione snapshot:** Backup di Azure snapshot per alcuni carichi di lavoro nativi di Azure (macchine virtuali e File di Azure), gestisce questi snapshot e consente ripristini rapidi da essi. Questa opzione riduce drasticamente il tempo necessario per ripristinare i dati nella memoria originale.

## <a name="vault-considerations"></a>Considerazioni sull'insieme di credenziali

Backup di Azure usa insiemi di credenziali (servizi di ripristino e insiemi di credenziali di backup) per orchestrare e gestire i backup. nonché per archiviare i dati sottoposti a backup. Una progettazione efficace dell'insieme di credenziali consente alle organizzazioni di stabilire una struttura per organizzare e gestire gli asset di backup in Azure per supportare le priorità aziendali. Quando si crea un insieme di credenziali, tenere presenti le linee guida seguenti:  

### <a name="align-to-subscription-design-strategy"></a>Allinearsi alla strategia di progettazione delle sottoscrizioni

Poiché l'insieme di credenziali è nell'ambito di una sottoscrizione,  adattare la progettazione dell'insieme di credenziali per soddisfare la strategia di progettazione della sottoscrizione, ad esempio la strategia di categoria dell'applicazione in cui le sottoscrizioni vengono separate in base ad applicazioni o servizi specifici o in base agli archetipi dell'applicazione. Per altre informazioni, vedere questo [articolo](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Singolo o più insiemi di credenziali

È possibile usare un singolo insieme di credenziali o più insiemi di credenziali per organizzare e gestire il backup. Considerare le linee guida seguenti:

* Se i carichi di lavoro sono tutti gestiti da una singola sottoscrizione e da una singola risorsa, è possibile usare un singolo insieme di credenziali per monitorare e gestire l'estate di backup.

* Se i carichi di lavoro sono distribuiti tra sottoscrizioni, è possibile creare più insiemi di credenziali, uno o più per ogni sottoscrizione.
  * Centro backup consente di avere un singolo riquadro di controllo per gestire tutte le attività correlate al backup. Fare clic [qui]() per altre informazioni.
  * È possibile personalizzare le visualizzazioni con i modelli di cartella di lavoro. Explorer di Backup è un modello di questo tipo per le macchine virtuali di Azure. Fare clic [qui](monitor-azure-backup-with-backup-explorer.md) per altre informazioni.
  * Se sono necessari criteri coerenti tra insiemi di credenziali, è possibile usare i criteri di Azure per propagare i criteri di backup tra più insiemi di credenziali. È possibile scrivere una definizione [Criteri di Azure](../governance/policy/concepts/definition-structure.md) che usa [l'effetto "deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) per propagare un criterio di backup tra più insiemi di credenziali. È anche [](../governance/policy/assign-policy-portal.md) possibile assegnare questa definizione Criteri di Azure a un ambito specifico (sottoscrizione o RG), in modo che distribuisca una risorsa "criteri di backup" a tutti gli insiemi di credenziali di Servizi di ripristino nell'ambito dell'assegnazione Criteri di Azure di ripristino. Le impostazioni dei criteri di backup ,ad esempio la frequenza di backup, la conservazione e così via, devono essere specificate dall'utente come parametri nell'assegnazione Criteri di Azure backup.

* Man mano che il footprint dell'organizzazione aumenta, è possibile spostare i carichi di lavoro tra le sottoscrizioni per i motivi seguenti: allineamento in base ai criteri di backup, consolidamento degli insiemi di credenziali, riduzione della ridondanza per risparmiare sui costi (passare da ARCHIVIAZIONE CON RID a archiviazione con ridondanza locale).  Backup di Azure supporta lo spostamento di un insieme di credenziali di Servizi di ripristino tra sottoscrizioni di Azure o in un altro gruppo di risorse all'interno della stessa sottoscrizione. Fare clic [qui](backup-azure-move-recovery-services-vault.md) per altre informazioni.

### <a name="review-default-settings"></a>Verifica impostazioni predefinite

Esaminare le impostazioni predefinite per Tipo di replica di archiviazione e Impostazioni di sicurezza per soddisfare i requisiti prima di configurare i backup nell'insieme di credenziali.

* *Il tipo di replica di* archiviazione per impostazione predefinita è impostato su Ridondanza geografica. Dopo aver configurato il backup, l'opzione da modificare è disabilitata. Seguire [questa](backup-create-rs-vault.md#set-storage-redundancy) procedura per esaminare e modificare le impostazioni.

* *L'eliminazione* software per impostazione predefinita è abilitata per gli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose. Seguire [questa](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) procedura per esaminare e modificare le impostazioni.

* *Il ripristino tra aree* consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata di Azure. Questa opzione consente di eseguire esercitazioni per soddisfare i requisiti di controllo o conformità e ripristinare la macchina virtuale o il relativo disco in caso di emergenza nell'area primaria. CRR è una funzionalità di consenso esplicito per qualsiasi insieme di credenziali GRS. Fare clic [qui](backup-create-rs-vault.md#set-cross-region-restore) per altre informazioni.

* Prima di finalizzare la progettazione dell'insieme di credenziali, esaminare le matrici di [supporto](backup-support-matrix.md#vault-support) dell'insieme di credenziali per comprendere i fattori che potrebbero influire o limitare le scelte di progettazione.

## <a name="backup-policy-considerations"></a>Considerazioni sui criteri di backup

Backup di Azure criteri include due componenti: *Pianificazione* (quando eseguire il backup) e Conservazione *(per* quanto tempo conservare il backup). È possibile definire i criteri in base al tipo di dati di cui viene eseguito il backup, ai requisiti RTO/RPO, alle esigenze di conformità operative o normative e al tipo di carico di lavoro (ad esempio, macchina virtuale, database, file). Fare clic [qui](backup-architecture.md#backup-policy-essentials) per altre informazioni.

Quando si creano criteri di backup, tenere presenti le linee guida seguenti:

### <a name="schedule-considerations"></a>Considerazioni sulla pianificazione

* È consigliabile raggruppare le macchine virtuali che richiedono le stesse impostazioni di ora di inizio, frequenza e conservazione della pianificazione all'interno di un singolo criterio.

* Assicurarsi che l'ora di inizio pianificata del backup sia durante l'ora dell'applicazione di produzione non di picco.

* Per distribuire il traffico di backup, è consigliabile eseguire il backup di macchine virtuali diverse in orari diversi del giorno e assicurarsi che gli orari non si sovrappongano.

### <a name="retention-considerations"></a>Considerazioni sulla conservazione

* La conservazione a breve termine può essere "minuti" o "giornalieri". La conservazione per i punti di backup "settimanale", "mensile" o "annuale" viene definita conservazione a lungo termine.

* Conservazione a lungo termine:
  * Pianificato (requisiti di conformità): se si sa in anticipo che i dati sono necessari anni a partire dall'ora corrente, usare la conservazione a lungo termine.
  * Non pianificato (requisito su richiesta): se non si conosce in anticipo, è possibile usare su richiesta con impostazioni di conservazione personalizzate specifiche (queste impostazioni di conservazione personalizzate non sono influenzate dalle impostazioni dei criteri).

* Backup su richiesta con conservazione personalizzata: se è necessario eseguire un backup non pianificato tramite criteri di backup, è possibile usare un backup su richiesta. Può essere utile per eseguire backup che non rientrano nel backup pianificato o per eseguire backup granulari (ad esempio, più backup di macchine virtuali IaaS al giorno, perché il backup pianificato consente un solo backup al giorno). È importante notare che i criteri di conservazione definiti nei criteri pianificati non si applicano ai backup su richiesta.

### <a name="optimize-backup-policy"></a>Ottimizzare i criteri di backup

* Quando i requisiti aziendali cambiano, potrebbe essere necessario estendere o ridurre la durata della conservazione. In questo caso, è possibile prevedere quanto segue:  
  * Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
  * Se la conservazione viene ridotta, i punti di ripristino vengono contrassegnati per l'eliminazione nel processo di pulizia successivo e successivamente eliminati.
  * Le regole di conservazione più recenti si applicano a tutti i punti di conservazione (esclusi i punti di conservazione su richiesta). Pertanto, se il periodo di conservazione viene esteso (ad esempio fino a 100 giorni), quando viene eseguito il backup, seguito dalla riduzione della conservazione (ad esempio da 100 a sette giorni), tutti i dati di backup verranno mantenuti in base all'ultimo periodo di conservazione specificato (ovvero, 7 giorni).

* Backup di Azure offre la flessibilità necessaria per interrompere la *protezione e la gestione dei backup:*
  * *Arrestare la protezione e conservare i dati di backup*. Se si ritira o si sta ritirando l'origine dati (macchina virtuale, applicazione), ma è necessario conservare i dati a scopo di controllo o conformità, è possibile usare questa opzione per impedire a tutti i processi di backup futuri di proteggere l'origine dati e conservare i punti di ripristino di cui è stato eseguito il backup. È quindi possibile ripristinare o riprendere la protezione delle macchine virtuali.
  * *Arrestare la protezione ed eliminare i dati di backup*. Questa opzione impedirà a tutti i processi di backup futuri di proteggere la macchina virtuale ed eliminerà tutti i punti di ripristino. Non sarà possibile ripristinare la macchina virtuale né usare l'opzione Riprendi backup.

  * Se si riprende la protezione (di un'origine dati arrestata con conservazione dei dati), verranno applicate le regole di conservazione. Eventuali punti di ripristino scaduti verranno rimossi (all'ora pianificata).

* Prima di completare la progettazione dei criteri, è importante tenere presenti i fattori seguenti che potrebbero influenzare le scelte di progettazione.
  * L'ambito di un criterio di backup è un insieme di credenziali.
  * È previsto un limite al numero di elementi per criterio , ad esempio 100 macchine virtuali. Per ridimensionare, è possibile creare criteri duplicati con le stesse pianificazioni o con pianificazioni diverse.
  * Non è possibile eliminare in modo selettivo punti di ripristino specifici.
  * Non è possibile disabilitare completamente il backup pianificato e mantenere l'origine dati in uno stato protetto. Il backup meno frequente che è possibile configurare con i criteri è avere un backup pianificato settimanale. Un'alternativa consiste nell'arrestare la protezione con mantieni i dati e abilitare la protezione ogni volta che si vuole eseguire un backup, eseguire un backup su richiesta e quindi disattivare la protezione mantenendo i dati di backup. Fare clic [qui](backup-azure-manage-vms.md#stop-protecting-a-vm) per altre informazioni.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Per proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda, Backup di Azure fornisce garanzie di riservatezza, integrità e disponibilità da attacchi intenzionali e abusi dei dati e dei sistemi preziosi. Prendere in considerazione le linee guida di sicurezza seguenti per la Backup di Azure soluzione:

### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

* Il controllo degli accessi in base al ruolo di Azure consente una gestione granulare degli accessi, la separazione dei compiti all'interno del team e la concessione solo della quantità di accesso agli utenti necessaria per eseguire i propri processi. Fare clic [qui](backup-rbac-rs-vault.md) per altre informazioni.

* Backup di Azure fornisce tre ruoli predefiniti per controllare le operazioni di gestione dei backup: collaboratori, operatori e lettori di backup. Fare clic [qui](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) per altre informazioni.

* Backup di Azure ha diversi controlli di sicurezza incorporati nel servizio per impedire, rilevare e rispondere alle vulnerabilità di sicurezza (altre informazioni)

* Gli account di archiviazione usati dagli insiemi di credenziali di Servizi di ripristino sono isolati e non sono accessibili agli utenti per scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di Backup di Azure, ad esempio il ripristino.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Crittografia dei dati in transito e inattivi

La crittografia protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione.

* All'interno di Azure i dati in transito tra Archiviazione di Azure e l'insieme di credenziali sono protetti con HTTPS. Questi dati rimangono all'interno della rete di Azure.

* I dati di backup vengono crittografati automaticamente usando chiavi gestite da Microsoft. In alternativa, è possibile usare chiavi proprie, note anche come [chiavi gestite dal cliente.](encryption-at-rest-with-cmk.md)

* Backup di Azure supporta il backup e il ripristino delle VM di Azure i cui dischi del sistema operativo e dei dati sono crittografati con Crittografia dischi di Azure. Fare clic [qui](backup-azure-vms-encryption.md) per altre informazioni.

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protezione dei dati di backup da eliminazioni accidentali

Backup di Azure offre funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione software, se un utente elimina il backup (di una macchina virtuale, un database SQL Server, una condivisione file di Azure e un database SAP HANA), i dati di backup vengono conservati per altri 14 giorni, consentendo il ripristino dell'elemento di backup senza perdita di dati. La conservazione aggiuntiva di 14 giorni dei dati di backup nello stato "eliminazione software" non comporta alcun costo per l'utente. Fare clic [qui](backup-azure-security-feature-cloud.md) per altre informazioni.

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoraggio e avvisi di attività sospette

Backup di Azure offre funzionalità di monitoraggio e avviso predefinite per visualizzare e configurare le azioni per gli eventi correlati a Backup di Azure. Fare clic [qui](security-overview.md#monitoring-and-alerts-of-suspicious-activity) per altre informazioni.

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funzionalità di sicurezza per la protezione dei backup ibridi

Il servizio Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS, Microsoft Azure Recovery Services) per eseguire il backup e il ripristino di file, cartelle e dello stato del volume o del sistema da un computer locale ad Azure. MARS offre ora funzionalità di sicurezza: una passphrase da crittografare prima del caricamento e decrittografare dopo il download da Backup di Azure, i dati di backup eliminati vengono conservati per altri 14 giorni dalla data di eliminazione e le operazioni critiche (ad esempio. la modifica di una passphrase può essere eseguita solo da utenti con credenziali di Azure valide. Fare clic [qui](backup-azure-security-feature.md) per altre informazioni.

## <a name="network-considerations"></a>Considerazioni per la rete

Backup di Azure richiede lo spostamento dei dati dal carico di lavoro all'insieme di credenziali di Servizi di ripristino. Backup di Azure offre diverse funzionalità per proteggere i dati di backup dall'esposizione accidentale (ad esempio un attacco man-in-the-middle sulla rete). Considerare le linee guida seguenti:

### <a name="internet-connectivity"></a>Connettività Internet

* *Backup di macchine* virtuali di Azure: tutte le comunicazioni e il trasferimento dei dati necessari tra l'archiviazione e il servizio Backup di Azure si verificano all'interno della rete di Azure senza dover accedere alla rete virtuale. Pertanto, il backup delle macchine virtuali di Azure inserite in reti protette non richiede l'accesso ad alcun IP o FQDN.

* *SAP HANA database nella* macchina virtuale di Azure, SQL Server database nella macchina virtuale di Azure: richiede connettività al servizio Backup di Azure, Archiviazione di Azure e Azure Active Directory. Questa operazione può essere eseguita usando endpoint privati oppure consentendo l'accesso agli indirizzi IP pubblici o FQDN richiesti. Non consentire una connettività corretta ai servizi di Azure necessari può causare errori nelle operazioni quali l'individuazione del database, la configurazione del backup, l'esecuzione di backup e il ripristino dei dati. Per indicazioni complete sulla rete durante l'uso di tag del gruppo di sicurezza di rete, firewall di Azure e proxy HTTP, vedere questi articoli su [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) sicurezza.

* *Ibrido:* l'agente MARS (Microsoft Azure Recovery Services) richiede l'accesso alla rete per tutte le operazioni critiche: installazione, configurazione, backup e ripristino. L'agente MARS può connettersi al servizio Backup di Azure tramite [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) usando il peering pubblico (disponibile per i circuiti meno comuni) e il peering Microsoft, usando [endpoint](install-mars-agent.md#private-endpoints) privati o [tramite proxy/firewall](install-mars-agent.md#verify-internet-access)con i controlli di accesso appropriati.

### <a name="private-endpoints-for-azure-backup"></a>Endpoint privati per Backup di Azure

Endpoint [privato di](../private-link/private-endpoint-overview.md) Azure è un'interfaccia di rete che connette l'utente in modo privato e sicuro a un servizio basato collegamento privato di Azure. Backup di Azure consente di eseguire in modo sicuro il backup e il ripristino dei dati dagli insiemi di credenziali di Servizi di ripristino usando endpoint privati.

* Quando si abilitano gli endpoint privati per l'insieme di credenziali, vengono usati solo per il backup e il ripristino dei carichi di lavoro SQL e SAP HANA in una macchina virtuale di Azure e nei backup dell'agente MARS.  È possibile usare l'insieme di credenziali anche per il backup di altri carichi di lavoro (tuttavia non richiederanno endpoint privati). Oltre al backup di carichi di lavoro SQL e SAP HANA e backup tramite l'agente MARS, gli endpoint privati vengono usati anche per eseguire il ripristino dei file nel caso del backup di macchine virtuali di Azure. Fare clic [qui](private-endpoints.md#recommended-and-supported-scenarios) per altre informazioni.

* Azure Active Directory attualmente non supporta endpoint privati. Pertanto, agli indirizzi IP e agli FQDN necessari per Azure Active Directory dovrà essere consentito l'accesso in uscita dalla rete protetta quando si esegue il backup dei database nelle macchine virtuali di Azure e il backup usando l'agente MARS. È anche possibile usare tag NSG e Firewall di Azure per consentire l'accesso Azure AD, se applicabile. Per altre informazioni sui [prerequisiti, vedere](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Considerazioni sulla governance

La governance in Azure viene implementata principalmente [con Criteri di Azure](../governance/policy/overview.md) e [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md). [Criteri di Azure](../governance/policy/overview.md) consente di creare, assegnare e gestire le definizioni dei criteri per applicare le regole alle risorse. Questa funzionalità consente di mantenere tali risorse conformi agli standard dell'azienda. [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md) consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider di servizi cloud. Inoltre, gli strumenti seguenti, ad esempio [il calcolatore dei](https://azure.microsoft.com/pricing/calculator/) prezzi di Azure [Azure Advisor](../advisor/advisor-overview.md)  svolgono un ruolo importante nel processo di gestione dei costi.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Backup di Azure supportano due scenari chiave tramite la funzionalità Criteri di Azure

* Assicurarsi che i computer business critical appena creati siano sottoposti automaticamente a backup con le impostazioni di conservazione giuste. Backup di Azure fornisce un criterio predefinito (tramite Criteri di Azure) che può essere assegnato a tutte le macchine virtuali di Azure in una posizione specificata all'interno di una sottoscrizione o di un gruppo di risorse. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono configurate automaticamente per il backup in un insieme di credenziali esistente nella stessa località e sottoscrizione. L'utente può specificare l'insieme di credenziali e i criteri di conservazione a cui devono essere associate le macchine virtuali di cui è stato eseguito il backup. Fare clic [qui](backup-azure-auto-enable-backup.md) per altre informazioni.

* Assicurarsi che per gli insiemi di credenziali appena creati sia abilitata la diagnostica per supportare i report. Spesso l'aggiunta manuale di un'impostazione di diagnostica per ogni insieme di credenziali può essere un'attività complicata. Inoltre, per qualsiasi nuovo insieme di credenziali creato devono essere abilitate le impostazioni di diagnostica per poter visualizzare i report per questo insieme di credenziali. Per semplificare la creazione di impostazioni di diagnostica su larga scala (con Log Analytics come destinazione), Backup di Azure offre una funzionalità Criteri di Azure. Questo criterio aggiunge un'impostazione di diagnostica latenza a tutti gli insiemi di credenziali in ogni sottoscrizione o gruppo di risorse. Le sezioni seguenti forniscono istruzioni su come usare questo criterio. Fare clic [qui](azure-policy-configure-diagnostics.md) per altre informazioni.

### <a name="azure-backup-cost-considerations"></a>Backup di Azure sui costi

Le Backup di Azure del servizio di continuità aziendale offrono la flessibilità necessaria per gestire in modo efficace i costi e soddisfare comunque i requisiti aziendali di continuità aziendale e ripristino di emergenza. Considerare le linee guida seguenti:

* Usare il calcolatore dei prezzi per valutare e ottimizzare i costi modificando le varie leve. [Per altre informazioni, vedi qui](azure-backup-pricing.md)

* Explorer di Backup: usare Explorer di Backup o report di Backup per comprendere e ottimizzare la crescita dell'archiviazione dei backup, arrestando i backup per carichi di lavoro non critici o macchine virtuali eliminate. È possibile ottenere una visualizzazione aggregata dell'intero complesso dal punto di vista del backup. Sono incluse non solo le informazioni sugli elementi di backup, ma anche le risorse che non sono configurate per il backup. Ciò garantisce che non si perdi mai la protezione dei dati critici nell'ambiente in crescita e che i backup siano ottimizzati per carichi di lavoro non critici o carichi di lavoro eliminati.

* Ottimizzare i criteri di backup
  * Ottimizzare le impostazioni di pianificazione e conservazione in base ad archetipi di carico di lavoro(ad esempio, cruciali, non critici)
  * Ottimizzare le impostazioni di conservazione per il ripristino istantaneo
  * Scegliere il tipo di backup corretto per soddisfare i requisiti, prendendo in considerazione i tipi di backup supportati (completo, incrementale, log, differenziale) dal carico Backup di Azure backup.

* Backup selettivo dei dischi: Escludi disco (funzionalità di anteprima) offre una scelta efficiente ed economica per eseguire il backup selettivo dei dati critici. Ad esempio, eseguire il backup di un solo disco quando non si vuole eseguire il backup degli altri dischi collegati a una macchina virtuale. Ciò è utile anche in caso di più soluzioni di backup. Ad esempio, quando si esegue il backup dei database o dei dati con una soluzione di backup del carico di lavoro (database SQL Server nel backup di macchine virtuali di Azure) e si vuole usare il backup a livello di macchina virtuale di Azure per i dischi selezionati.

* Backup di Azure snapshot delle macchine virtuali di Azure e le archivia insieme ai dischi per migliorare la creazione del punto di ripristino e velocizzare le operazioni di ripristino. Questa operazione viene definita ripristino istantaneo. Per impostazione predefinita, gli snapshot di ripristino istantaneo vengono conservati per due giorni. Questa funzionalità consente un'operazione di ripristino da questi snapshot riducendo i tempi di ripristino. Riduce il tempo necessario per trasformare e copiare i dati dall'insieme di credenziali. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo. Fare clic [qui](backup-instant-restore-capability.md#configure-snapshot-retention) per altre informazioni.

* Backup di Azure tipo di replica di archiviazione dell'insieme di credenziali per impostazione predefinita è impostato su Ridondanza geografica. Questa opzione non può essere modificata dopo la protezione degli elementi. L'archiviazione con ridondanza geografica offre un livello più elevato di durabilità dei dati rispetto all'archiviazione con ridondanza locale, consente a un consenso esplicito di usare il ripristino tra aree e costa di più. Esaminare i compromessi tra costi inferiori e durabilità dei dati più elevata e decidere qual è la soluzione migliore per lo scenario. [Per altre informazioni, vedi qui](backup-create-rs-vault.md#set-storage-redundancy)

* Se si protegge sia il carico di lavoro in esecuzione all'interno di una macchina virtuale che la macchina virtuale stessa, verificare se è necessaria questa doppia protezione.

## <a name="monitoring-and-alerting-considerations"></a>Considerazioni sul monitoraggio e l'avviso

Un utente o un amministratore di backup dovrebbe essere in grado di monitorare tutte le soluzioni di backup e ricevere notifiche in scenari importanti. Questa sezione contiene informazioni dettagliate sulle funzionalità di monitoraggio e notifica fornite dal Backup di Azure servizio.

### <a name="monitoring"></a>Monitoraggio

* Backup di Azure il **monitoraggio dei processi** incorporato per operazioni quali la configurazione di backup, backup, ripristino, eliminazione di backup e così via. L'ambito è l'insieme di credenziali ed è ideale per il monitoraggio di un singolo insieme di credenziali. Fare clic [qui](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) per altre informazioni.

* Se è necessario monitorare le attività operative su larga scala, **Explorer di Backup** offre una visualizzazione aggregata dell'intero set di backup, abilitando l'analisi dettagliata del drill-down e la risoluzione dei problemi. Si tratta di una cartella di lavoro Monitoraggio di Azure predefinita che offre un'unica posizione centrale che consente di monitorare le attività operative nell'intero ambiente di backup in Azure, che si estende su tenant, località, sottoscrizioni, gruppi di risorse e insiemi di credenziali. Fare clic [qui](monitor-azure-backup-with-backup-explorer.md) per altre informazioni.
  * Usarlo per identificare le risorse non configurate per il backup e assicurarsi di non perdere mai la protezione dei dati critici nel settore in crescita.
  * Il dashboard fornisce attività operative per gli ultimi sette giorni (massimo). Se è necessario conservare questi dati, è possibile esportarli come file di Excel e conservarli.
  * Se si è un utente Azure Lighthouse, è possibile visualizzare le informazioni tra più tenant, abilitando il monitoraggio senza limiti.

* Se è necessario conservare e visualizzare le attività operative a lungo termine, usare **Report**. Un requisito comune per gli amministratori di backup è ottenere informazioni dettagliate sui backup in base ai dati che si estendono per un lungo periodo di tempo. I casi d'uso per una soluzione di questo tipo includono:
  * Allocazione e previsione dello spazio di archiviazione cloud utilizzato.
  * Controllo delle attività dii backup e ripristino.
  * Identificazione delle tendenze principali a diversi livelli di granularità.

* Inoltre,
  * È possibile inviare dati,ad esempio processi, criteri e così via, all'area **di lavoro Log Analytics.** In questo modo le funzionalità dei log di Monitoraggio di Azure consentiranno la correlazione dei dati con altri dati di monitoraggio raccolti da Monitoraggio di Azure, consolideranno le voci di log di più sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi, useranno query di log per eseguire analisi complesse e ottenere informazioni approfondite sulle voci di log. Fare clic [qui](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace) per altre informazioni.
  * È possibile inviare dati all'hub eventi per inviare voci all'esterno di Azure, ad esempio a un sistema SIEM di terze parti (Security Information and Event Management) o a un'altra soluzione di analisi dei log. Fare clic [qui](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs) per altre informazioni.
  * È possibile inviare dati a un account Archiviazione di Azure se si desidera conservare i dati di log per più di 90 giorni per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni o meno, non è necessario configurare gli archivi in un account di archiviazione, perché gli eventi del log attività vengono mantenuti nella piattaforma Azure per 90 giorni. [Altre informazioni](../azure-monitor/essentials/activity-log.md#send-to--azure-storage)

### <a name="alerting"></a>Creazione di avvisi

* Gli avvisi sono principalmente un modo per ricevere una notifica per intraprendere un'azione pertinente. La sezione Avvisi di backup mostra gli avvisi generati dal Backup di Azure servizio.

* Backup di Azure fornisce un **meccanismo di notifica** degli avvisi predefinito tramite posta elettronica per errori, avvisi e operazioni critiche. È possibile specificare singoli indirizzi di posta elettronica o liste di distribuzione per ricevere una notifica quando viene generato un avviso. È anche possibile scegliere se ricevere una notifica per ogni singolo avviso o raggrupparli in un digest orario e quindi ricevere una notifica.
  * Questi avvisi sono definiti dal servizio e forniscono supporto per scenari limitati, ad esempio errori di backup/ripristino, arresto della protezione con conservazione dei dati/arresto della protezione con eliminazione dei dati e così via. Fare clic [qui](backup-azure-monitoring-built-in-monitor.md#alert-scenarios) per altre informazioni.
  * Se viene eseguita un'operazione distruttiva, ad esempio l'arresto della protezione con l'eliminazione dei dati, viene  generato un avviso e viene inviato un messaggio di posta elettronica a proprietari, amministratori e co-amministratori della sottoscrizione anche se le notifiche non sono configurate per l'insieme di credenziali di Servizi di ripristino.
  * Alcuni carichi di lavoro possono generare un'elevata frequenza di errori( ad esempio, SQL Server ogni 15 minuti). Per evitare di sovraccaricare gli avvisi generati per ogni occorrenza di errore, gli avvisi vengono consolidati. Fare clic [qui](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts) per altre informazioni.
  * Gli avvisi predefiniti non possono essere personalizzati e sono limitati ai messaggi di posta elettronica definiti nel portale di Azure.

* Se è necessario creare **avvisi personalizzati,** ad esempio avvisi di processi riusciti, usare Log Analytics. In Monitoraggio di Azure è possibile creare avvisi personalizzati in un'area di lavoro Log Analytics. I carichi di lavoro ibridi (DPM/MABS) possono anche inviare dati a LA e usare LA per fornire avvisi comuni tra i carichi di lavoro supportati da Backup di Azure.

* È anche possibile ricevere notifiche tramite i log attività predefiniti dell'insieme di credenziali di Servizi **di ripristino.** Supporta tuttavia scenari limitati e non è adatto per operazioni come il backup pianificato, che si allinea meglio con i log delle risorse rispetto ai log attività. Per altre informazioni su queste limitazioni e su come usare l'area di lavoro Log Analytics per il monitoraggio e la creazione di avvisi su larga scala per tutti i carichi di lavoro protetti da Backup di Azure, vedere questo [articolo.](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)

## <a name="next-steps"></a>Passaggi successivi

È consigliabile leggere gli articoli seguenti come punti di partenza per l'uso di Backup di Azure:

* [Backup di Azure panoramica](backup-overview.md)
* [Domande frequenti](backup-azure-backup-faq.yml)
