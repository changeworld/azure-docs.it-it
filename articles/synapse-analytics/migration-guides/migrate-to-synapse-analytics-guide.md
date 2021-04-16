---
title: 'Azure Synapse Analytics: Guida alla migrazione'
description: Seguire questa guida per eseguire la migrazione dei database a Azure Synapse Analytics sql dedicato.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565645"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Eseguire la data warehouse a un pool SQL dedicato in Azure Synapse Analytics

Le sezioni seguenti forniscono una panoramica delle operazioni di migrazione di una soluzione data warehouse esistente a un pool SQL Azure Synapse Analytics dedicato.

## <a name="overview"></a>Panoramica

Prima di iniziare la migrazione, è necessario verificare che Azure Synapse Analytics sia la soluzione migliore per il carico di lavoro. Azure Synapse Analytics è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. La migrazione Azure Synapse Analytics richiede alcune modifiche di progettazione che non sono difficili da comprendere, ma che potrebbero richiedere del tempo per l'implementazione. Se l'azienda richiede un data warehouse di classe enterprise, i vantaggi valgono la pena. Tuttavia, se non è necessaria la potenza del Azure Synapse Analytics, è più conveniente usare SQL Server [o](https://docs.microsoft.com/sql/sql-server/) [database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/).

È consigliabile usare Azure Synapse Analytics quando:

- Avere uno o più terabyte di dati.
- Pianificare l'esecuzione di analisi su quantità sostanziali di dati.
- È necessaria la possibilità di ridimensionare le risorse di calcolo e archiviazione.
- Per risparmiare sui costi, sospendere le risorse di calcolo quando non sono necessarie.

Anziché Azure Synapse Analytics, prendere in considerazione altre opzioni per i carichi di lavoro operativi (OLTP) con:

- Operazioni di lettura e scrittura ad alta frequenza.
- Numero elevato di opzioni di selezione singleton.
- Volumi elevati di inserimenti a riga singola.
- Esigenze di elaborazione riga per riga.
- Formati incompatibili ,ad esempio JSON e XML.

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Uno dei blocchi critici che i clienti devono affrontare è la traduzione degli oggetti di database durante la migrazione da un sistema a un altro. [Azure Synapse percorso consente](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) di eseguire l'aggiornamento a una piattaforma data warehouse moderna automatizzando la conversione degli oggetti delle data warehouse. Si tratta di uno strumento gratuito, intuitivo e facile da usare che automatizza la conversione del codice per consentire una migrazione più rapida a Azure Synapse Analytics.

## <a name="prerequisites"></a>Prerequisiti

# <a name="migrate-from-sql-server"></a>[Eseguire la migrazione da SQL Server](#tab/migratefromSQLServer)

Per eseguire la SQL Server data warehouse a Azure Synapse Analytics, assicurarsi di aver soddisfatto i prerequisiti seguenti:

- Avere un carico data warehouse o di analisi.
- Scaricare la versione più recente di [Azure Synapse percorso per](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eseguire la migrazione SQL Server oggetti Azure Synapse oggetti.
- Avere un [pool SQL dedicato in un'area](../get-started-create-workspace.md) Azure Synapse lavoro.

# <a name="migrate-from-netezza"></a>[Eseguire la migrazione da Netezza](#tab/migratefromNetezza)

Per eseguire la migrazione del data warehouse Netezza Azure Synapse Analytics, assicurarsi di aver soddisfatto i prerequisiti seguenti:

- Scaricare la versione più recente di [Azure Synapse percorso per](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eseguire la migrazione SQL Server oggetti Azure Synapse oggetti.
- Avere un [pool SQL dedicato in un'area](../get-started-create-workspace.md) Azure Synapse lavoro.

Per altre informazioni, vedere Soluzioni [Azure Synapse Analytics migrazione per Netezza.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)

# <a name="migrate-from-snowflake"></a>[Eseguire la migrazione da Snowflake](#tab/migratefromSnowflake)

Per eseguire la migrazione del data warehouse Snowflake Azure Synapse Analytics, assicurarsi di aver soddisfatto i prerequisiti seguenti:

- Scaricare la versione più recente di [Azure Synapse percorso per](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eseguire la migrazione di oggetti Snowflake Azure Synapse oggetti.
- Avere un [pool SQL dedicato in un'area](../get-started-create-workspace.md) Azure Synapse lavoro.

# <a name="migrate-from-oracle"></a>[Eseguire la migrazione da Oracle](#tab/migratefromOracle)

Per eseguire la migrazione data warehouse oracle Azure Synapse Analytics, assicurarsi di aver soddisfatto i prerequisiti seguenti:

- Avere un carico data warehouse o di analisi.
- Scaricare SQL Server Migration Assistant oracle per convertire gli oggetti Oracle in SQL Server. Per altre informazioni, vedere [Migrazione di database Oracle a SQL Server (OracleToSQL).](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)
- Scaricare la versione più recente di [Azure Synapse per](https://www.microsoft.com/download/details.aspx?id=102787) eseguire la migrazione SQL Server oggetti Azure Synapse oggetti.
- Disporre di un pool SQL dedicato in [un'area](../get-started-create-workspace.md) Azure Synapse lavoro.

Per altre informazioni, vedere [Azure Synapse Analytics soluzioni e migrazione per oracle data warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver deciso di eseguire la migrazione di una soluzione esistente Azure Synapse Analytics, è necessario pianificare la migrazione prima di iniziare. L'obiettivo principale della pianificazione è garantire che i dati, gli schemi di tabella e il codice siano compatibili con Azure Synapse Analytics. Esistono alcune differenze di compatibilità tra il sistema corrente e Azure Synapse Analytics che è necessario risolvere. Inoltre, la migrazione di grandi quantità di dati ad Azure richiede tempo. Un'attenta pianificazione velocizza il processo di recupero dei dati in Azure.

Un altro obiettivo chiave della pianificazione consiste nel modificare la progettazione per garantire che la soluzione sfrutta al meglio le prestazioni elevate delle query Azure Synapse Analytics progettate per fornire. La progettazione di data warehouse per la scalabilità introduce modelli di progettazione univoci, quindi gli approcci tradizionali non sono sempre i migliori. Anche se è possibile apportare alcune modifiche di progettazione dopo la migrazione, apportare modifiche in precedenza nel processo consente di risparmiare tempo in un secondo momento.

## <a name="migrate"></a>Migrate

Per eseguire correttamente la migrazione, è necessario eseguire la migrazione di schemi, codice e dati della tabella. Per indicazioni più dettagliate su questi argomenti, vedere gli articoli seguenti:

- [Prendere in considerazione la progettazione di tabelle](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Prendere in considerazione la modifica del codice](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Eseguire la migrazione dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Prendere in considerazione la gestione dei carichi di lavoro](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Altre risorse

Il team di consulenza clienti ha alcune indicazioni Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) pubblicate come post di blog. Per altre informazioni sulla migrazione, vedere [Migrazione dei dati Azure SQL Data Warehouse in pratica](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migrazione di asset da engagement reali

Per altre informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti. Sono stati sviluppati a supporto di un impegno reale per il progetto di migrazione.

| Titolo/collegamento                              | Descrizione                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento offre piattaforme di destinazione "più adatte", idoneità per il cloud e livello di correzione dell'applicazione o del database per un determinato carico di lavoro. Offre un calcolo semplice e con un clic e la generazione di report che consente di accelerare le valutazioni di grandi dimensioni offrendo un processo decisionale automatizzato e uniforme per la piattaforma di destinazione. |
| [Gestione dei problemi di codifica dei dati durante il caricamento dei dati in Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Questo post di blog fornisce informazioni dettagliate su alcuni dei problemi di codifica dei dati che possono verificarsi durante l'uso di PolyBase per caricare i dati in SQL Data Warehouse. Questo articolo fornisce anche alcune opzioni che è possibile usare per risolvere tali problemi e caricare correttamente i dati. |
| [Recupero delle dimensioni delle tabelle Azure Synapse Analytics pool SQL dedicato](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Una delle attività principali che un architetto deve eseguire è ottenere le metriche relative a un nuovo ambiente dopo la migrazione. Ad esempio, la raccolta dei tempi di caricamento dall'ambiente locale al cloud e la raccolta dei tempi di caricamento di PolyBase. Una delle attività più importanti è determinare le dimensioni di archiviazione in SQL Data Warehouse rispetto alla piattaforma corrente del cliente. |
| [Utilità per spostare gli account di SQL Server locali in Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Uno script di PowerShell crea uno script di comando T-SQL per creare nuovamente gli account di accesso e selezionare gli utenti del database da un'istanza locale di SQL Server a un servizio Azure SQL platform as a service (PaaS). Lo strumento consente il mapping automatico degli account Windows Server Active Directory agli account Azure Active Directory oppure può eseguire ricerche UPN per ogni account di accesso in Windows Server Active Directory. Facoltativamente, lo strumento sposta SQL Server anche gli account di accesso nativi. I ruoli del server e del database personalizzati vengono associati a script, insieme all'appartenenza ai ruoli, al ruolo del database e alle autorizzazioni utente. I database indipendenti non sono supportati e viene eseguito lo script solo un subset SQL Server autorizzazioni. Altre informazioni sono disponibili nel documento di supporto e lo script include commenti per facilitare la comprensione. |

Il team di data sql engineering ha sviluppato queste risorse. Lo scopo principale di questo team è sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Azure di Microsoft.

## <a name="videos"></a>Video

Osservare come [Walgreens ha eseguito la migrazione](https://www.youtube.com/watch?v=86dhd8N1lH4) del sistema di inventario delle vendite al dettaglio con circa 100 TB di dati da Netezza a Azure Synapse Analytics tempo record.
