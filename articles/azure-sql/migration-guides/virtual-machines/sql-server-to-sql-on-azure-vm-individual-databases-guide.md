---
title: 'SQL Server SQL Server in macchine virtuali di Azure: Guida alla migrazione'
description: In questa guida si apprenderà come eseguire la migrazione dei singoli database di SQL Server ai SQL Server in macchine virtuali di Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550898"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Guida alla migrazione: SQL Server di SQL Server in macchine virtuali di Azure

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In questa guida si apprenderà come *individuare*, *valutare* e *migrare* i database utente da SQL Server a un'istanza di SQL Server in macchine virtuali di Azure mediante backup e ripristino e log shipping che usa [Data Migration Assistant](/sql/dma/dma-overview) per la valutazione.

È possibile eseguire la migrazione di SQL Server in esecuzione in locale o in:

- SQL Server su macchine virtuali (VM).
- Amazon Web Services (AWS) EC2.
- Servizio database relazionale di Amazon (AWS RDS).
- Motore di calcolo (Google Cloud Platform [GCP]).

Per informazioni sulle strategie di migrazione aggiuntive, vedere la [SQL Server Panoramica della migrazione delle macchine virtuali](sql-server-to-sql-on-azure-vm-migration-overview.md). Per altre guide alla migrazione, vedere [guide alla migrazione del database di Azure](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagramma che mostra un flusso del processo di migrazione.":::

## <a name="prerequisites"></a>Prerequisiti

Per la migrazione a SQL Server in macchine virtuali di Azure sono necessarie le risorse seguenti:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- [Progetto Azure migrate](../../../migrate/create-manage-projects.md).
- Una destinazione preparata SQL Server in un'istanza di [macchine virtuali di Azure con](../../virtual-machines/windows/create-sql-vm-portal.md) una versione uguale o superiore a quella dell'origine SQL Server.
- [Connettività tra Azure e](/azure/architecture/reference-architectures/hybrid-networking)l'ambiente locale.
- [Scelta di una strategia di migrazione appropriata](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Pre-migrazione

Prima di iniziare la migrazione, è necessario individuare la topologia dell'ambiente SQL e valutare la fattibilità della migrazione desiderata.

### <a name="discover"></a>Rilevazione

Azure Migrate valuta l'idoneità della migrazione dei computer locali, esegue il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione in locale. Per pianificare la migrazione, usare Azure Migrate per [identificare le origini dati esistenti e i dettagli sulle funzionalità usate dalle](../../../migrate/concepts-assessment-calculation.md) istanze di SQL Server. Questo processo comporta l'analisi della rete per identificare tutte le istanze di SQL Server nell'organizzazione con la versione e le funzionalità in uso.

> [!IMPORTANT]
> Quando si sceglie una macchina virtuale di Azure di destinazione per l'istanza di SQL Server, assicurarsi di prendere in considerazione le [linee guida sulle prestazioni per SQL Server in macchine virtuali di Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Per altri strumenti di individuazione, vedere i [Servizi e gli strumenti](../../../dms/dms-tools-matrix.md#business-justification-phase) disponibili per gli scenari di migrazione dei dati.

### <a name="assess"></a>Valutare

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Dopo aver individuato tutte le origini dati, usare [Data Migration Assistant](/sql/dma/dma-overview) per valutare le istanze di SQL Server locali che eseguono la migrazione a un'istanza di SQL Server in macchine virtuali di Azure per comprendere i gap tra le istanze di origine e di destinazione.

> [!NOTE]
> Se _non_ si sta aggiornando la versione di SQL Server, ignorare questo passaggio e passare alla sezione [Migrate (migrazione](#migrate) ).

#### <a name="assess-user-databases"></a>Valutare i database utente

Data Migration Assistant facilita la migrazione a una piattaforma dati moderna grazie al rilevamento di problemi di compatibilità che possono influire sulle funzionalità del database nella nuova versione di SQL Server. Data Migration Assistant consiglia miglioramenti per le prestazioni e l'affidabilità per l'ambiente di destinazione e consente inoltre di spostare lo schema, i dati e gli oggetti di accesso dal server di origine al server di destinazione.

Per altre informazioni, vedere [Assessment](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>In base al tipo di valutazione, le autorizzazioni necessarie per la SQL Server di origine possono essere diverse:
   > - Per la *funzionalità* di Advisor, le credenziali fornite per la connessione al database di SQL Server di origine devono essere un membro del ruolo del server *sysadmin* .
   > - Per i *problemi di compatibilità* Advisor, le credenziali specificate devono avere almeno `CONNECT SQL` le `VIEW SERVER STATE` autorizzazioni, e `VIEW ANY DEFINITION` .
   > - Data Migration Assistant evidenzierà le autorizzazioni necessarie per l'advisor scelto prima di eseguire la valutazione.

#### <a name="assess-the-applications"></a>Valutare le applicazioni

In genere, un livello dell'applicazione accede ai database utente per salvare in modo permanente e modificare i dati. Data Migration Assistant possibile valutare il livello di accesso ai dati di un'applicazione in due modi:

- Tramite [gli eventi estesi](/sql/relational-databases/extended-events/extended-events) acquisiti o [SQL Server Profiler tracce](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) dei database utente. È inoltre possibile utilizzare il [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) per creare un registro di traccia che può essere utilizzato anche per il test a/B.
- Usando [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), che fornisce l'individuazione e la valutazione delle query SQL all'interno del codice e viene usato per eseguire la migrazione del codice sorgente dell'applicazione da una piattaforma di database a un'altra. Questo strumento supporta i tipi di file più diffusi, come C#, Java, XML e testo normale. Per una guida su come eseguire una valutazione di Data Access Migration Toolkit, vedere il post di Blog relativo all' [uso di Data Migration Assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Durante la valutazione dei database utente, usare Data Migration Assistant per [importare](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) i file di traccia acquisiti o i file del Toolkit di migrazione dell'accesso ai dati.

#### <a name="assessments-at-scale"></a>Valutazioni su larga scala

Se si dispone di più server che richiedono una valutazione del Data Migration Assistant, è possibile automatizzare il processo tramite l' [interfaccia della riga di comando](/sql/dma/dma-commandline). Utilizzando l'interfaccia, è possibile preparare i comandi di valutazione in anticipo per ogni istanza di SQL Server nell'ambito per la migrazione.

Per la creazione di report di riepilogo tra le aziende di grandi dimensioni, è ora possibile [consolidare Data Migration Assistant valutazioni in Azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Effettuare il refactoring dei database con Data Migration Assistant

In base ai risultati della valutazione Data Migration Assistant, è possibile che si disponga di una serie di raccomandazioni per garantire che i database utente eseguano e funzionino correttamente dopo la migrazione. Data Migration Assistant fornisce informazioni dettagliate sugli oggetti interessati e sulle risorse per la risoluzione di ogni problema. Assicurarsi di risolvere tutte le modifiche di rilievo e le modifiche del comportamento prima di avviare la migrazione di produzione.

Per le funzionalità deprecate, è possibile scegliere di eseguire i database utente con la modalità di [compatibilità](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) originale se si vuole evitare di apportare queste modifiche e velocizzare la migrazione. Questa azione impedirà l' [aggiornamento della compatibilità del database](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) finché gli elementi deprecati non saranno stati risolti.

È necessario creare uno script per tutte le correzioni Data Migration Assistant e applicarle al database SQL Server di destinazione durante la fase di [post-migrazione](#post-migration) .

> [!CAUTION]
> Non tutte le versioni di SQL Server supportano tutte le modalità di compatibilità. Verificare che la [versione di SQL Server di destinazione](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) supporti la compatibilità del database scelta. Ad esempio, SQL Server 2019 non supporta i database con compatibilità di livello 90 (SQL Server 2005). Questi database richiedono almeno un aggiornamento al livello di compatibilità 100.
>

## <a name="migrate"></a>Migrate

Dopo aver completato i passaggi di pre-migrazione, si è pronti per eseguire la migrazione dei database e dei componenti utente. Eseguire la migrazione dei database usando il [metodo di migrazione](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)preferito.

Nelle sezioni seguenti vengono illustrati i passaggi per eseguire una migrazione mediante backup e ripristino o una migrazione del tempo di inattività minima mediante backup e ripristino con log shipping.

### <a name="backup-and-restore"></a>Backup e ripristino

Per eseguire una migrazione standard mediante backup e ripristino:

1. Configurare la connettività per SQL Server in macchine virtuali di Azure in base alle esigenze. Per altre informazioni, vedere [connettersi a una macchina virtuale SQL Server in Azure (Gestione risorse)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Sospendere o arrestare le applicazioni che utilizzano database destinati alla migrazione.
1. Verificare che i database utente siano inattivi usando la [modalità utente singolo](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Eseguire un backup completo del database su una posizione locale.
1. Copiare i file di backup locali nella macchina virtuale usando un desktop remoto, [Azure Esplora dati](/azure/data-explorer/data-explorer-overview)o l' [utilità da riga di comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md). Sono consigliati più di 2 TB di backup.
1. Ripristinare i backup completi del database nel SQL Server in macchine virtuali di Azure.

### <a name="log-shipping-minimize-downtime"></a>Log shipping (riduzione del tempo di inattività)

Per eseguire una migrazione del tempo di inattività minimo utilizzando backup e ripristino e log shipping:

1. Configurare la connettività al SQL Server in macchine virtuali di Azure in base alle esigenze. Per altre informazioni, vedere [connettersi a una macchina virtuale SQL Server in Azure (Gestione risorse)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Assicurarsi che i database utente locali da migrare siano in un modello di recupero con registrazione completa o con registrazione minima delle operazioni bulk.
1. Eseguire un backup completo del database in un percorso locale e modificare tutti i processi di backup completo del database esistenti per usare la parola chiave [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) per mantenere la catena di log.
1. Copiare i file di backup locali nella macchina virtuale usando un desktop remoto, [Azure Esplora dati](/azure/data-explorer/data-explorer-overview)o l' [utilità da riga di comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md). Sono consigliati i backup di dimensioni superiori a 1 TB.
1. Ripristinare i backup completi del database in SQL Server in macchine virtuali di Azure.
1. Configurare [log shipping](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) tra il database locale e SQL Server in macchine virtuali di Azure. Assicurarsi di non reinizializzare i database perché questa attività è già stata completata nei passaggi precedenti.
1. Passare al server di destinazione.
   1. Sospendere o arrestare le applicazioni utilizzando i database di cui eseguire la migrazione.
   1. Verificare che i database utente siano inattivi usando la [modalità utente singolo](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Quando si è pronti, eseguire un log shipping [failover controllato](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) dei database locali per SQL Server in macchine virtuali di Azure.

### <a name="migrate-objects-outside-user-databases"></a>Eseguire la migrazione di oggetti all'esterno dei database utente

È possibile che siano necessari altri oggetti SQL Server per il funzionamento semplice dei database utente dopo la migrazione.

La tabella seguente include un elenco dei componenti e dei metodi di migrazione consigliati che possono essere completati prima o dopo la migrazione dei database utente.

| **Funzionalità** | **Componente** | **Metodi di migrazione** |
| --- | --- | --- |
| **Database** | Modello | Script con SQL Server Management Studio. |
|| TempDB | Pianificare lo spostamento di tempDB nel [disco temporaneo della macchina virtuale (SSD) di Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)per ottenere prestazioni ottimali. Assicurarsi di selezionare le dimensioni di una macchina virtuale con un'unità SSD locale sufficiente per gestire il database tempDB. |
|| Database utente con FileStream | Usare i metodi di [backup e ripristino](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) per la migrazione. Data Migration Assistant non supporta i database con FileStream. |
| **Sicurezza** | SQL Server e account di accesso di Windows | Utilizzare Data Migration Assistant per [eseguire la migrazione degli account di accesso utente](/sql/dma/dma-migrateserverlogins). |
|| Ruoli di SQL Server | Script con SQL Server Management Studio. |
|| Provider del servizio di crittografia | Consigliare la [conversione per usare Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Questa procedura usa il [provider di risorse VM SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Oggetti server** | Dispositivi di backup | Sostituire con il backup del database usando [backup di Azure](../../../backup/backup-sql-server-database-azure-vms.md)o scrivere backup in [archiviazione di azure](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Questa procedura usa il [provider di risorse VM SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Server collegati | Script con SQL Server Management Studio. |
|| Trigger del server | Script con SQL Server Management Studio. |
| **Replica** | Pubblicazioni locali | Script con SQL Server Management Studio. |
|| Sottoscrittori locali | Script con SQL Server Management Studio. |
| **PolyBase** | PolyBase | Script con SQL Server Management Studio. |
| **Gestione** | Posta elettronica database | Script con SQL Server Management Studio. |
| **SQL Server Agent** | Processi | Script con SQL Server Management Studio. |
|| Avvisi | Script con SQL Server Management Studio. |
|| Operatori | Script con SQL Server Management Studio. |
|| Proxy | Script con SQL Server Management Studio. |
| **Sistema operativo** | File, condivisioni file | Prendere nota di tutti gli altri file o condivisioni file usati da SQL Server e replicarli nella destinazione macchine virtuali di Azure. |

## <a name="post-migration"></a>Post-migrazione

Dopo aver completato la fase di migrazione, è necessario completare una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. In alcuni casi, l'esecuzione di questa attività potrebbe richiedere modifiche alle applicazioni.

Applicare eventuali correzioni consigliate da Data Migration Assistant ai database utente. È necessario creare uno script per queste correzioni per garantire la coerenza e consentire l'automazione.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test alla migrazione del database è costituito dalle attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare le query SQL. Creazione di query di convalida per l'esecuzione in entrambi i database di origine e di destinazione. Le query di convalida devono coprire l'ambito definito.
1. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire i test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

> [!TIP]
> Utilizzare i [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) per facilitare la valutazione delle prestazioni di SQL Server di destinazione.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati, verificare la completezza e risolvere i potenziali problemi di prestazioni con il carico di lavoro.

Per ulteriori informazioni su questi problemi e sui passaggi per attenuarli, vedere:

- [Guida all'ottimizzazione e alla convalida post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Ottimizzazione delle prestazioni in macchine virtuali SQL di Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Centro di ottimizzazione dei costi di Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Passaggi successivi

- Per verificare la disponibilità dei servizi che si applicano a SQL Server, vedere il [centro di infrastruttura globale di Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati e attività speciali, vedere [Servizi e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).
- Per altre informazioni su Azure SQL, vedere:
   - [Opzioni di distribuzione](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server in Macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calcolatore costo totale di proprietà (TCO) di Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   - [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Procedure consigliate per il costo e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Per informazioni sulle licenze, vedere:
   - [Bring Your Own License con il Vantaggio Azure Hybrid](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Ottieni supporto esteso gratuito per SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni sull'esecuzione di test A/B per il livello di accesso ai dati, vedere [Panoramica di database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
