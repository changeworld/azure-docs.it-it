---
title: 'SQL Server al database SQL di Azure: Panoramica della migrazione'
description: Informazioni sugli strumenti e sulle opzioni disponibili per eseguire la migrazione dei database di SQL Server al database SQL di Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065175"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Panoramica della migrazione: SQL Server al database SQL di Azure
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Informazioni sulle opzioni e considerazioni per la migrazione dei database di SQL Server al database SQL di Azure. 

È possibile eseguire la migrazione di SQL Server database in esecuzione in locale o in: 

- SQL Server in macchine virtuali di Azure.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2).
- Servizio database relazionale AWS (RDS).
- Motore di calcolo in Google Cloud Platform (GCP).  
- SQL cloud per SQL Server in GCP. 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Panoramica

Il [database SQL di Azure](../../database/sql-database-paas-overview.md) è un'opzione di destinazione consigliata per carichi di lavoro SQL Server che richiedono una piattaforma distribuita come servizio (PaaS) completamente gestita. Il database SQL gestisce la maggior parte delle funzioni di gestione del database. Include inoltre funzionalità predefinite di disponibilità elevata, elaborazione di query intelligenti, scalabilità e prestazioni per soddisfare molti tipi di applicazioni. 

Il database SQL offre flessibilità con più [modelli di distribuzione](../../database/sql-database-paas-overview.md#deployment-models) e livelli di [servizio](../../database/service-tiers-vcore.md#service-tiers) che soddisfano i diversi tipi di applicazioni o carichi di lavoro.

Uno dei principali vantaggi della migrazione al database SQL è che è possibile modernizzare l'applicazione usando le funzionalità PaaS. È quindi possibile eliminare qualsiasi dipendenza dai componenti tecnici che hanno come ambito a livello di istanza, ad esempio i processi di SQL Agent.

È anche possibile ridurre i costi usando il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per SQL Server eseguire la migrazione delle licenze locali SQL Server al database SQL di Azure. Questa opzione è disponibile se si sceglie il [modello di acquisto basato su vCore](../../database/service-tiers-vcore.md).

## <a name="considerations"></a>Considerazioni 

I fattori chiave da considerare quando si valutano le opzioni di migrazione sono: 

- Numero di server e database
- Dimensioni dei database
- Tempo di inattività aziendale accettabile durante il processo di migrazione 

Le opzioni di migrazione elencate in questa guida prendono in considerazione questi fattori. Per la migrazione dei dati logici al database SQL di Azure, il tempo di migrazione può dipendere dal numero di oggetti in un database e dalle dimensioni del database. 

Sono disponibili strumenti per diversi carichi di lavoro e preferenze utente. Alcuni strumenti possono essere utilizzati per eseguire una rapida migrazione di un singolo database tramite uno strumento basato su interfaccia utente. Altri strumenti consentono di automatizzare la migrazione di più database per gestire le migrazioni su larga scala. 

## <a name="choose-an-appropriate-target"></a>Scegliere una destinazione appropriata

Prendere in considerazione le linee guida generali che consentono di scegliere il modello di distribuzione e il livello di servizio appropriati per il database SQL di Azure. È possibile scegliere risorse di calcolo e di archiviazione durante la distribuzione e quindi [modificarle successivamente usando il portale di Azure](../../database/scale-resources.md) senza incorrere in tempi di inattività per l'applicazione.

**Modelli di distribuzione**: comprendere il carico di lavoro dell'applicazione e il modello di utilizzo per scegliere tra un singolo database o un pool elastico. 

- Un [database singolo](../../database/single-database-overview.md) rappresenta un database completamente gestito adatto per la maggior parte dei microservizi e delle applicazioni cloud moderne.
- Un [pool elastico](../../database/elastic-pool-overview.md) è una raccolta di database singoli con un set condiviso di risorse, ad esempio CPU o memoria. È adatto per combinare i database in un pool con modelli di utilizzo prevedibili che possono condividere in modo efficace lo stesso set di risorse.

**Modelli di acquisto**: scegliere tra vCore, unità di transazione di database (DTU) o modelli di acquisto senza server. 

- Il [modello vCore](../../database/service-tiers-vcore.md) consente di scegliere il numero di Vcore per il database SQL di Azure, quindi è la scelta più semplice quando si esegue la conversione da SQL Server locali. Questa è l'unica opzione che supporta il salvataggio dei costi delle licenze con la [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- Il [modello DTU](../../database/service-tiers-dtu.md) astrae le risorse di calcolo, memoria e i/O sottostanti per fornire un DTU misto. 
- Il [modello senza server](../../database/serverless-tier-overview.md) è per i carichi di lavoro che richiedono il ridimensionamento automatico su richiesta con risorse di calcolo fatturate al secondo. Il livello di calcolo senza Server sospende automaticamente i database durante i periodi di inattività (in cui vengono fatturate solo le risorse di archiviazione). Riprende automaticamente i database quando l'attività restituisce. 

**Livelli di servizio**: scegliere tra tre livelli di servizio progettati per diversi tipi di applicazioni.

- Il [livello di servizio per utilizzo generico/standard](../../database/service-tier-general-purpose.md) offre un'opzione di calcolo e di archiviazione bilanciata, ideale per la distribuzione di applicazioni in livelli intermedi e inferiori. La ridondanza è incorporata nel livello di archiviazione per il recupero dagli errori. È progettato per la maggior parte dei carichi di lavoro del database. 
- Il [livello di servizio business critical/Premium](../../database/service-tier-business-critical.md) è per le applicazioni ad alto livello che richiedono tassi di transazione elevati, I/O a bassa latenza e un elevato livello di resilienza. Le repliche secondarie sono disponibili per il failover e per l'offload dei carichi di lavoro di lettura.
- Il [livello di servizio iperscalabile](../../database/service-tier-hyperscale.md) è per i database con volumi di dati crescenti ed è necessario scalare automaticamente fino a 100 TB nelle dimensioni del database. È progettato per database di dimensioni molto grandi. 

> [!IMPORTANT]
> [La velocità del log delle transazioni è regolata](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) nel database SQL di Azure per limitare le velocità di inserimento elevate. Di conseguenza, durante la migrazione potrebbe essere necessario scalare le risorse del database di destinazione (VCore o DTU) per semplificare la pressione sulla CPU o sulla velocità effettiva. Scegliere il database di destinazione di dimensioni appropriate, ma pianificare la scalabilità delle risorse per la migrazione, se necessario. 


### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

L'azienda potrebbe avere requisiti che rendono [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) una destinazione più appropriata rispetto al database SQL di Azure. 

Se si applica una delle condizioni seguenti alla propria azienda, provare a eseguire il passaggio a una macchina virtuale SQL Server (VM): 

- È necessario l'accesso diretto al sistema operativo o file system, ad esempio per installare agenti di terze parti o personalizzati nella stessa macchina virtuale con SQL Server. 
- Si dispone di una dipendenza rigorosa da funzionalità che non sono ancora supportate, ad esempio FileStream/FileTable, polibase e transazioni tra istanze. 
- È necessario rimanere a una versione specifica di SQL Server (ad esempio, 2012). 
- I requisiti di calcolo sono molto inferiori a quelle offerte dall'istanza gestita (ad esempio, una vCore) e il consolidamento dei database non è un'opzione accettabile. 


## <a name="migration-tools"></a>Strumenti di migrazione 

È consigliabile usare gli strumenti di migrazione seguenti: 

|Tecnologia | Descrizione|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Questo servizio di Azure ti aiuta a individuare e valutare le tue proprietà SQL Data su larga scala in VMware. Fornisce indicazioni per la distribuzione di Azure SQL, le dimensioni di destinazione e le stime mensili. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|Questo strumento desktop di Microsoft fornisce valutazioni senza problemi delle migrazioni di SQL Server e di database singolo al database SQL di Azure (sia dello schema che dei dati). </br></br>Lo strumento può essere installato in un server locale o nel computer locale con connettività ai database di origine. Il processo di migrazione è uno spostamento di dati logici tra oggetti nei database di origine e di destinazione.|
|[Servizio Migrazione del database di Azure](../../../dms/tutorial-sql-server-to-azure-sql.md)|Questo servizio di Azure può eseguire la migrazione di database di SQL Server al database SQL di Azure tramite la portale di Azure o automaticamente tramite PowerShell. Il servizio migrazione del database richiede la selezione di una rete virtuale di Azure preferita durante il provisioning per garantire la connettività ai database di origine SQL Server. È possibile eseguire la migrazione di singoli database o su larga scala. |
| | |


La tabella seguente elenca gli strumenti di migrazione alternativi: 

|Tecnologia |Descrizione  |
|---------|---------|
|[Replica transazionale](../../database/replication-to-sql-database.md)|Replicare i dati dalle tabelle di database di origine SQL Server al database SQL di Azure fornendo un'opzione di migrazione del tipo di Sottoscrittore del server di pubblicazione mantenendo la coerenza transazionale. Le modifiche incrementali dei dati vengono propagate ai Sottoscrittori non appena si verificano nei Publisher.|
|[Import Export Service/BACPAC](../../database/database-import.md)|[BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) è un file di Windows con estensione. bacpac che incapsula lo schema e i dati di un database. È possibile usare BACPAC per esportare i dati da un'origine SQL Server e importare i dati nel database SQL di Azure. Un file BACPAC può essere importato in un nuovo database SQL tramite il portale di Azure. </br></br> Per la scalabilità e le prestazioni con database di grandi dimensioni o un numero elevato di database, è consigliabile utilizzare lo strumento da riga di comando [SqlPackage](../../database/database-import.md#using-sqlpackage) per esportare e importare i database.|
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|Lo [strumento di copia bulk (BCP)](/sql/tools/bcp-utility) copia i dati da un'istanza di SQL Server in un file di dati. Usare lo strumento per esportare i dati dall'origine e importare il file di dati nel database SQL di destinazione. </br></br> Per le operazioni di copia bulk ad alta velocità per spostare i dati nel database SQL di Azure, è possibile usare lo strumento per la [copia bulk intelligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) per ottimizzare la velocità di trasferimento sfruttando le attività di copia parallele.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|L' [attività di copia](../../../data-factory/copy-activity-overview.md) in Azure Data Factory esegue la migrazione dei dati dai database SQL Server di origine al database SQL di Azure usando connettori predefiniti e un [runtime di integrazione](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory supporta un'ampia gamma di [connettori](../../../data-factory/connector-overview.md) per spostare i dati dalle origini SQL Server al database SQL di Azure.|
|[Sincronizzazione dati SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|Sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati in modo bidirezionale tra più database, sia in locale che nel cloud.</br>La sincronizzazione dei dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database nel database SQL di Azure o in SQL Server.|
| | |

## <a name="compare-migration-options"></a>Confrontare le opzioni di migrazione

Confrontare le opzioni di migrazione per scegliere il percorso appropriato per le esigenze aziendali. 

Nella tabella seguente vengono confrontate le opzioni di migrazione consigliate: 

|Opzione di migrazione  |Utilizzo  |Considerazioni  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | -Migrare i database singoli (schema e dati).  </br> -Può supportare i tempi di inattività durante il processo di migrazione dei dati. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -L'attività di migrazione esegue lo spostamento dei dati tra oggetti di database (dall'origine alla destinazione), quindi è consigliabile eseguirla durante gli orari di minore attività. </br> -Data Migration Assistant segnala lo stato della migrazione per ogni oggetto di database, incluso il numero di righe di cui è stata eseguita la migrazione.  </br> -Per migrazioni di grandi dimensioni (numero di database o dimensioni di database), usare il servizio migrazione del database di Azure.|
|[Servizio Migrazione del database di Azure](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Migrare singoli database o su larga scala. </br> -Può supportare i tempi di inattività durante il processo di migrazione. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -Le migrazioni su larga scala possono essere automatizzate tramite [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Il tempo necessario per completare la migrazione dipende dalle dimensioni del database e dal numero di oggetti nel database. </br> -Richiede l'impostazione del database di origine come di sola lettura. |
| | | |

Nella tabella seguente vengono confrontate le opzioni di migrazione alternative: 

|Metodo o tecnologia |Utilizzo    |Considerazioni  |
|---------|---------|---------|
|[Replica transazionale](../../database/replication-to-sql-database.md)| -Esegue la migrazione pubblicando continuamente le modifiche dalle tabelle di database di origine alle tabelle del database SQL di destinazione. </br> -Esegue migrazioni di database complete o parziali di tabelle selezionate (subset di un database).  </br> </br> Origini supportate: </br> - [SQL Server (da 2016 a 2019) con alcune limitazioni](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP calcolo della macchina virtuale SQL Server  | -Il programma di installazione è relativamente complesso rispetto ad altre opzioni di migrazione.   </br> -Fornisce un'opzione di replica continua per eseguire la migrazione dei dati (senza portare i database offline).  </br> -La replica transazionale presenta alcune limitazioni da considerare quando si configura il server di pubblicazione nell'istanza di SQL Server di origine. Per altre informazioni, vedere [limitazioni relative alla pubblicazione di oggetti](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) . </br>-È possibile monitorare l' [attività di replica](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import Export Service/BACPAC](../../database/database-import.md)| -Migrare i singoli database delle applicazioni line-of-business. </br>-Adatto per database più piccoli.  </br>  -Non richiede uno strumento o un servizio di migrazione separato. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server  |  -Richiede tempi di inattività perché i dati devono essere esportati nell'origine e importati nella destinazione.   </br> -I formati di file e i tipi di dati utilizzati nell'esportazione o nell'importazione devono essere coerenti con gli schemi di tabella per evitare errori di troncamento o di mancata corrispondenza del tipo di dati.  </br> -Il tempo impiegato per esportare un database con un numero elevato di oggetti può essere significativamente superiore.       |
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Eseguire migrazioni di dati complete o parziali. </br> -Può contenere tempi di inattività. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server   | -Richiede tempi di inattività per l'esportazione dei dati dall'origine e l'importazione nella destinazione. </br> -I formati di file e i tipi di dati utilizzati nell'esportazione o nell'importazione devono essere coerenti con gli schemi di tabella. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Eseguire la migrazione e/o trasformare i dati dai database di origine SQL Server. </br> -L'Unione di dati da più origini di dati al database SQL di Azure è in genere per carichi di lavoro business intelligence (BI).  |  -Richiede la creazione di pipeline di spostamento dei dati in Data Factory per spostare i dati dall'origine alla destinazione.   </br> - Il [costo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) è una considerazione importante e si basa su fattori quali trigger della pipeline, esecuzioni di attività e durata dello spostamento dei dati. |
|[Sincronizzazione dati SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Sincronizzare i dati tra i database di origine e di destinazione.</br> -Adatto per eseguire la sincronizzazione continua tra il database SQL di Azure e SQL Server locali in un flusso bidirezionale. | -Il database SQL di Azure deve essere il database hub per la sincronizzazione con un database di SQL Server locale come database membro.</br> -Rispetto alla replica transazionale, sincronizzazione dati SQL supporta la sincronizzazione bidirezionale dei dati tra l'istanza locale e il database SQL di Azure. </br> -Può avere un maggiore effetto sulle prestazioni, a seconda del carico di lavoro.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilità delle funzionalità 

Quando si esegue la migrazione di carichi di lavoro basati su altre funzionalità di SQL Server, sono necessarie altre considerazioni.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Eseguire la migrazione di pacchetti di SQL Server Integration Services (SSIS) ad Azure ridistribuendo i pacchetti nel runtime di Azure-SSIS in [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [supporta la migrazione di pacchetti SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) fornendo un runtime compilato per l'esecuzione di pacchetti SSIS in Azure. In alternativa, è possibile riscrivere la logica SSIS ETL (Extract, Transform, Load) in modo nativo in Azure Data Factory usando [i flussi di dati](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Eseguire la migrazione dei report SQL Server Reporting Services (SSRS) a report impaginati in Power BI. Utilizzare lo [strumento di migrazione RDL](https://github.com/microsoft/RdlMigration) per preparare ed eseguire la migrazione dei report. Microsoft ha sviluppato questo strumento per aiutare i clienti a migrare i report Report Definition Language (RDL) dai server SSRS ai Power BI. È disponibile in GitHub e consente di seguire una procedura dettagliata completa dello scenario di migrazione. 

### <a name="high-availability"></a>Disponibilità elevata
L'installazione manuale di SQL Server funzionalità a disponibilità elevata come Always On istanze del cluster di failover e Gruppi di disponibilità Always On diventa obsoleta nel database SQL di destinazione. L'architettura a disponibilità elevata è già incorporata in entrambi i livelli di servizio [per utilizzo generico (modello di disponibilità standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [business critical (modello di disponibilità Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) per il database SQL di Azure. Il livello di servizio business critical/Premium fornisce anche la scalabilità in lettura che consente la connessione a uno dei nodi secondari per finalità di sola lettura. 

Oltre all'architettura a disponibilità elevata inclusa nel database SQL di Azure, la funzionalità [gruppi di failover automatico](../../database/auto-failover-group-overview.md) consente di gestire la replica e il failover dei database in un'istanza gestita in un'altra area. 

### <a name="sql-agent-jobs"></a>Processi di SQL Agent
I processi di SQL Agent non sono direttamente supportati nel database SQL di Azure e devono essere distribuiti ai [processi di database elastici (anteprima)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Account di accesso e gruppi
Spostare gli account di accesso SQL dall'origine SQL Server al database SQL di Azure usando il servizio migrazione del database in modalità offline. Utilizzare il riquadro **accessi selezionati** della migrazione guidata per eseguire la migrazione degli account di accesso al database SQL di destinazione. 

È inoltre possibile eseguire la migrazione di utenti e gruppi di Windows tramite il servizio migrazione del database abilitando l'interruttore corrispondente nella pagina di **configurazione** del servizio migrazione del database. 

In alternativa, è possibile usare l' [utilità PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) appositamente progettata da Microsoft Data Migration Architects. L'utilità USA PowerShell per creare uno script Transact-SQL (T-SQL) per ricreare gli account di accesso e selezionare gli utenti del database dall'origine alla destinazione. 

L'utilità PowerShell esegue automaticamente il mapping degli account di Windows Server Active Directory agli account Azure Active Directory (Azure AD) ed è in grado di eseguire una ricerca UPN per ogni account di accesso nell'istanza di Active Directory di origine. I ruoli del database e del server personalizzati degli script dell'utilità, insieme alle autorizzazioni utente e di appartenenza ai ruoli. I database indipendenti non sono ancora supportati e viene creato uno script solo un subset delle autorizzazioni di SQL Server possibili. 

### <a name="system-databases"></a>Database di sistema
Per il database SQL di Azure, gli unici database di sistema applicabili sono [Master](/sql/relational-databases/databases/master-database) e tempdb. Per altre informazioni, vedere [tempdb nel database SQL di Azure](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud nel database SQL. Ad esempio, non è necessario preoccuparsi di gestire i backup perché il servizio esegue questa operazione. È possibile eseguire il ripristino in qualsiasi [punto nel tempo entro il periodo di conservazione](../../database/recovery-using-backups.md#point-in-time-restore). 

Per rafforzare la sicurezza, è consigliabile usare [Azure ad autenticazione](../../database/authentication-aad-overview.md), il [controllo](../../database/auditing-overview.md), il [rilevamento delle minacce](../../database/azure-defender-for-sql.md), la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking).

Oltre alle funzionalità avanzate di gestione e sicurezza, il database SQL offre strumenti che consentono di [monitorare e ottimizzare il carico di lavoro](../../database/monitor-tune-overview.md). [Analisi SQL di Azure (anteprima)](../../../azure-monitor/insights/azure-sql.md) è una soluzione avanzata per il monitoraggio delle prestazioni di tutti i database nel database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Analisi SQL di Azure raccoglie e visualizza le metriche delle prestazioni chiave con l'intelligence predefinita per la risoluzione dei problemi relativi alle prestazioni.

[Ottimizzazione automatica](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   monitora continuamente le prestazioni del piano di esecuzione SQL e corregge automaticamente i problemi di prestazioni identificati. 


## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriori informazioni, vedere le risorse seguenti sviluppate per i progetti di migrazione nel mondo reale.

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento fornisce le piattaforme di destinazione consigliate, la preparazione per il cloud e un livello di monitoraggio e aggiornamento dell'applicazione/database per un carico di lavoro. Offre semplici calcoli con un solo clic e la generazione di report che consentono di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale automatico e uniforme per le piattaforme di destinazione.|
|[Utilità DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|È possibile usare DBLoader per caricare dati da file di testo delimitati in SQL Server. Questa utilità console di Windows utilizza l'interfaccia di caricamento bulk SQL Server Native Client. L'interfaccia funziona in tutte le versioni di SQL Server, insieme al database SQL di Azure.|
|[Creazione di database in blocco con PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|È possibile usare un set di tre script di PowerShell per creare un gruppo di risorse (create_rg.ps1), il [server logico in Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e un database SQL (create_sqldb.ps1). Gli script includono funzionalità di ciclo, in modo da poter scorrere e creare tutti i server e i database necessari.|
|[Distribuzione dello schema bulk con MSSQL-Scripter e PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Questo asset crea un gruppo di risorse, crea uno o più [server logici in Azure](../../database/logical-servers.md) per ospitare il database SQL di Azure, esporta ogni schema da un'istanza di SQL Server locale (o più istanze di SQL Server 2005 +) e importa gli schemi nel database SQL di Azure.|
|[Convertire SQL Server Agent processi in processi di database elastici](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Questo script esegue la migrazione dei processi di SQL Server Agent di origine ai processi di database elastici.|
|[Inviare messaggi di posta elettronica dal database SQL di Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Questa soluzione rappresenta un'alternativa alla funzionalità SendMail ed è disponibile per SQL Server locali. Usa funzioni di Azure e il servizio SendGrid per inviare messaggi di posta elettronica dal database SQL di Azure.|
|[Utilità per spostare gli account di accesso SQL Server locali nel database SQL di Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Uno script di PowerShell può creare uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database dall'SQL Server locale al database SQL di Azure. Lo strumento consente il mapping automatico degli account di Windows Server Active Directory agli account Azure AD, oltre alla migrazione facoltativa di SQL Server account di accesso nativi.|
|[Automazione della raccolta dati PerfMon tramite logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|È possibile utilizzare lo strumento logman per raccogliere dati PerfMon (per comprendere le prestazioni di base) e ottenere consigli sulla destinazione della migrazione. Questo strumento usa logman.exe per creare il comando per la creazione, l'avvio, l'arresto e l'eliminazione dei contatori delle prestazioni impostati in un'istanza di SQL Server remota.|
|[Migrazione del database al database SQL di Azure tramite BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Questo white paper fornisce istruzioni e procedure per accelerare le migrazioni dalle SQL Server al database SQL di Azure usando i file BACPAC.|

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a migrare i database di SQL Server al database SQL di Azure, vedere la [Guida alla migrazione da SQL Server a database SQL di Azure](sql-server-to-sql-database-guide.md).

- Per una matrice di servizi e strumenti che consentono di eseguire scenari di migrazione di database e dati, nonché attività speciali, vedere [Servizi e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul database SQL, vedere:
   - [Panoramica del database SQL di Azure](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Per informazioni dettagliate su come eseguire test A/B per il livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
