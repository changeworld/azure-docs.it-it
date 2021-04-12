---
title: 'Analisi delle sinapsi di Azure: Guida alla migrazione'
description: Seguire questa guida per eseguire la migrazione dei database a un pool SQL dedicato di Azure sinapsi Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8304064e62ea3996e2ee6be6e12885cb853c9375
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278780"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Eseguire la migrazione di un data warehouse a un pool SQL dedicato in Azure sinapsi Analytics

Le sezioni seguenti forniscono una panoramica degli elementi necessari per la migrazione di una soluzione data warehouse esistente a un pool SQL dedicato di analisi delle sinapsi di Azure.

## <a name="overview"></a>Panoramica

Prima di iniziare la migrazione, è necessario verificare che Azure sinapsi Analytics sia la soluzione migliore per il carico di lavoro. Azure sinapsi Analytics è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. Per eseguire la migrazione ad Azure sinapsi Analytics sono necessarie alcune modifiche di progettazione che non sono difficili da comprendere, ma l'implementazione potrebbe richiedere del tempo. Se l'azienda richiede un data warehouse di classe enterprise, i vantaggi valgono la pena. Tuttavia, se non è necessaria la potenza di Azure sinapsi Analytics, è più conveniente usare [SQL Server](https://docs.microsoft.com/sql/sql-server/) o il [database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/).

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
- Formati non compatibili, ad esempio JSON e XML.

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Uno dei blocchi critici che i clienti affrontano è la conversione degli oggetti di database quando si esegue la migrazione da un sistema a un altro. Il [percorso delle sinapsi di Azure](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) consente di eseguire l'aggiornamento a una piattaforma di data warehouse moderna automatizzando la traduzione degli oggetti data warehouse esistente. Si tratta di uno strumento gratuito, intuitivo e facile da usare che consente di automatizzare la conversione del codice per consentire una migrazione più rapida ad Azure sinapsi Analytics.

## <a name="prerequisites"></a>Prerequisiti

# <a name="migrate-from-sql-server"></a>[Eseguire la migrazione da SQL Server](#tab/migratefromSQLServer)

Per eseguire la migrazione del SQL Server data warehouse ad Azure sinapsi Analytics, verificare di aver soddisfatto i prerequisiti seguenti:

- Avere un carico di lavoro data warehouse o Analytics.
- Scaricare la versione più recente del [percorso della sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi di Azure.
- Avere un [pool SQL dedicato](../get-started-create-workspace.md) in un'area di lavoro di sinapsi di Azure.

# <a name="migrate-from-netezza"></a>[Eseguire la migrazione da Netezza](#tab/migratefromNetezza)

Per eseguire la migrazione del data warehouse Netezza ad Azure sinapsi Analytics, verificare di aver soddisfatto i prerequisiti seguenti:

- Scaricare la versione più recente del [percorso della sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi di Azure.
- Avere un [pool SQL dedicato](../get-started-create-workspace.md) in un'area di lavoro di sinapsi di Azure.

Per altre informazioni, vedere la pagina relativa alle [soluzioni e alla migrazione di Azure sinapsi Analytics per Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrare da fiocco di neve](#tab/migratefromSnowflake)

Per eseguire la migrazione del data warehouse a fiocco di neve ad Azure sinapsi Analytics, verificare di aver soddisfatto i prerequisiti seguenti:

- Scaricare la versione più recente del [percorso sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per migrare gli oggetti fiocco di neve agli oggetti sinapsi di Azure.
- Avere un [pool SQL dedicato](../get-started-create-workspace.md) in un'area di lavoro di sinapsi di Azure.

# <a name="migrate-from-oracle"></a>[Eseguire la migrazione da Oracle](#tab/migratefromOracle)

Per eseguire la migrazione del data warehouse Oracle ad Azure sinapsi Analytics, verificare di aver soddisfatto i prerequisiti seguenti:

- Avere un carico di lavoro data warehouse o Analytics.
- Scaricare SQL Server Migration Assistant per Oracle per convertire gli oggetti Oracle in SQL Server. Per ulteriori informazioni, vedere [migrazione di database Oracle a SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Scaricare la versione più recente del [percorso della sinapsi di Azure](https://www.microsoft.com/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi di Azure.
- Avere un [pool SQL dedicato](../get-started-create-workspace.md) in un'area di lavoro di sinapsi di Azure.

Per altre informazioni, vedere la pagina relativa alle [soluzioni e alla migrazione di Azure sinapsi Analytics per un data warehouse Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver deciso di eseguire la migrazione di una soluzione esistente ad Azure sinapsi Analytics, è necessario pianificare la migrazione prima di iniziare. Un obiettivo principale della pianificazione è garantire che i dati, gli schemi di tabella e il codice siano compatibili con l'analisi delle sinapsi di Azure. Esistono alcune differenze di compatibilità tra il sistema corrente e l'analisi delle sinapsi di Azure che è necessario risolvere. Inoltre, la migrazione di grandi quantità di dati in Azure richiede tempo. Un'attenta pianificazione consente di velocizzare il processo di recupero dei dati in Azure.

Un altro obiettivo principale della pianificazione consiste nel modificare la progettazione per assicurarsi che la soluzione sfrutti pienamente le prestazioni di query elevate che Azure sinapsi Analytics è progettato per fornire. La progettazione di data warehouse per la scalabilità introduce modelli di progettazione univoci, quindi gli approcci tradizionali non sono sempre il migliore. Sebbene sia possibile apportare alcune modifiche alla progettazione dopo la migrazione, apportare modifiche in un secondo momento durante il processo consente di risparmiare tempo.

## <a name="migrate"></a>Migrate

Per eseguire una migrazione corretta, è necessario eseguire la migrazione degli schemi, del codice e dei dati della tabella. Per istruzioni più dettagliate su questi argomenti, vedere gli articoli seguenti:

- [Considera progettazione tabella](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Prendere in considerazione la modifica del codice](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Eseguire la migrazione dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Considerare la gestione del carico di lavoro](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Altre risorse

Il team di consulenza clienti dispone di alcune eccezionali linee guida di analisi delle sinapsi di Azure (in precedenza Azure SQL Data Warehouse) pubblicate come post di Blog. Per ulteriori informazioni sulla migrazione, vedere la pagina relativa [alla migrazione dei dati a Azure SQL data warehouse in pratica](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Asset di migrazione da Engagement reali

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti. Sono state sviluppate a supporto di un engagement di progetto di migrazione reale.

| Titolo/collegamento                              | Descrizione                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione consigliate, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione o del database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e la generazione di report che consentono di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale di piattaforma di destinazione automatizzato e uniforme. |
| [Gestione dei problemi di codifica dei dati durante il caricamento dei dati in Azure sinapsi Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Questo post di Blog fornisce informazioni dettagliate su alcuni dei problemi di codifica dei dati che possono verificarsi durante l'uso di polibase per caricare i dati in SQL Data Warehouse. In questo articolo sono inoltre disponibili alcune opzioni che è possibile utilizzare per risolvere tali problemi e caricare correttamente i dati. |
| [Recupero delle dimensioni delle tabelle nel pool SQL dedicato di Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Una delle attività principali che un progettista deve eseguire è ottenere le metriche su un nuovo ambiente dopo la migrazione. Gli esempi includono la raccolta dei tempi di caricamento dall'ambiente locale al cloud e la raccolta di tempi di caricamento di base. Una delle attività più importanti consiste nel determinare le dimensioni di archiviazione in SQL Data Warehouse rispetto alla piattaforma corrente del cliente. |
| [Utilità per spostare gli account di accesso SQL Server locali in Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Uno script di PowerShell crea uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database da un'istanza locale di SQL Server a un servizio della piattaforma distribuita come servizio (PaaS) di Azure. Lo strumento consente il mapping automatico degli account di Windows Server Active Directory agli account Azure Active Directory oppure può eseguire ricerche UPN per ogni account di accesso in Windows Server Active Directory locale. Lo strumento sposta facoltativamente SQL Server account di accesso nativi. Vengono inclusi nello script i ruoli del server e del database personalizzati, insieme all'appartenenza al ruolo, al ruolo del database e alle autorizzazioni utente. I database indipendenti non sono supportati e viene inserito in uno script solo un subset delle autorizzazioni di SQL Server possibili. Ulteriori informazioni sono disponibili nel documento di supporto e lo script include commenti per una maggiore facilità di comprensione. |

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="videos"></a>Video

Osservare il modo in [cui Walgreens ha migrato il sistema di inventario delle vendite al dettaglio](https://www.youtube.com/watch?v=86dhd8N1lH4) con circa 100 TB di dati da Netezza ad Azure sinapsi Analytics in fase di record.
