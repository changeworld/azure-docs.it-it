---
title: 'Analisi delle sinapsi di Azure: Guida alla migrazione'
description: Seguire questa guida per eseguire la migrazione dei database al pool SQL dedicato di Azure sinapsi Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: e27e7e0bb4616508c8f137260bde68de9420f9cf
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644961"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrazione di un data warehouse a un pool SQL dedicato in Azure sinapsi Analytics 
Le sezioni seguenti forniscono una panoramica degli elementi necessari per la migrazione di una soluzione di data warehouse esistente al pool SQL dedicato di Azure sinapsi Analytics.

## <a name="overview"></a>Panoramica
Prima di eseguire la migrazione, è necessario verificare che Azure sinapsi Analytics sia la soluzione migliore per il carico di lavoro. Azure sinapsi Analytics è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. Per eseguire la migrazione ad Azure sinapsi Analytics sono necessarie alcune modifiche di progettazione che non sono difficili da comprendere, ma l'implementazione potrebbe richiedere del tempo. Se l'azienda richiede un data warehouse di classe enterprise, i vantaggi valgono la pena. Tuttavia, se non è necessaria la potenza di Azure sinapsi Analytics, è più conveniente usare [SQL Server](https://docs.microsoft.com/sql/sql-server/) o il [database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/).

Si consiglia di usare Azure sinapsi Analytics quando si:
- Avere uno o più terabyte di dati.
- Pianificare l'esecuzione di analisi su quantità sostanziali di dati.
- È necessario avere la possibilità di ridimensionare le risorse di calcolo e archiviazione.
- Si vuole risparmiare sui costi sospendendo le risorse di calcolo quando non sono necessarie.

Anziché Azure sinapsi Analytics, prendere in considerazione altre opzioni per i carichi di lavoro operativi (OLTP) con:
- Letture e Scritture ad alta frequenza.
- Viene selezionato un numero elevato di singleton.
- Volumi elevati di inserimenti in una singola riga.
- Necessità di elaborazione riga per riga.
- Formati non compatibili (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Uno dei blocchi critici che i clienti affrontano è la conversione degli oggetti di database durante la migrazione da un sistema a un altro. Il [percorso delle sinapsi di Azure](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) consente di eseguire l'aggiornamento a una piattaforma di data warehouse moderna automatizzando la traduzione degli oggetti data warehouse esistente. Si tratta di uno strumento gratuito, intuitivo e facile da usare che consente di automatizzare la conversione del codice e di velocizzare la migrazione ad Azure sinapsi Analytics.

## <a name="prerequisites"></a>Prerequisiti
# <a name="migrate-from-sql-server"></a>[Eseguire la migrazione da SQL Server](#tab/migratefromSQLServer)
Per eseguire la migrazione del SQL Server data warehouse ad Azure sinapsi Analytics, verificare che siano soddisfatti i prerequisiti seguenti: 

- Un carico di lavoro data warehouse o Analytics 
- Scaricare la versione più recente dello strumento di [percorso delle sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi
- Un [pool SQL dedicato](../get-started-create-workspace.md) nell'area di lavoro sinapsi di Azure. 

# <a name="migrate-from-netezza"></a>[Eseguire la migrazione da Netezza](#tab/migratefromNetezza)
Per eseguire la migrazione del data warehouse Netezza ad Azure sinapsi Analytics, verificare che siano soddisfatti i prerequisiti seguenti: 

- Scaricare la versione più recente dello strumento di [percorso delle sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi
- Un [pool SQL dedicato](../get-started-create-workspace.md) nell'area di lavoro sinapsi di Azure.

Per altre informazioni, vedere la pagina relativa alle [soluzioni e alla migrazione di Azure sinapsi Analytics per Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrare da fiocco di neve](#tab/migratefromSnowflake)
Per eseguire la migrazione del data warehouse a fiocco di neve ad Azure sinapsi Analytics, verificare che siano soddisfatti i prerequisiti seguenti: 

- Scaricare la versione più recente dello strumento di [percorso delle sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione degli oggetti di fiocco di neve
- Un [pool SQL dedicato](../get-started-create-workspace.md) nell'area di lavoro sinapsi di Azure. 

# <a name="migrate-from-oracle"></a>[Eseguire la migrazione da Oracle](#tab/migratefromOracle)
Per eseguire la migrazione del data warehouse Oracle ad Azure sinapsi Analytics, verificare che siano soddisfatti i prerequisiti seguenti: 

- Un carico di lavoro data warehouse o Analytics 
- Scaricare SSMA per Oracle per convertire gli oggetti Oracle in SQL Server. Per ulteriori informazioni, vedere [migrazione di database Oracle a SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) . 
- Scaricare la versione più recente dello strumento di [percorso delle sinapsi di Azure](https://www.microsoft.com/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi di Azure.
- Un [pool SQL dedicato](../get-started-create-workspace.md) nell'area di lavoro sinapsi di Azure. 

Per altre informazioni, vedere la pagina relativa alle [soluzioni e alla migrazione di Azure sinapsi Analytics per un data warehouse Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Pre-migrazione
Dopo aver deciso di eseguire la migrazione di una soluzione esistente ad Azure sinapsi Analytics, è importante pianificare la migrazione prima di iniziare. Un obiettivo principale della pianificazione è garantire che i dati, gli schemi di tabella e il codice siano compatibili con l'analisi delle sinapsi di Azure. Esistono alcune differenze di compatibilità tra il sistema corrente e l'analisi delle sinapsi di Azure che è necessario aggirare. Inoltre, la migrazione di grandi quantità di dati in Azure richiede tempo. Un'attenta pianificazione consente di velocizzare il processo di recupero dei dati in Azure. Un altro obiettivo principale della pianificazione consiste nel modificare la progettazione per assicurarsi che la soluzione sfrutti pienamente le prestazioni di query elevate che Azure sinapsi Analytics è progettato per fornire. La progettazione di data warehouse per la scalabilità introduce modelli di progettazione univoci, quindi gli approcci tradizionali non sono sempre il migliore. Sebbene sia possibile apportare alcune modifiche alla progettazione dopo la migrazione, apportare modifiche in un secondo momento durante il processo consente di risparmiare tempo.

## <a name="migrate"></a>Migrate
Per eseguire una migrazione corretta, è necessario eseguire la migrazione degli schemi, del codice e dei dati della tabella. Per istruzioni più dettagliate su questi argomenti, vedere:
- L'articolo [considera la progettazione delle tabelle](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- L'articolo [considera la modifica del codice](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- L'articolo [esegue la migrazione dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- L'articolo prende in [considerazione la gestione del carico di lavoro](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>Risorse aggiuntive 
- CAT (Customer Advisory team) presenta alcune eccezionali linee guida di analisi delle sinapsi di Azure (in precedenza SQL DW) pubblicate come post di Blog. Per altre informazioni sulla migrazione, vedere l'articolo relativo alla [migrazione dei dati a Azure SQL data warehouse in pratica](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Asset di migrazione da Engagement reali
Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione del mondo reale.

| Titolo/collegamento                              | Descrizione                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e generazione di report che consentono di accelerare le valutazioni di grandi dimensioni grazie a un processo decisionale automatizzato e uniforme della piattaforma di destinazione. |
| [Gestione dei problemi di codifica dei dati durante il caricamento dei dati in Azure sinapsi Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Questo Blog ha lo scopo di fornire informazioni su alcuni dei problemi di codifica dei dati che possono verificarsi durante l'uso di polibase per caricare i dati in SQL Data Warehouse. In questo articolo sono inoltre disponibili alcune opzioni che è possibile utilizzare per risolvere tali problemi e caricare correttamente i dati. |
| [Recupero delle dimensioni delle tabelle nel pool SQL dedicato di Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Una delle attività principali che un progettista deve eseguire è quello di ottenere le metriche relative a un nuovo ambiente post-migrazione: raccolta dei tempi di caricamento da locale al cloud, raccolta dei tempi di caricamento di base e così via. Di queste attività, uno dei più importanti consiste nel determinare le dimensioni di archiviazione in SQL Data Warehouse rispetto alla piattaforma corrente del cliente. |
| [Utilità per spostare gli account di accesso SQL Server locali in Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Uno script di PowerShell che crea uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database da un SQL Server locale a un servizio PaaS di Azure SQL. Lo strumento consente il mapping automatico degli account di Windows AD per Azure AD account oppure può eseguire ricerche UPN per ogni account di accesso con le Active Directory Windows locali. Lo strumento, facoltativamente, consente di spostare anche SQL Server account di accesso nativi. I ruoli server e database personalizzati sono inclusi nello script, nonché l'appartenenza al ruolo e le autorizzazioni utente e ruolo del database. I database indipendenti non sono ancora supportati e viene creato uno script solo un subset delle autorizzazioni di SQL Server possibili. ovvero la concessione delle autorizzazioni with Grant non è supportata (alberi delle autorizzazioni complessi). Ulteriori informazioni sono disponibili nel documento di supporto e lo script include commenti per una maggiore facilità di comprensione. |

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="videos"></a>Video
- Osservare il modo in cui [Walgreens ha migrato il sistema di inventario delle vendite al dettaglio](https://www.youtube.com/watch?v=86dhd8N1lH4) con informazioni su 100 TB di dati da Netezza ad Azure sinapsi Analytics (in precedenza SQL DW) in fase di record. 