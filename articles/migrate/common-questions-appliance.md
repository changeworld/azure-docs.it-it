---
title: Domande frequenti sull'appliance Azure Migrate
description: Risposte alle domande più comuni sull'appliance Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563022"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni sull'appliance Azure Migrate. Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande su [individuazione, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Domande sulla [migrazione del server](common-questions-server-migration.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Che cos'è la Azure Migrate Appliance?

Il Azure Migrate Appliance è un dispositivo leggero che lo strumento Azure Migrate: individuazione e valutazione USA per individuare e valutare i server fisici o virtuali dall'ambiente locale o da qualsiasi cloud. Lo strumento di migrazione Azure Migrate: Server usa anche l'appliance per la migrazione senza agenti di server locali in esecuzione nell'ambiente VMware.

Di seguito sono riportate altre informazioni sull'appliance Azure Migrate:

- Il dispositivo viene distribuito in locale come server fisico o come server virtualizzato.
- L'appliance individua i server locali e invia continuamente i dati sulle prestazioni e i metadati del server a Azure Migrate.
- L'individuazione dell'appliance è senza agente. Nessun elemento viene installato nei server individuati.

[Altre](migrate-appliance.md) informazioni sull'appliance.

## <a name="how-can-i-deploy-the-appliance"></a>Come si distribuisce l'appliance?

L'appliance può essere distribuita usando due metodi:

- Il dispositivo può essere distribuito usando un modello per i server in esecuzione nell'ambiente VMware o Hyper-V ([modello OVA per VMware](how-to-set-up-appliance-vmware.md) o [VHD per Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se non si vuole usare un modello, è possibile distribuire l'appliance per l'ambiente VMware o Hyper-V usando uno [script del programma di installazione di PowerShell](deploy-appliance-script.md).
- In Azure per enti pubblici è necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](deploy-appliance-script-government.md).
- Per i server fisici o virtualizzati in locale o in qualsiasi altro cloud, è sempre necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>In che modo l'appliance si connette ad Azure?

L'appliance può connettersi tramite Internet o usando Azure ExpressRoute. 

- Assicurarsi che l'appliance possa connettersi a questi [URL di Azure](./migrate-appliance.md#url-access). 
- È possibile usare ExpressRoute con il peering Microsoft. Il peering pubblico è deprecato e non è disponibile per i nuovi circuiti ExpressRoute.
- Il peering privato non è supportato.


## <a name="does-appliance-analysis-affect-performance"></a>L'analisi degli appliance influisce sulle prestazioni?

L'appliance Azure Migrate profilare i server locali in modo continuo per misurare i dati sulle prestazioni. Questa profilatura non ha alcun effetto sulle prestazioni sui server profilati.

## <a name="can-i-harden-the-appliance"></a>È possibile rafforzare l'appliance?

Quando si usa il modello scaricato per creare l'appliance, è possibile aggiungere componenti (ad esempio, antivirus) al modello se si lasciano invariate le regole di comunicazione e del firewall necessarie per l'appliance Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Quale connettività di rete è necessaria?

Il dispositivo deve accedere agli URL di Azure. [Esaminare](migrate-appliance.md#url-access) l'elenco di URL.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Vedere gli articoli seguenti per informazioni sui dati raccolti dal Azure Migrate Appliance nei server:

- **Server in ambiente VMware**: [esaminare](migrate-appliance.md#collected-data---vmware) i dati raccolti.
- **Server nell'ambiente Hyper-V**: [esaminare](migrate-appliance.md#collected-data---hyper-v) i dati raccolti.
- **Server fisici o virtuali**:[esaminare](migrate-appliance.md#collected-data---physical) i dati raccolti.

## <a name="how-is-data-stored"></a>Come vengono archiviati i dati?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nella località di Azure in cui è stato creato il progetto.

Di seguito sono riportate altre informazioni sulla modalità di archiviazione dei dati:

- I dati raccolti vengono archiviati in modo sicuro in CosmosDB in una sottoscrizione Microsoft. I dati vengono eliminati quando si elimina il progetto. Lo spazio di archiviazione viene gestito da Azure Migrate. Non è possibile scegliere in modo specifico un account di archiviazione per i dati raccolti.
- Se si usa la [visualizzazione delle dipendenze](concepts-dependency-visualization.md), i dati raccolti vengono archiviati in un'area di lavoro di Azure log Analytics creata nella sottoscrizione di Azure. I dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quanti dati vengono caricati durante la profilatura continua?

Il volume dei dati inviati a Azure Migrate dipende da più parametri. Ad esempio, un progetto che dispone di 10 server (ognuno con un disco e una NIC) Invia circa 50 MB di dati al giorno. Questo valore è approssimativo. il valore effettivo varia a seconda del numero di punti dati per i dischi e le schede di rete. Se il numero di server, dischi o schede di rete aumenta, l'incremento dei dati inviati è non lineare.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>I dati sono crittografati inattivi e in transito?

Sì, per entrambi:

- I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS.
- I metadati vengono archiviati in un database di [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e nell' [archiviazione BLOB di Azure](../storage/common/storage-service-encryption.md) in una sottoscrizione Microsoft. I metadati vengono crittografati a riposo per l'archiviazione.
- I dati per l'analisi delle dipendenze sono inoltre crittografati in transito (tramite Secure HTTPS). Viene archiviato in un'area di lavoro Log Analytics nella sottoscrizione. I dati sono crittografati per l'analisi delle dipendenze.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>In che modo l'appliance si connette a server vCenter?

Questi passaggi descrivono il modo in cui l'appliance si connette a server VMware vCenter:

1. L'appliance si connette a server vCenter (porta 443) usando le credenziali specificate durante la configurazione dell'appliance.
2. L'appliance usa VMware PowerCLI per eseguire query server vCenter per raccogliere i metadati relativi ai server gestiti da server vCenter.
3. Il dispositivo raccoglie i dati di configurazione relativi ai server (core, memoria, dischi e schede di rete) e alla cronologia delle prestazioni di ogni server per l'ultimo mese.
4. I metadati raccolti vengono inviati al Azure Migrate: strumento di individuazione e valutazione (tramite Internet tramite HTTPS) per la valutazione.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Il dispositivo Azure Migrate può connettersi a più server vCenter?

No. Esiste un mapping uno-a-uno tra un [Azure migrate Appliance](migrate-appliance.md) e server vCenter. Per individuare i server in più istanze di server vCenter, è necessario distribuire più appliance.

## <a name="can-a-project-have-multiple-appliances"></a>Un progetto può avere più appliance?

Un progetto può avere più appliance registrate. Tuttavia, un dispositivo può essere registrato solo con un progetto.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Il dispositivo di Azure Migrate/appliance di replica può connettersi allo stesso vCenter?

Sì. È possibile aggiungere il dispositivo Azure Migrate (usato per la valutazione e la migrazione VMware senza agente) e l'appliance di replica (usato per la migrazione basata su agenti di server in esecuzione in VMware) allo stesso server vCenter. Assicurarsi però di non configurare entrambi i dispositivi nello stesso server e che non sono attualmente supportati.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quanti server è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 server in ambiente VMware, fino a 5.000 server nell'ambiente Hyper-V e fino a 1000 server fisici con una singola appliance. Se si dispone di più server nell'ambiente locale, vedere la pagina relativa alla [scalabilità di una valutazione di Hyper-V](scale-hyper-v-assessment.md), alla [scalabilità di una valutazione VMware](scale-vmware-assessment.md)e alla [scalabilità di una valutazione del server fisico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>È possibile eliminare un appliance?

Attualmente, l'eliminazione di un appliance dal progetto non è supportata.

L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il progetto associato all'appliance.

Tuttavia, l'eliminazione del gruppo di risorse Elimina anche altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri componenti di Azure nel gruppo di risorse associato al progetto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>È possibile usare l'appliance con una sottoscrizione o un progetto diverso?

Per usare il dispositivo con una sottoscrizione o un progetto diverso, è necessario riconfigurare l'appliance esistente eseguendo lo script del programma di installazione di PowerShell per lo scenario specifico (VMware/Hyper-V/fisico) nell'appliance. Tramite lo script vengono puliti i componenti e le impostazioni dell'appliance esistente per distribuire una nuova appliance. Assicurarsi di cancellare la cache del browser prima di iniziare a usare la gestione configurazione Appliance appena distribuita.

Non è inoltre possibile riutilizzare una chiave del progetto esistente in un'appliance riconfigurata. Assicurarsi di generare una nuova chiave dal progetto o dalla sottoscrizione desiderata per completare la registrazione dell'appliance.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>È possibile configurare l'appliance in una macchina virtuale di Azure?

No. Attualmente questa opzione non è supportata.

## <a name="can-i-discover-on-an-esxi-host"></a>È possibile individuare in un host ESXi?

No. Per individuare i server nell'ambiente VMware, è necessario avere server vCenter.

## <a name="how-do-i-update-the-appliance"></a>Ricerca per categorie aggiornare l'appliance?

Per impostazione predefinita, l'appliance e gli agenti installati vengono aggiornati automaticamente. Il dispositivo verifica la disponibilità di aggiornamenti ogni 24 ore. Gli aggiornamenti che non riescono vengono ripetuti. 

Solo gli agenti Appliance e appliance vengono aggiornati da questi aggiornamenti automatici. Il sistema operativo non viene aggiornato da Azure Migrate aggiornamenti automatici. Utilizzare gli aggiornamenti di Windows per rendere aggiornato il sistema operativo.

## <a name="can-i-check-agent-health"></a>È possibile controllare l'integrità dell'agente?

Sì. Nel portale passare alla pagina **integrità agente** per lo strumento di migrazione Azure migrate: Discovery and assessment o Azure migrate: Server. Qui è possibile controllare lo stato della connessione tra Azure e gli agenti di individuazione e valutazione nell'appliance.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>È possibile aggiungere più credenziali del server nell'appliance VMware?

Sì, sono ora supportate più credenziali server per eseguire l'inventario software (individuazione di applicazioni installate), l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database. [Altre informazioni](tutorial-discover-vmware.md#provide-server-credentials) su come fornire le credenziali in Gestione configurazione Appliance.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Quali tipi di credenziali del server è possibile aggiungere nell'appliance VMware?
È possibile specificare le credenziali di autenticazione Domain/Windows (non di dominio)/Linux (non di dominio)/SQL Server in Gestione configurazione Appliance. [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Quali tipi di proprietà di connessione SQL Server sono supportati da Azure Migrate per l'individuazione SQL?
Azure Migrate crittografa la comunicazione tra le istanze Azure Migrate Appliance e SQL Server di origine (con la proprietà Crittografa connessione impostata su TRUE). Queste connessioni vengono crittografate con [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (impostato su true); il livello trasporto userà SSL per crittografare il canale e ignorare la catena di certificati per convalidare l'attendibilità. Il server appliance deve essere configurato in modo da [considerare attendibile l'autorità radice del certificato](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Se al momento dell'avvio non è stato eseguito il provisioning di alcun certificato sul server, SQL Server genera un certificato autofirmato utilizzato per crittografare i pacchetti di accesso. [Altre informazioni](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)


## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).