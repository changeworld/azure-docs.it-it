---
title: 'SQL Server a SQL Istanza gestita: Panoramica della migrazione'
description: Informazioni sugli strumenti e sulle opzioni disponibili per eseguire la migrazione dei database di SQL Server al Istanza gestita SQL di Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078979"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Panoramica della migrazione: SQL Server ad Azure SQL Istanza gestita
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Informazioni sulle opzioni e considerazioni per la migrazione dei database di SQL Server al Istanza gestita SQL di Azure. 

È possibile eseguire la migrazione di SQL Server database in esecuzione in locale o in: 

- SQL Server in macchine virtuali di Azure.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2). 
- Servizio database relazionale AWS (RDS). 
- Motore di calcolo in Google Cloud Platform (GCP).  
- SQL cloud per SQL Server in GCP. 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Panoramica

[Azure SQL istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md) è un'opzione di destinazione consigliata per carichi di lavoro SQL Server che richiedono un servizio completamente gestito senza dover gestire le macchine virtuali o i sistemi operativi. SQL Istanza gestita consente di spostare le applicazioni locali in Azure con modifiche minime dell'applicazione o del database. Offre un isolamento completo delle istanze con supporto per le reti virtuali native. 

## <a name="considerations"></a>Considerazioni 

I fattori chiave da considerare quando si valutano le opzioni di migrazione sono: 
- Numero di server e database
- Dimensioni dei database
- Tempo di inattività aziendale accettabile durante il processo di migrazione 

Uno dei principali vantaggi della migrazione dei database di SQL Server a SQL Istanza gestita è che è possibile scegliere di eseguire la migrazione dell'intera istanza o solo di un subset di singoli database. Pianificare con attenzione l'inclusione di quanto segue nel processo di migrazione: 
- Tutti i database che devono essere posizionati nella stessa istanza 
- Oggetti a livello di istanza richiesti per l'applicazione, inclusi gli account di accesso, le credenziali, i processi e gli operatori di SQL Agent e i trigger a livello di server 

> [!NOTE]
> Azure SQL Istanza gestita garantisce una disponibilità del 99,99%, anche in scenari critici. Il sovraccarico causato da alcune funzionalità di SQL Istanza gestita non può essere disabilitato. Per ulteriori informazioni, vedere le [cause principali delle differenze di prestazioni tra SQL istanza gestita e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) intervento nel Blog. 


## <a name="choose-an-appropriate-target"></a>Scegliere una destinazione appropriata

Le linee guida generali seguenti possono essere utili per scegliere il livello di servizio e le caratteristiche di SQL Istanza gestita più appropriati per soddisfare i requisiti di base per le [prestazioni](sql-server-to-managed-instance-performance-baseline.md): 

- Utilizzare la baseline utilizzo CPU per eseguire il provisioning di un'istanza gestita corrispondente al numero di core utilizzati dall'istanza di SQL Server. Potrebbe essere necessario scalare le risorse in base alle [caratteristiche di generazione dell'hardware](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Utilizzare la linea di base utilizzo memoria per scegliere un' [opzione vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) che corrisponda in modo appropriato all'allocazione di memoria. 
- Usare la latenza di I/O della linea di base del sottosistema file per scegliere tra il per utilizzo generico (latenza maggiore di 5 ms) e i livelli di servizio business critical (latenza inferiore a 3 ms). 
- Utilizzare la velocità effettiva di base per preallocare le dimensioni dei file di dati e di log per ottenere le prestazioni di I/O previste. 

È possibile scegliere risorse di calcolo e di archiviazione durante la distribuzione e quindi [modificarle successivamente usando il portale di Azure](../../database/scale-resources.md), senza incorrere in tempi di inattività per l'applicazione. 

> [!IMPORTANT]
> Eventuali discrepanze nei [requisiti della rete virtuale per le istanze gestite](../../managed-instance/connectivity-architecture-overview.md#network-requirements) possono impedire la creazione di nuove istanze o l'utilizzo di quelle esistenti. Altre informazioni sulla [creazione di nuove](../../managed-instance/virtual-network-subnet-create-arm-template.md)   reti e sulla configurazione di reti [esistenti](../../managed-instance/vnet-existing-add-subnet.md)   . 

Un altro aspetto importante nella selezione del livello di servizio di destinazione in Azure SQL Istanza gestita (per utilizzo generico rispetto a business critical) è la disponibilità di alcune funzionalità, ad esempio In-Memory OLTP, disponibili solo nel livello business critical. 

### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

L'azienda potrebbe avere requisiti che rendono [SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) una destinazione più appropriata rispetto a istanza gestita SQL di Azure. 

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
|[Servizio Migrazione del database di Azure](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Questo servizio di Azure supporta la migrazione in modalità offline per le applicazioni che possono permettersi tempi di inattività durante il processo di migrazione. A differenza della migrazione continua in modalità online, la migrazione in modalità offline esegue un ripristino unico di un backup completo del database dall'origine alla destinazione. | 
|[Backup e ripristino nativi](../../managed-instance/restore-sample-database-quickstart.md) | SQL Istanza gestita supporta il ripristino di backup di database SQL Server nativi (file con estensione bak). Si tratta dell'opzione di migrazione più semplice per i clienti che possono fornire backup completi del database nell'archiviazione di Azure. Anche i backup completi e differenziali sono supportati e documentati nella [sezione sugli asset di migrazione](#migration-assets) più avanti in questo articolo.| 
|[Servizio di riproduzione log](../../managed-instance/log-replay-service-migrate.md) | Questo servizio cloud è abilitato per SQL Istanza gestita in base alla tecnologia di SQL Server log shipping. Si tratta di un'opzione di migrazione per i clienti che possono fornire backup completi, differenziali e di log al database di archiviazione di Azure. Il servizio di riesecuzione del log viene usato per ripristinare i file di backup dall'archiviazione BLOB di Azure a SQL Istanza gestita.| 
| | |

La tabella seguente elenca gli strumenti di migrazione alternativi: 

|**Tecnologia** |**Descrizione**  |
|---------|---------|
|[Replica transazionale](../../managed-instance/replication-transactional-overview.md) | Replicare i dati dalle tabelle di database di origine SQL Server a SQL Istanza gestita fornendo un'opzione di migrazione del tipo di Sottoscrittore del server di pubblicazione mantenendo la coerenza transazionale. | 
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| Lo [strumento di copia bulk (BCP)](/sql/tools/bcp-utility) copia i dati da un'istanza di SQL Server in un file di dati. Utilizzare lo strumento per esportare i dati dall'origine e importare il file di dati nell'istanza gestita di SQL di destinazione. </br></br> Per le operazioni di copia bulk ad alta velocità per spostare i dati in Istanza gestita SQL di Azure, è possibile usare lo strumento per la [copia bulk intelligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) per ottimizzare la velocità di trasferimento sfruttando le attività di copia parallele. | 
|[Importazione/esportazione guidata/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) è un file di Windows con estensione. bacpac che incapsula lo schema e i dati di un database. È possibile usare BACPAC per esportare i dati da un'origine SQL Server e importare di nuovo i dati in Istanza gestita SQL di Azure. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  L' [attività di copia](../../../data-factory/copy-activity-overview.md) in Azure Data Factory esegue la migrazione dei dati dai database SQL Server di origine a SQL istanza gestita usando connettori predefiniti e un [runtime di integrazione](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory supporta un'ampia gamma di [connettori](../../../data-factory/connector-overview.md) per spostare i dati da origini SQL Server a SQL istanza gestita. |

## <a name="compare-migration-options"></a>Confrontare le opzioni di migrazione

Confrontare le opzioni di migrazione per scegliere il percorso appropriato per le esigenze aziendali. 

Nella tabella seguente vengono confrontate le opzioni di migrazione consigliate: 

|Opzione di migrazione  |Utilizzo  |Considerazioni  |
|---------|---------|---------|
|[Servizio Migrazione del database di Azure](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Migrare database singoli o più database su larga scala. </br> -Può supportare i tempi di inattività durante il processo di migrazione. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server |  -Le migrazioni su larga scala possono essere automatizzate tramite [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Il tempo necessario per completare la migrazione dipende dalle dimensioni del database ed è influenzato dall'ora di backup e ripristino. </br> -Potrebbe essere necessario un tempo di inattività sufficiente. |
|[Backup e ripristino nativi](../../managed-instance/restore-sample-database-quickstart.md) | -Migrare i singoli database delle applicazioni line-of-business.  </br> -Migrazione rapida e semplice senza uno strumento o un servizio di migrazione separato.  </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -Il backup del database usa più thread per ottimizzare il trasferimento dei dati nell'archiviazione BLOB di Azure, ma le dimensioni del database e della larghezza di banda del partner possono influire sulla velocità </br> -Il tempo di inattività deve adattarsi al tempo necessario per eseguire un backup completo e un ripristino, ovvero un'operazione di dimensione dei dati.| 
|[Servizio di riproduzione log](../../managed-instance/log-replay-service-migrate.md) | -Migrare i singoli database delle applicazioni line-of-business.  </br> -È necessario un maggiore controllo per le migrazioni di database.  </br> </br> Origini supportate: </br> -SQL Server (da 2008 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server | -La migrazione comporta l'esecuzione di backup completi del database in SQL Server e la copia dei file di backup nell'archiviazione BLOB di Azure. Il servizio di riesecuzione del log viene usato per ripristinare i file di backup dall'archiviazione BLOB di Azure a SQL Istanza gestita. </br> -I database da ripristinare durante il processo di migrazione saranno in modalità di ripristino e non potranno essere usati per leggere o scrivere fino al termine del processo.| 
| | | |

Nella tabella seguente vengono confrontate le opzioni di migrazione alternative: 

|Metodo o tecnologia |Utilizzo |Considerazioni  |
|---------|---------|---------|
|[Replica transazionale](../../managed-instance/replication-transactional-overview.md) | -Esegue la migrazione pubblicando continuamente le modifiche dalle tabelle di database di origine alle tabelle di database SQL Istanza gestita di destinazione. </br> -Esegue migrazioni di database complete o parziali di tabelle selezionate (subset di un database).  </br> </br> Origini supportate: </br> -SQL Server (da 2012 a 2019) con alcune limitazioni </br> -AWS EC2  </br> -GCP calcolo della macchina virtuale SQL Server | </br> -Il programma di installazione è relativamente complesso rispetto ad altre opzioni di migrazione.   </br> -Fornisce un'opzione di replica continua per eseguire la migrazione dei dati (senza portare i database offline).</br> -La replica transazionale presenta alcune limitazioni da considerare quando si configura il server di pubblicazione nell'istanza di SQL Server di origine. Per altre informazioni, vedere [limitazioni relative alla pubblicazione di oggetti](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) .  </br> -È disponibile la funzionalità di [monitoraggio dell'attività di replica](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Copia bulk](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Eseguire migrazioni di dati complete o parziali. </br> -Può contenere tempi di inattività. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server   | -Richiede tempi di inattività per l'esportazione dei dati dall'origine e l'importazione nella destinazione. </br> -I formati di file e i tipi di dati utilizzati nell'esportazione o nell'importazione devono essere coerenti con gli schemi di tabella. |
|[Importazione/esportazione guidata/BACPAC](../../database/database-import.md)| -Migrare i singoli database delle applicazioni line-of-business. </br>-Adatto per database più piccoli.  </br>  Non richiede uno strumento o un servizio di migrazione separato. </br> </br> Origini supportate: </br> -SQL Server (da 2005 a 2019) in locale o in una VM di Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP calcolo della macchina virtuale SQL Server  |   </br> -Richiede tempi di inattività perché i dati devono essere esportati nell'origine e importati nella destinazione.   </br> -I formati di file e i tipi di dati utilizzati nell'esportazione o nell'importazione devono essere coerenti con gli schemi di tabella per evitare errori di troncamento o di mancata corrispondenza del tipo di dati. </br> -Il tempo impiegato per esportare un database con un numero elevato di oggetti può essere significativamente superiore. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Eseguire la migrazione e/o trasformare i dati dai database di origine SQL Server.</br> -L'Unione di dati da più origini di dati a Istanza gestita SQL di Azure è in genere per carichi di lavoro di business intelligence (BI).   </br> -Richiede la creazione di pipeline di spostamento dei dati in Data Factory per spostare i dati dall'origine alla destinazione.   </br> - Il [costo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) è una considerazione importante e si basa su fattori quali trigger della pipeline, esecuzioni di attività e durata dello spostamento dei dati. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilità delle funzionalità 

Quando si esegue la migrazione di carichi di lavoro basati su altre funzionalità di SQL Server, sono necessarie altre considerazioni. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Eseguire la migrazione di progetti e pacchetti di SQL Server Integration Services (SSIS) in SSISDB ad Azure SQL Istanza gestita usando il [servizio migrazione del database di Azure](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Solo i pacchetti SSIS in SSISDB che iniziano con SQL Server 2012 sono supportati per la migrazione. Converte i pacchetti SSIS meno recenti prima della migrazione. Per ulteriori informazioni, vedere l' [esercitazione sulla conversione dei progetti](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

È possibile eseguire la migrazione dei report di SQL Server Reporting Services (SSRS) ai report impaginati di Power BI. Utilizzare lo [strumento di migrazione RDL](https://github.com/microsoft/RdlMigration) per preparare ed eseguire la migrazione dei report. Microsoft ha sviluppato questo strumento per aiutare i clienti a migrare i report Report Definition Language (RDL) dai server SSRS ai Power BI. È disponibile in GitHub e consente di seguire una procedura dettagliata completa dello scenario di migrazione. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

È possibile eseguire la migrazione di SQL Server Analysis Services modelli tabulari da SQL Server 2012 e versioni successive a Azure Analysis Services, ovvero un modello di distribuzione di piattaforma distribuita come servizio (PaaS) per il modello tabulare Analysis Services in Azure. Per altre informazioni sulla migrazione di modelli locali a Azure Analysis Services in [questa esercitazione video](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/), vedere.

In alternativa, è possibile prendere in considerazione la migrazione dei modelli tabulari Analysis Services locali per [Power bi Premium utilizzando i nuovi endpoint di lettura/scrittura XMLA](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Disponibilità elevata

Le SQL Server funzionalità a disponibilità elevata Always On istanze del cluster di failover e Gruppi di disponibilità Always On diventano obsolete nell'istanza gestita di SQL di destinazione. L'architettura a disponibilità elevata è già incorporata nei livelli di servizio [per utilizzo generico (modello di disponibilità standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [business critical (modello di disponibilità premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) per istanza gestita SQL. Il modello di disponibilità Premium fornisce anche la scalabilità in lettura che consente la connessione a uno dei nodi secondari per finalità di sola lettura.     

Oltre all'architettura a disponibilità elevata inclusa in SQL Istanza gestita, la funzionalità [gruppi di failover automatico](../../database/auto-failover-group-overview.md) consente di gestire la replica e il failover dei database in un'istanza gestita in un'altra area. 

### <a name="sql-agent-jobs"></a>Processi di SQL Agent

Usare l'opzione offline del servizio migrazione del database di Azure per eseguire la migrazione dei [processi di SQL Agent](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). In caso contrario, eseguire lo script dei processi in Transact-SQL (T-SQL) utilizzando SQL Server Management Studio e quindi ricrearli manualmente nell'istanza gestita di SQL di destinazione. 

> [!IMPORTANT]
> Attualmente, il servizio migrazione del database di Azure supporta solo i processi con i passaggi del sottosistema T-SQL. È necessario eseguire manualmente la migrazione dei processi con i passaggi del pacchetto SSIS. 

### <a name="logins-and-groups"></a>Account di accesso e gruppi

Spostare gli account di accesso SQL dall'origine SQL Server al Istanza gestita SQL di Azure usando il servizio migrazione del database in modalità offline.  Utilizzare il riquadro [Seleziona account di accesso](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) della migrazione guidata per eseguire la migrazione degli account di accesso all'istanza gestita di SQL di destinazione. 

Per impostazione predefinita, il servizio migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. Tuttavia, è possibile abilitare la migrazione degli account di accesso di Windows per:

- Verificare che l'istanza gestita di SQL di destinazione disponga dell'accesso in lettura Azure Active Directory (Azure AD). Un utente che dispone del ruolo di amministratore globale può configurare l'accesso tramite il portale di Azure.
- Configurazione del servizio migrazione del database di Azure per abilitare le migrazioni degli account di accesso a utenti o gruppi di Windows. Questa impostazione viene configurata tramite il portale di Azure nella pagina **configurazione** . Dopo aver abilitato questa impostazione, riavviare il servizio per rendere effettive le modifiche.

Dopo aver riavviato il servizio, gli account di accesso di utenti o gruppi di Windows vengono visualizzati nell'elenco degli account di accesso disponibili per la migrazione. Per gli account di accesso di qualsiasi utente o gruppo di Windows di cui si esegue la migrazione, viene richiesto di specificare il nome di dominio associato. Gli account utente del servizio (account con l'autorità NT nome di dominio) e gli account utente virtuali (account con il nome di dominio NT SERVICE) non sono supportati. Per altre informazioni, vedere [come eseguire la migrazione di utenti e gruppi di Windows in un'istanza di SQL Server al istanza gestita SQL di Azure con T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

In alternativa, è possibile usare l' [utilità PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) appositamente progettata da Microsoft Data Migration Architects. L'utilità USA PowerShell per creare uno script T-SQL per ricreare gli account di accesso e selezionare gli utenti del database dall'origine alla destinazione. 

L'utilità PowerShell esegue automaticamente il mapping degli account di Windows Server Active Directory agli account Azure AD ed è in grado di eseguire una ricerca UPN per ogni account di accesso nell'istanza di Active Directory di origine. I ruoli del database e del server personalizzati degli script dell'utilità, insieme alle autorizzazioni utente e di appartenenza ai ruoli. I database indipendenti non sono ancora supportati e viene creato uno script solo un subset delle autorizzazioni di SQL Server possibili. 

### <a name="encryption"></a>Crittografia

Quando si esegue la migrazione dei database protetti da  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   a un'istanza gestita utilizzando l'opzione di ripristino nativo, [migrare il certificato corrispondente](../../managed-instance/tde-certificate-migrate.md) dall'istanza di SQL Server di origine all'istanza gestita di SQL di destinazione *prima* del ripristino del database. 

### <a name="system-databases"></a>Database di sistema

Il ripristino di database di sistema non è supportato. Per eseguire la migrazione degli oggetti a livello di istanza (archiviati nei database master e msdb), crearne uno script usando T-SQL e quindi ricrearli nell'istanza gestita di destinazione. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabelle con ottimizzazione per la memoria)

SQL Server fornisce una funzionalità OLTP In-Memory. Consente l'utilizzo di tabelle ottimizzate per la memoria, tipi di tabella ottimizzata per la memoria e moduli SQL compilati in modo nativo per l'esecuzione di carichi di lavoro con requisiti di velocità effettiva elevata e bassa latenza per l'elaborazione transazionale. 

> [!IMPORTANT]
> In-Memory OLTP è supportato solo nel livello di business critical in Istanza gestita SQL di Azure. Non è supportato nel livello per utilizzo generico.

Se si dispone di tabelle ottimizzate per la memoria o di tipi di tabella ottimizzata per la memoria nell'istanza di SQL Server locale e si vuole eseguire la migrazione ad Azure SQL Istanza gestita, è necessario effettuare una delle operazioni seguenti:

- Scegliere il livello di business critical per l'istanza gestita di SQL di destinazione che supporta In-Memory OLTP.
- Se si vuole eseguire la migrazione al livello per utilizzo generico in Istanza gestita SQL di Azure, rimuovere le tabelle ottimizzate per la memoria, i tipi di tabella ottimizzata per la memoria e i moduli SQL compilati in modo nativo che interagiscono con gli oggetti ottimizzati per la memoria prima della migrazione dei database. È possibile usare la query T-SQL seguente per identificare tutti gli oggetti che devono essere rimossi prima della migrazione al livello per utilizzo generico:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Per altre informazioni sulle tecnologie in memoria, vedere [ottimizzare le prestazioni usando le tecnologie in memoria nel database SQL di Azure e in Azure sql istanza gestita](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud in SQL Istanza gestita. Ad esempio, non è necessario preoccuparsi di gestire i backup perché il servizio esegue questa operazione. È possibile eseguire il ripristino in qualsiasi [punto nel tempo entro il periodo di conservazione](../../database/recovery-using-backups.md#point-in-time-restore). Inoltre, non è necessario preoccuparsi della configurazione della disponibilità elevata, perché [la disponibilità elevata è incorporata](../../database/high-availability-sla.md). 

Per rafforzare la sicurezza, è consigliabile usare [Azure ad autenticazione](../../database/authentication-aad-overview.md), il [controllo](../../managed-instance/auditing-configure.md), il [rilevamento delle minacce](../../database/azure-defender-for-sql.md), la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking).

Oltre alle funzionalità avanzate di gestione e sicurezza, SQL Istanza gestita offre strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro](../../database/monitor-tune-overview.md). [Analisi SQL di Azure](../../../azure-monitor/insights/azure-sql.md) consente di monitorare un ampio set di istanze gestite in modo centralizzato.  [Ottimizzazione automatica](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   nelle istanze gestite monitora continuamente le prestazioni dell'esecuzione del piano SQL e corregge automaticamente i problemi di prestazioni identificati. 

Alcune funzionalità sono disponibili solo dopo che il [livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) è stato impostato sul livello di compatibilità più recente (150). 

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriori informazioni, vedere le risorse seguenti sviluppate per i progetti di migrazione nel mondo reale.

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento fornisce le piattaforme di destinazione consigliate, la preparazione per il cloud e un livello di monitoraggio e aggiornamento dell'applicazione/database per un carico di lavoro. Offre semplici calcoli con un solo clic e la generazione di report che consentono di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale automatico e uniforme per le piattaforme di destinazione.|
|[Utilità DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|È possibile usare DBLoader per caricare dati da file di testo delimitati in SQL Server. Questa utilità console di Windows utilizza l'interfaccia di caricamento bulk SQL Server Native Client. L'interfaccia funziona in tutte le versioni di SQL Server, insieme al Istanza gestita SQL di Azure.|
|[Utilità per spostare gli account di accesso SQL Server locali in Azure SQL Istanza gestita](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Uno script di PowerShell può creare uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database da SQL Server locali ad Azure SQL Istanza gestita. Lo strumento consente il mapping automatico degli account di Windows Server Active Directory agli account Azure AD, oltre alla migrazione facoltativa di SQL Server account di accesso nativi.|
|[Automazione della raccolta dati PerfMon tramite logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|È possibile utilizzare lo strumento logman per raccogliere dati PerfMon (per comprendere le prestazioni di base) e ottenere consigli sulla destinazione della migrazione. Questo strumento usa logman.exe per creare il comando per la creazione, l'avvio, l'arresto e l'eliminazione dei contatori delle prestazioni impostati in un'istanza di SQL Server remota.|
|[Migrazione del database al Istanza gestita SQL di Azure tramite il ripristino di backup completi e differenziali](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Questo white paper fornisce istruzioni e procedure per accelerare le migrazioni da SQL Server ad Azure SQL Istanza gestita se sono presenti solo backup completi e differenziali (e nessuna funzionalità di backup del log).|

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a migrare i database di SQL Server al Istanza gestita SQL di Azure, vedere la [Guida alla migrazione da SQL Server ad Azure sql istanza gestita](sql-server-to-managed-instance-guide.md).

- Per una matrice di servizi e strumenti che consentono di eseguire scenari di migrazione di database e dati, nonché attività speciali, vedere [Servizi e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul Istanza gestita SQL di Azure, vedere:
   - [Livelli di servizio in Azure SQL Istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Differenze tra SQL Server e Azure SQL Istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Per informazioni dettagliate su come eseguire test A/B a livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
