---
title: 'SQL Server ad Azure sinapsi Analytics: Guida alla migrazione'
description: Seguire questa guida per eseguire la migrazione dei database SQL al pool SQL di Azure sinapsi Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 09914b409c7d8412f6ba30d4412e28e264bd50f6
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225791"
---
# <a name="migration-guide-sql-server-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Guida alla migrazione: SQL Server a un pool SQL dedicato in Azure sinapsi Analytics 
Le sezioni seguenti forniscono una panoramica degli elementi necessari per la migrazione di una soluzione di data warehouse SQL Server esistente al pool SQL di Azure sinapsi Analytics

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

## <a name="prerequisites"></a>Prerequisiti
Per eseguire la migrazione del SQL Server ad Azure sinapsi Analytics, verificare che siano soddisfatti i prerequisiti seguenti: 

- Un carico di lavoro data warehouse o Analytics 
- Versione più recente dello strumento di [percorso delle sinapsi di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) per eseguire la migrazione di oggetti SQL Server agli oggetti sinapsi di Azure.
- Un [pool SQL dedicato](../get-started-create-workspace.md) nell'area di lavoro sinapsi di Azure. 

## <a name="pre-migration"></a>Pre-migrazione
Dopo aver deciso di eseguire la migrazione di una soluzione esistente ad Azure sinapsi Analytics, è importante pianificare la migrazione prima di iniziare. Un obiettivo principale della pianificazione è garantire che i dati, gli schemi di tabella e il codice siano compatibili con l'analisi delle sinapsi di Azure. Esistono alcune differenze di compatibilità tra il sistema corrente e SQL Data Warehouse che è necessario aggirare. Inoltre, la migrazione di grandi quantità di dati in Azure richiede tempo. Un'attenta pianificazione consente di velocizzare il processo di recupero dei dati in Azure. Un altro obiettivo principale della pianificazione consiste nel modificare la progettazione per assicurarsi che la soluzione sfrutti pienamente le prestazioni di query elevate che Azure sinapsi Analytics è progettato per fornire. La progettazione di data warehouse per la scalabilità introduce modelli di progettazione univoci, quindi gli approcci tradizionali non sono sempre il migliore. Sebbene sia possibile apportare alcune modifiche alla progettazione dopo la migrazione, apportare modifiche in un secondo momento durante il processo consente di risparmiare tempo.

## <a name="azure-synapse-pathway"></a>Percorso sinapsi di Azure
Uno dei blocchi critici che i clienti affrontano è la conversione del codice SQL quando si esegue la migrazione da un sistema a un altro. Il [percorso delle sinapsi di Azure](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) consente di eseguire l'aggiornamento a una piattaforma di data warehouse moderna automatizzando la conversione del codice del data warehouse esistente. Si tratta di uno strumento gratuito, intuitivo e facile da usare che consente di automatizzare la conversione del codice e di velocizzare la migrazione ad Azure sinapsi Analytics.

## <a name="migrate"></a>Migrate
Per eseguire una migrazione corretta, è necessario eseguire la migrazione degli schemi, del codice e dei dati della tabella. Per istruzioni più dettagliate su questi argomenti, vedere:
- L'articolo [esegue la migrazione degli schemi](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- L'articolo [esegue la migrazione del codice](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- L'articolo [esegue la migrazione dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Risorse aggiuntive 
- CAT (Customer Advisory team) presenta alcune eccezionali linee guida di analisi delle sinapsi di Azure (in precedenza SQL Data Warehouse) pubblicate come post di Blog. Per altre informazioni sulla migrazione, vedere l'articolo relativo alla [migrazione dei dati a Azure SQL data warehouse in pratica](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).
- Per ulteriori informazioni e consigli, consultare il white paper [scegliere il percorso di migrazione del database in Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) .
- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere l'articolo [servizio e strumenti per la migrazione dei dati](https://docs.microsoft.com/azure/dms/dms-tools-matrix). 

## <a name="migration-assets-from-real-world-engagements"></a>Asset di migrazione da Engagement reali
Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione del mondo reale.

| Titolo/collegamento                              | Descrizione                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e generazione di report che consentono di accelerare le valutazioni di grandi dimensioni grazie a un processo decisionale automatizzato e uniforme della piattaforma di destinazione. |
| [Gestione dei problemi di codifica dei dati durante il caricamento dei dati in Azure sinapsi Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Questo Blog ha lo scopo di fornire informazioni su alcuni dei problemi di codifica dei dati che possono verificarsi durante l'uso di polibase per caricare i dati in SQL Data Warehouse. In questo articolo sono inoltre disponibili alcune opzioni che è possibile utilizzare per risolvere tali problemi e caricare correttamente i dati. |
| [Recupero delle dimensioni delle tabelle nel pool SQL di Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Una delle attività principali che un progettista deve eseguire è quello di ottenere le metriche relative a un nuovo ambiente post-migrazione: raccolta dei tempi di caricamento da locale al cloud, raccolta dei tempi di caricamento di base e così via. Di queste attività, uno dei più importanti consiste nel determinare le dimensioni di archiviazione in SQL Data Warehouse rispetto alla piattaforma corrente del cliente. |
| [Utilità per spostare gli account di accesso SQL Server locali in Azure sinapsi Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Uno script di PowerShell che crea uno script di comando T-SQL per ricreare gli account di accesso e selezionare gli utenti del database da un SQL Server locale a un servizio PaaS di Azure SQL. Lo strumento consente il mapping automatico degli account di Windows AD per Azure AD account oppure può eseguire ricerche UPN per ogni account di accesso con le Active Directory Windows locali. Lo strumento, facoltativamente, consente di spostare anche SQL Server account di accesso nativi. I ruoli server e database personalizzati sono inclusi nello script, nonché l'appartenenza al ruolo e le autorizzazioni utente e ruolo del database. I database indipendenti non sono ancora supportati e viene creato uno script solo un subset delle autorizzazioni di SQL Server possibili. ovvero la concessione delle autorizzazioni with Grant non è supportata (alberi delle autorizzazioni complessi). Ulteriori informazioni sono disponibili nel documento di supporto e lo script include commenti per una maggiore facilità di comprensione. |

> [!NOTE]
> Queste risorse sono state sviluppate come parte del programma Data Migration Jumpstart (DM Jumpstart), sponsorizzato dal team di progettazione del gruppo di dati di Azure. La carta di base di DM JumpStart è quella di sbloccare e accelerare la modernizzazione complessa e competere con le opportunità di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure. Se si ritiene che la propria organizzazione sia interessata a partecipare al programma DM JumpStart, contattare il team dell'account e richiedere l'invio di una candidatura.

## <a name="videos"></a>Video
- Per una panoramica della Guida alla migrazione del database di Azure e delle informazioni in esso contenute, vedere il video [su come usare la guida alla migrazione del database](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Per una descrizione dettagliata delle fasi del processo di migrazione e per informazioni dettagliate sugli strumenti e i servizi specifici consigliati per eseguire la valutazione e la migrazione, vedere la [Panoramica video del percorso di migrazione e gli strumenti e i servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).