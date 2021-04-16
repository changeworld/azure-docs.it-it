---
title: 'SQL Server da Istanza gestita di SQL di Azure: Baseline delle prestazioni'
description: Informazioni su come creare e confrontare una baseline delle prestazioni durante la migrazione dei database SQL Server a Istanza gestita di SQL di Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389707"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Prestazioni della migrazione: da SQL Server a Istanza gestita di SQL di Azure di base delle prestazioni
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Creare una baseline delle prestazioni per confrontare le prestazioni del carico di lavoro in un Istanza gestita SQL con il carico di lavoro originale in esecuzione SQL Server. 

## <a name="create-a-baseline"></a>Creare una baseline

Le prestazioni sono idealmente simili o migliori dopo la migrazione, quindi è importante misurare e registrare i valori delle prestazioni di base nell'origine e quindi confrontarli con l'ambiente di destinazione. Una baseline delle prestazioni è un set di parametri che definiscono il carico di lavoro medio nell'origine. 

Selezionare un set di query importanti e rappresentative del carico di lavoro aziendale. Misurare e documentare la durata minima/media/massima e l'utilizzo della CPU per queste query, nonché le metriche delle prestazioni nel server di origine, ad esempio l'utilizzo medio/massimo della CPU, la latenza media/massima di I/O del disco, la velocità effettiva, le operazioni di I/O al secondo, l'aspettativa di vita media/massima delle pagine e le dimensioni massime medie di tempdb. 

Le risorse seguenti consentono di definire una baseline delle prestazioni: 

   - [Monitorare l'utilizzo della CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorare l'utilizzo della memoria](/sql/relational-databases/performance-monitor/monitor-memory-usage)   e determinare la quantità di memoria usata dai diversi componenti, ad esempio pool di buffer, cache dei piani, pool di archivi colonne, [OLTP in](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)memoria e così via. Inoltre, è necessario trovare i valori medi e massimi del contatore delle prestazioni Dell'aspettativa di vita della pagina. 
   - Monitorare l'utilizzo di I/O del disco SQL Server'istanza di origine usando [la sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)o   i [contatori delle prestazioni](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Monitorare le prestazioni del carico di lavoro e delle query esaminando le viste a gestione dinamica (o Query Store se si esegue la migrazione da SQL Server 2016 e versioni successive). Identificare la durata media e l'utilizzo della CPU delle query più importanti nel carico di lavoro. 

Prima della migrazione, è consigliabile risolvere SQL Server problemi di prestazioni nell'origine. La migrazione di problemi noti a qualsiasi nuovo sistema potrebbe causare risultati imprevisti e invalidare qualsiasi confronto delle prestazioni. 


## <a name="compare-performance"></a>Confrontare le prestazioni 

Dopo aver definito una baseline, confrontare prestazioni del carico di lavoro simili nel database SQL di Istanza gestita. Per l'accuratezza, è importante che l'ambiente Istanza gestita SQL sia il più possibile paragonabile all'ambiente SQL Server locale. 

Esistono differenze di infrastruttura Istanza gestita SQL che rendono le prestazioni corrispondenti esattamente improbabili. Alcune query possono essere eseguite più velocemente del previsto, mentre altre potrebbero essere più lente. L'obiettivo di questo confronto è verificare che le prestazioni del carico di lavoro nell'istanza gestita corrispondano alle prestazioni in SQL Server (in media) e identificare le query critiche con prestazioni che non corrispondono alle prestazioni originali. 

Il confronto delle prestazioni può comportare i risultati seguenti: 

- Le prestazioni del carico di lavoro nell'istanza gestita sono allineate o migliori rispetto alle prestazioni del carico di lavoro nell'origine SQL Server. In questo caso, è stato confermato che la migrazione ha esito positivo. 

- La maggior parte dei parametri delle prestazioni e delle query nel carico di lavoro viene eseguita come previsto, con alcune eccezioni che causano prestazioni ridotte. In questo caso, identificare le differenze e la relativa importanza. Se sono presenti query importanti con prestazioni ridotte, verificare se i piani SQL sottostanti sono stati modificati o se le query stanno per raggiungere i limiti delle risorse. È possibile attenuare questo problema applicando alcuni hint alle query critiche (ad esempio, modificare il livello di compatibilità, lo strumento di stima della cardinalità legacy) direttamente o usando guide di piano. Assicurarsi che le statistiche e gli indici siano aggiornati ed equivalenti in entrambi gli ambienti. 

- La maggior parte delle query è più lenta in un'istanza gestita rispetto all'SQL Server di origine. In questo caso, provare a identificare le [](../../managed-instance/resource-limits.md#service-tier-characteristics) cause radice della differenza, ad esempio il raggiungimento di un limite di risorse, ad esempio i limiti di I/O, memoria o frequenza del log dell'istanza. Se non sono presenti limiti di risorse che causano la differenza, provare a modificare il livello di compatibilità del database o modificare le impostazioni del database, ad esempio la stima della cardinalità legacy, ed eseguire nuovamente il test. Esaminare le raccomandazioni fornite dall'istanza gestita o dalle Query Store per identificare le query con prestazioni regredite. 

SQL Istanza gestita dispone di una funzionalità di correzione automatica dei piani incorporata abilitata per impostazione predefinita. Questa funzionalità garantisce che le query che hanno funzionato correttamente in passato non si degradino in futuro. Se questa funzionalità non è abilitata, eseguire il carico di lavoro con le impostazioni predefinite in modo che SQL Istanza gestita possa apprendere la baseline delle prestazioni. Abilitare quindi la funzionalità ed eseguire di nuovo il carico di lavoro con le nuove impostazioni. 

Apportare modifiche ai parametri del test o eseguire l'aggiornamento a livelli di servizio più elevati per raggiungere la configurazione ottimale per le prestazioni del carico di lavoro che soddisfano le esigenze. 

## <a name="monitor-performance"></a>Monitorare le prestazioni 

SQL Istanza gestita offre strumenti avanzati per il monitoraggio e la risoluzione dei problemi ed è consigliabile usarli per monitorare le prestazioni nell'istanza. Alcune delle metriche principali da monitorare sono: 

- Utilizzo della CPU nell'istanza di per determinare se il numero di vCore di cui è stato effettuato il provisioning è la corrispondenza giusta per il carico di lavoro. 
- Aspettativa di durata della pagina nell'istanza gestita per determinare se è necessaria [memoria aggiuntiva.](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)
-  Statistiche come INSTANCE_LOG_GOVERNOR o PAGEIOLATCH che identificano i problemi di I/O di archiviazione, in particolare nel livello per utilizzo generico, in cui potrebbe essere necessario preallocare i file per ottenere prestazioni di I/O migliori. 


## <a name="considerations"></a>Considerazioni  

Quando si confrontano le prestazioni, tenere presente quanto segue: 

- Le impostazioni corrispondono tra l'origine e la destinazione. Verificare che varie impostazioni di istanza, database e tempdb siano equivalenti tra i due ambienti. Le differenze nella configurazione, nei livelli di compatibilità, nelle impostazioni di crittografia, nei flag di traccia e così via possono astrarre tutte le prestazioni. 

- L'archiviazione viene configurata in base [alle procedure consigliate.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Ad esempio, per per utilizzo generico, potrebbe essere necessario preallocare le dimensioni dei file per migliorare le prestazioni. 

- Esistono differenze [di ambiente chiave che](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) possono causare differenze di prestazioni tra un'istanza gestita e SQL Server. Identificare i rischi rilevanti per l'ambiente che potrebbero contribuire a un problema di prestazioni. 

- L'archivio query e l'ottimizzazione automatica devono essere abilitati nel database SQL Istanza gestita consentono di misurare le prestazioni del carico di lavoro e di ridurre automaticamente i potenziali problemi di prestazioni. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come ottimizzare il nuovo Istanza gestita di SQL di Azure, vedere le risorse seguenti: 

- [Come identificare perché le prestazioni del carico di lavoro Istanza gestita di SQL di Azure sono diverse da quelle SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Cause principali delle differenze di prestazioni tra sql Istanza gestita e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Procedure consigliate e considerazioni sulle prestazioni di archiviazione per Istanza gestita di SQL di Azure (per utilizzo generico)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitoraggio delle prestazioni in tempo reale per Istanza gestita di SQL di Azure (questa è archiviata, è la destinazione prevista?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)