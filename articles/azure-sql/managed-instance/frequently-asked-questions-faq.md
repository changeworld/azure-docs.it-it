---
title: Domande frequenti
titleSuffix: Azure SQL Managed Instance
description: Domande frequenti su Istanza gestita di SQL di Azure
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 16f937286b967aaea8ec6a16e97835b2de5a0331
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765498"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Domande frequenti su Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo contiene le domande più comuni su [Istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Caratteristiche supportate

**Dove è possibile trovare un elenco delle funzionalità supportate in SQL Istanza gestita?**

Per un elenco delle funzionalità supportate in SQL Istanza gestita, vedere Istanza gestita di SQL di Azure [funzionalità .](../database/features-comparison.md)

Per le differenze di sintassi e comportamento tra Istanza gestita di SQL di Azure e SQL Server, vedere [Differenze di T-SQL rispetto SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Specifiche tecniche, limiti delle risorse e altre limitazioni
 
**Dove è possibile trovare le caratteristiche tecniche e i limiti delle risorse per SQL Istanza gestita?**

Per le caratteristiche di generazione dell'hardware disponibili, vedere [Differenze tecniche nelle generazioni di hardware.](resource-limits.md#hardware-generation-characteristics)
Per i livelli di servizio disponibili e le relative caratteristiche, vedere [Differenze tecniche tra i livelli di servizio](resource-limits.md#service-tier-characteristics).

**Per quale livello di servizio sono idoneo?**

Qualsiasi cliente è idoneo per qualsiasi livello di servizio. Tuttavia, se si vogliono scambiare le licenze esistenti con tariffe scontate su Istanza gestita di SQL di Azure usando [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/), tenere presente che i clienti di SQL Server Enterprise Edition con Software Assurance sono idonei per i livelli di prestazioni [per utilizzo generico](../database/service-tier-general-purpose.md) o [business critical](../database/service-tier-business-critical.md) e i clienti SQL Server Standard Edition con Software Assurance sono idonei solo per il livello di prestazioni per utilizzo generico. Per altre informazioni, vedere [Diritti specifici di AHB.](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)

**Quali tipi di sottoscrizione sono supportati per sql Istanza gestita?**

Per l'elenco dei tipi di sottoscrizione supportati, vedere [Tipi di sottoscrizione supportati](resource-limits.md#supported-subscription-types). 

**Quali aree di Azure sono supportate?**

Le istanze gestite possono essere create nella maggior parte delle aree di Azure. Vedere [Aree supportate per SQL Istanza gestita](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se è necessaria un'istanza gestita in un'area attualmente non supportata, inviare una richiesta di supporto [tramite](../database/quota-increase-request.md)il portale di Azure .

**Esistono limitazioni di quota per le distribuzioni Istanza gestita SQL?**

Istanza gestita ha due limiti predefiniti: il limite al numero di subnet che è possibile usare e un limite al numero di vCore di cui è possibile effettuare il provisioning. I limiti variano a seconda dei tipi di sottoscrizione e delle aree. Per l'elenco delle limitazioni delle risorse a livello di regione in base al tipo di sottoscrizione, vedere la tabella [di Limitazione delle risorse a livello di regione.](resource-limits.md#regional-resource-limitations) Si tratta di limiti soft che possono essere aumentati su richiesta. Se è necessario effettuare il provisioning di più istanze gestite nelle aree correnti, inviare una richiesta di supporto per aumentare la quota usando il portale di Azure. Per altre informazioni, vedere [Request quota increases for Azure SQL Database](../database/quota-increase-request.md) (Richiedere aumenti di quota per il database SQL di Azure).

**È possibile aumentare il limite del numero di database (100) nell'istanza gestita su richiesta?**

No e attualmente non sono disponibili piani di cui è stato eseguito il commit per aumentare il numero di database in SQL Istanza gestita. 

**Dove è possibile eseguire la migrazione se si hanno più di 8 TB di dati?**
È possibile prendere in considerazione la migrazione ad altre versioni di Azure adatte al carico di lavoro: database SQL di Azure [Hyperscale](../database/service-tier-hyperscale.md) [o SQL Server in Macchine virtuali di Azure.](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

**Dove è possibile eseguire la migrazione se si hanno requisiti hardware specifici, ad esempio un rapporto maggiore tra RAM e vCore o più CPU?**
È possibile prendere in considerazione la [migrazione SQL Server in macchine virtuali di Azure](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) o database SQL di Azure ottimizzata per memoria/CPU. [](../database/sql-database-paas-overview.md)

## <a name="known-issues-and-defects"></a>Problemi noti e difetti

**Dove è possibile trovare problemi e difetti noti?**

Per i difetti del prodotto e i problemi noti, vedere [Problemi noti.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Nuove funzionalità

**Dove è possibile trovare le funzionalità più recenti e le funzionalità in anteprima pubblica?**

Per le funzionalità nuove e di anteprima, vedere [Note sulla versione.](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Creare, aggiornare, eliminare o spostare sql Istanza gestita

**Come è possibile effettuare il provisioning di SQL Istanza gestita?**

È possibile effettuare il provisioning di un'istanza [da portale di Azure](instance-create-quickstart.md), [PowerShell,](scripts/create-configure-managed-instance-powershell.md)l'interfaccia della [riga di comando di Azure](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) e i [modelli arm.](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)

**È possibile effettuare il provisioning di istanze gestite in una sottoscrizione esistente?**

Sì, è possibile effettuare il provisioning di Istanza gestita in una sottoscrizione esistente se tale sottoscrizione appartiene ai [tipi di sottoscrizione supportati](resource-limits.md#supported-subscription-types).

**Perché non è stato possibile effettuare il provisioning di Istanza gestita nella subnet quale nome inizia con una cifra?**

Si tratta di una limitazione corrente del componente sottostante che verifica il nome della subnet rispetto all'espressione regolare ^[a-zA-Z_][^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ]*(?<![ \. \s])$. Tutti i nomi che passano l'espressione regolare e sono nomi di subnet validi sono attualmente supportati.

**Come è possibile ridimensionare l'istanza gestita?**

È possibile ridimensionare l'istanza gestita da [portale di Azure,](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation) [PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)interfaccia della riga di comando di [Azure](/cli/azure/sql/mi#az_sql_mi_update) o [modelli arm.](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)

**È possibile spostare il Istanza gestita da un'area a un'altra?**

Sì, è possibile. Per istruzioni, vedere [Spostare le risorse tra aree.](../database/move-resources-across-regions.md)

**Come è possibile eliminare i Istanza gestita?**

È possibile eliminare istanze gestite tramite portale di Azure, [PowerShell,](/powershell/module/az.sql/remove-azsqlinstance)l'interfaccia della riga di comando di [Azure](/cli/azure/sql/mi#az_sql_mi_delete) [o Resource Manager API REST.](/rest/api/sql/managedinstances/delete)

**Quanto tempo è necessario per creare o aggiornare un'istanza o per ripristinare un database?**

Il tempo previsto per creare una nuova istanza gestita o per modificare i livelli di servizio (vCore, archiviazione) dipende da diversi fattori. Vedere [Operazioni di gestione](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Convenzioni di denominazione

**Un'istanza gestita può avere lo stesso nome di un'SQL Server locale?**

La modifica del nome di un'istanza gestita non è supportata.

**È possibile modificare il prefisso della zona DNS?**

Sì, Istanza gestita zona DNS predefinita *.database.windows.net* può essere modificata. 

Per usare un'altra zona DNS anziché l'impostazione predefinita, ad esempio . *contoso.com*: 
- Usare CliConfig per definire un alias. Lo strumento è solo un wrapper delle impostazioni del Registro di sistema, quindi può essere eseguito anche usando criteri di gruppo o uno script.
- Usare *CNAME* con *l'opzione TrustServerCertificate=true.*

## <a name="migration-options"></a>Opzioni di migrazione

**Come è possibile eseguire la migrazione database SQL di Azure pool singolo o elastico a SQL Istanza gestita?**

Istanza gestita offre gli stessi livelli di prestazioni per le dimensioni di calcolo e archiviazione delle altre opzioni di distribuzione database SQL di Azure. Se si vogliono consolidare i dati in una singola istanza o è necessaria semplicemente una funzionalità supportata esclusivamente nell'istanza gestita, è possibile eseguire la migrazione dei dati usando la funzionalità di esportazione/importazione (BACPAC). Ecco altri modi per prendere in considerazione la migrazione del database SQL a SQL Istanza gestita: 
- Uso [dell'origine dati esterna](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Uso [di SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Uso [di BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Come è possibile eseguire la migrazione del database dell'istanza a un singolo database SQL di Azure?**

Un'opzione è [esportare un database in BACPAC](../database/database-export.md) e quindi [importare il file BACPAC](../database/database-import.md). Questo è l'approccio consigliato se il database è inferiore a 100 GB.

[La replica transazionale](replication-two-instances-and-sql-server-configure-tutorial.md) può essere usata  se tutte le tabelle del database hanno chiavi primarie e non sono presenti oggetti OLTP in memoria nel database.

Non COPY_ONLY possibile ripristinare i backup nativi di un'istanza gestita in SQL Server perché l'istanza gestita ha una versione del database più recente rispetto a SQL Server. Per altri dettagli, vedere [Backup di sola copia.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Come è possibile eseguire la migrazione dell'istanza SQL Server a SQL Istanza gestita?**

Per eseguire la migrazione dell'istanza SQL Server, vedere SQL Server [migrazione dell'istanza a Istanza gestita di SQL di Azure](migrate-to-instance-from-sql-server.md).

**Come è possibile eseguire la migrazione da altre piattaforme a SQL Istanza gestita?**

Per informazioni sulla migrazione da altre piattaforme, vedere [Azure Database Migration Guide](https://datamigration.microsoft.com/) (Guida alla migrazione di database di Azure).

## <a name="switch-hardware-generation"></a>Generazione dell'hardware del commutatore 

**È possibile cambiare la generazione dell'hardware dell'istanza gestita tra gen 4 e generazione 5 online?**

Il passaggio online automatico da Gen4 a Gen5 è possibile se l'hardware gen5 è disponibile nell'area in cui viene effettuato il provisioning dell'istanza gestita. In questo caso, è possibile controllare la pagina [di panoramica del modello vCore](../database/service-tiers-vcore.md) che spiega come passare da una generazione di hardware all'altro.

Si tratta di un'operazione a esecuzione lunga, in quanto verrà eseguito il provisioning di una nuova istanza gestita in background e i database verranno trasferiti automaticamente tra la vecchia e la nuova istanza con un failover rapido alla fine del processo.

Nota: l'hardware di generazione 4 è in fase di uscita e non è più disponibile per le nuove distribuzioni. Tutti i nuovi database devono essere distribuiti nell'hardware di prima generazione. Anche il passaggio da Gen5 a Gen4 non è disponibile.

## <a name="performance"></a>Prestazioni 

**Come è possibile confrontare le Istanza gestita prestazioni con SQL Server prestazioni?**

Per un confronto delle prestazioni tra istanza gestita e SQL Server, un buon punto di partenza è Procedure consigliate per il confronto delle prestazioni tra Azure SQL istanza gestita e SQL Server [articolo.](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)

**Cosa causa differenze di prestazioni tra Istanza gestita e SQL Server?**

Vedere [Cause principali delle differenze di prestazioni tra Istanza gestita di SQL e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Per altre informazioni sull'impatto sulle dimensioni del file di log per utilizzo generico Istanza gestita prestazioni, vedere Impatto delle dimensioni del file di [log per utilizzo generico](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Ricerca per categorie ottimizzare le prestazioni dell'istanza gestita?**

È possibile ottimizzare le prestazioni dell'istanza gestita:
- [Ottimizzazione automatica che](../database/automatic-tuning-overview.md) offre prestazioni massime e carichi di lavoro stabili tramite l'ottimizzazione continua delle prestazioni basata su intelligenza artificiale e Machine Learning.
-    [OLTP in memoria che migliora](../in-memory-oltp-overview.md) la velocità effettiva e la latenza nei carichi di lavoro di elaborazione transazionale e offre informazioni aziendali più veloci. 

Per ottimizzare ulteriormente le prestazioni, è  consigliabile applicare alcune delle procedure consigliate per l'ottimizzazione di [applicazioni e database.](../database/performance-guidance.md#tune-your-database)
Se il carico di lavoro è costituito da un numero elevato di transazioni di piccole dimensioni, provare a passare dal [proxy](connection-types-overview.md#changing-connection-type) alla modalità di reindirizzamento per ridurre la latenza e aumentare la velocità effettiva.

## <a name="monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi

**Quali sono le opzioni per il monitoraggio e gli avvisi per l'istanza gestita?**

Per tutte le opzioni possibili per monitorare e inviare avvisi sul consumo e sulle prestazioni Istanza gestita SQL, vedere il post Istanza gestita di SQL di Azure di blog sulle opzioni [di monitoraggio.](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416) Per il monitoraggio delle prestazioni in tempo reale per SQL MI, vedere Monitoraggio delle prestazioni in tempo reale per Azure SQL [database Istanza gestita](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**È possibile usare SQL Profiler per il rilevamento delle prestazioni?**

Sì, SQL Profiler è supportato o sql Istanza gestita. Per altre informazioni, vedere [SQL Profiler.](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)

**Sono Advisor per database e Informazioni dettagliate prestazioni query per i Istanza gestita database?**

No, non sono supportati. È possibile usare [DMV](../database/monitoring-with-dmvs.md) [e Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) con [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) e [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) per monitorare i database.

**È possibile creare avvisi delle metriche in SQL Istanza gestita?**

Sì. Per istruzioni, vedere [Creare avvisi per SQL Istanza gestita](alerts-create.md).

**È possibile creare avvisi delle metriche in un database in un'istanza gestita?**

Non è possibile, le metriche di avviso sono disponibili solo per l'istanza gestita. Le metriche di avviso per i singoli database nell'istanza gestita non sono disponibili.

## <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

**Qual è la dimensione massima di archiviazione per SQL Istanza gestita?**

Le dimensioni di archiviazione Istanza gestita sql dipende dal livello di servizio selezionato (per utilizzo generico o business critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [Caratteristiche del livello di servizio](../database/service-tiers-general-purpose-business-critical.md).

**Quali sono le dimensioni minime di archiviazione disponibili per un'istanza gestita?**

La quantità minima di spazio di archiviazione disponibile in un'istanza è di 32 GB. L'archiviazione può essere aggiunta in incrementi di 32 GB fino alle dimensioni massime dello spazio di archiviazione. I primi 32 GB sono gratuiti.

**È possibile aumentare lo spazio di archiviazione assegnato a un'istanza, indipendentemente dalle risorse di calcolo?**

Sì, è possibile acquistare in qualche misura l'archiviazione dei componenti aggiuntivi, indipendentemente dal calcolo. Vedere *Archiviazione riservata dell'istanza* massima nella [tabella](resource-limits.md#hardware-generation-characteristics).

**Come è possibile ottimizzare le prestazioni di archiviazione nel per utilizzo generico di servizio?**

Per ottimizzare le prestazioni di archiviazione, vedere [Procedure consigliate per l'archiviazione in per utilizzo generico](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525).

## <a name="backup-and-restore"></a>Backup e ripristino

**L'archiviazione di backup viene dedotta dall'archiviazione dell'istanza gestita?**

No, l'archiviazione di backup non viene dedotta dallo spazio di archiviazione dell'istanza gestita. L'archiviazione di backup è indipendente dallo spazio di archiviazione dell'istanza e non ha dimensioni limitate. L'archiviazione di backup è limitata dal periodo di tempo per conservare il backup dei database dell'istanza, configurabile fino a 35 giorni. Per informazioni dettagliate, vedere [Backup automatici.](../database/automated-backups-overview.md)

**Come è possibile visualizzare quando vengono evasi backup automatici nell'istanza gestita?**

Per tenere traccia del momento in cui sono stati eseguiti i backup Istanza gestita, vedere Come tenere traccia del [backup](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)automatico per un Istanza gestita di SQL di Azure .

**Il backup su richiesta è supportato?**

Sì, è possibile creare un backup completo di sola copia nel Archiviazione BLOB di Azure, ma sarà ripristinabile solo in Istanza gestita. Per informazioni dettagliate, vedere [Backup di sola copia.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) Tuttavia, il backup di sola copia è impossibile se il database è crittografato da TDE gestita dal servizio perché il certificato usato per la crittografia non è accessibile. In tal caso, usare la funzionalità di ripristino tempormente utile per spostare il database in un altro Istanza gestita SQL o passare alla chiave gestita dal cliente.

**Il ripristino nativo (da file con estensione bak) Istanza gestita supportato?**

Sì, è supportato e disponibile per le versioni SQL Server 2005 e successive.  Per usare il ripristino nativo, caricare il file con estensione bak nell'archivio BLOB di Azure ed eseguire i comandi T-SQL. Per altre informazioni, vedere [Ripristino nativo dall'URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Continuità aziendale

**I database di sistema vengono replicati nell'istanza secondaria in un gruppo di failover?**

I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Pertanto, gli scenari che dipendono dagli oggetti dei database di sistema saranno impossibili nell'istanza secondaria, a meno che gli oggetti non vengano creati manualmente nel database secondario. Per una soluzione alternativa, vedere [Abilitare scenari dipendenti dall'oggetto dai database di sistema.](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)
 
## <a name="networking-requirements"></a>Requisiti di rete 

**Quali sono i vincoli NSG in ingresso/in uscita correnti nella subnet Istanza gestita rete?**

Le regole NSG e UDR necessarie sono documentate [qui](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e impostate automaticamente dal servizio.
Tenere presente che queste regole sono solo quelle necessarie per la gestione del servizio. Per connettersi a un'istanza gestita e usare funzionalità diverse, è necessario impostare regole aggiuntive specifiche della funzionalità che è necessario gestire.

**Come è possibile impostare le regole del gruppo di sicurezza di rete in ingresso sulle porte di gestione?**

SQL Istanza gestita è responsabile dell'impostazione delle regole sulle porte di gestione. Questa operazione viene ottenuta tramite la funzionalità [denominata configurazione della subnet con supporto del servizio.](connectivity-architecture-overview.md#service-aided-subnet-configuration)
Ciò consente di garantire un flusso ininterrotta del traffico di gestione per soddisfare un contratto di servizio.

**È possibile ottenere gli intervalli IP di origine usati per il traffico di gestione in ingresso?**

Sì. È possibile analizzare il traffico proveniente dal gruppo di sicurezza di rete [configurando Network Watcher di flusso.](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)

**È possibile impostare il gruppo di protezione di rete per controllare l'accesso all'endpoint dati (porta 1433)?**

Sì. Dopo aver effettuato Istanza gestita provisioning di un gruppo di Istanza gestita, è possibile impostare un gruppo di protezione di rete che controlla l'accesso in ingresso alla porta 1433. È consigliabile restringere il più possibile l'intervallo IP.

**È possibile impostare l'infrastruttura di rete o il firewall locale per filtrare il traffico di gestione in uscita in base ai nomi fqdn?**

No. Questa operazione non è supportata per diversi motivi:
-    Il routing del traffico che rappresenta la risposta alla richiesta di gestione in ingresso sarebbe asimmetrico e non potrebbe funzionare.
-    Il routing del traffico verso l'archiviazione potrebbe essere influenzato da vincoli di velocità effettiva e latenza, quindi in questo modo non sarà possibile fornire la qualità e la disponibilità del servizio previste.
-    In base all'esperienza, queste configurazioni sono erre e non supportabili.

**È possibile impostare l'NVA o il firewall per il traffico non di gestione in uscita?**

Sì. Il modo più semplice per ottenere questo risultato è aggiungere una regola 0/0 a una route definita dall'utente associata alla subnet dell'istanza gestita per instradare il traffico attraverso l'NVA.
 
**Quanti indirizzi IP sono necessari per un Istanza gestita?**

La subnet deve avere un numero sufficiente di [indirizzi IP disponibili.](connectivity-architecture-overview.md#network-requirements) Per determinare le dimensioni della subnet della rete virtuale per SQL Istanza gestita, vedere Determinare le dimensioni e l'intervallo della [subnet necessari per Istanza gestita](./vnet-subnet-determine-size.md). 

**Cosa succede se non sono disponibili indirizzi IP sufficienti per eseguire l'operazione di aggiornamento dell'istanza?**

Se nella subnet in cui viene effettuato il provisioning dell'istanza gestita non sono presenti indirizzi [IP](connectivity-architecture-overview.md#network-requirements) sufficienti, sarà necessario creare una nuova subnet e una nuova istanza gestita al suo interno. Si consiglia anche di creare la nuova subnet con più indirizzi IP allocati, in modo da evitare situazioni simili nelle operazioni di aggiornamento future. Dopo aver effettuato il provisioning della nuova istanza, è possibile eseguire manualmente il backup e il ripristino dei dati tra le istanze precedente e nuova oppure eseguire il ripristino temporato tra [istanze.](point-in-time-restore.md?tabs=azure-powershell)

**È necessaria una subnet vuota per creare un Istanza gestita?**

No. È possibile usare una subnet vuota o una subnet che contiene già Istanza gestita una o più subnet. 

**È possibile modificare l'intervallo di indirizzi della subnet?**

Non se sono presenti istanze gestite all'interno. Si tratta di una limitazione dell'infrastruttura di rete di Azure. È possibile aggiungere spazio [indirizzi aggiuntivo solo a una subnet vuota.](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings) 

**È possibile spostare l'istanza gestita in un'altra subnet?**

No. Si tratta di una limitazione di Istanza gestita corrente. È tuttavia possibile effettuare il provisioning di una nuova istanza in un'altra subnet ed eseguire manualmente il backup e il ripristino dei dati tra la vecchia e la nuova istanza oppure eseguire il ripristino temporato tra [istanze.](point-in-time-restore.md?tabs=azure-powershell)

**È necessaria una rete virtuale vuota per creare un Istanza gestita?**

Questa operazione non è obbligatoria. È possibile [creare una rete virtuale per Istanza gestita di SQL di Azure](./virtual-network-subnet-create-arm-template.md) o configurare una rete virtuale esistente per [Istanza gestita di SQL di Azure](./vnet-existing-add-subnet.md).

**È possibile inserire un Istanza gestita con altri servizi in una subnet?**

No. Attualmente non è possibile inserire Istanza gestita in una subnet che contiene già altri tipi di risorse.

## <a name="connectivity"></a>Connettività 

**È possibile connettersi all'istanza gestita usando l'indirizzo IP?**

No, questa operazione non è supportata. Il Istanza gestita host di un Istanza gestita viene mappato al servizio di bilanciamento del carico. Poiché un cluster virtuale può ospitare più istanze gestite, non è possibile instradare una connessione al Istanza gestita senza specificarne il nome.
Per altre informazioni sull'architettura del cluster virtuale Istanza gestita SQL, vedere [Architettura della connettività del cluster virtuale](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**L'istanza gestita può avere un indirizzo IP statico?**

Non supportato attualmente.

In rari casi, ma necessari, potrebbe essere necessario eseguire una migrazione online di un'istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è causata da modifiche dello stack tecnologico che mirano a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP mappato al nome host dell'istanza gestita. Il servizio istanza gestita non richiede il supporto degli indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso come parte dei normali cicli di manutenzione.

Per questo motivo, è fortemente sconsigliato affidarsi all'immutabilità dell'indirizzo IP perché potrebbe causare tempi di inattività non necessari.

**È Istanza gestita un endpoint pubblico?**

Sì. Istanza gestita un endpoint pubblico usato per impostazione predefinita solo per Gestione servizi, ma un cliente può abilitarlo anche per l'accesso ai dati. Per altre informazioni, vedere [Usare sql Istanza gestita con endpoint pubblici.](./public-endpoint-overview.md) Per configurare l'endpoint pubblico, passare [a Configurare l'endpoint pubblico in SQL Istanza gestita](public-endpoint-configure.md).

**In che modo Istanza gestita controllare l'accesso all'endpoint pubblico?**

Istanza gestita controlla l'accesso all'endpoint pubblico a livello di rete e di applicazione.

I servizi di gestione e distribuzione si connettono a un'istanza gestita usando un [endpoint di gestione](./connectivity-architecture-overview.md#management-endpoint) mappato a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set di porte predefinite usate esclusivamente dai componenti di gestione dell'istanza gestita. Per consentire il traffico solo da intervalli IP specifici di Microsoft, viene inoltre configurato un firewall incorporato nei nodi. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite i certificati. Per altri dettagli, vedere [Architettura della connettività per SQL Istanza gestita](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**È possibile usare l'endpoint pubblico per accedere ai dati in Istanza gestita database?**

Sì. Il cliente dovrà abilitare l'accesso ai dati degli endpoint pubblici da [portale di Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShell/ARM e configurare il gruppo di sicurezza di rete per bloccare l'accesso alla porta dati (numero di porta  /  [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) 3342). Per altre informazioni, vedere [Configurare un endpoint pubblico in Istanza gestita di SQL di Azure](public-endpoint-configure.md) e Usare Istanza gestita di SQL di Azure in modo sicuro con [l'endpoint pubblico.](public-endpoint-overview.md) 

**È possibile specificare una porta personalizzata per gli endpoint dati SQL?**

No, questa opzione non è disponibile.  Per l'endpoint dati privato, Istanza gestita usa il numero di porta predefinito 1433 e per l'endpoint dati pubblico, Istanza gestita usa il numero di porta predefinito 3342.

**Qual è il modo consigliato per connettere le istanze gestite posizionate in aree diverse?**

Il peering del circuito Express Route è il modo migliore per eseguire questa operazione. Il peering di rete virtuale globale è supportato con la limitazione descritta nella nota seguente.  

> [!IMPORTANT]
> [Il 22/09/2020 Microsoft ha annunciato il Peering reti virtuali globale per i cluster virtuali appena creati](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ciò significa che il Peering reti virtuali globale è supportato per le Istanze gestite di SQL create nelle subnet vuote dopo la data di annuncio, nonché per tutte le istanze gestite successive create in tali subnet. Per tutte le altre istanze gestite di SQL, il supporto del peering è limitato alle reti nella stessa area a causa dei vincoli del [peering di rete virtuale globale.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Per altri dettagli, vedere anche la sezione pertinente dell'articolo Domande frequenti sulle reti virtuali di [Azure.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 

Se il peering del circuito Express Route e il peering di rete virtuale globale non sono possibili, l'unica altra opzione è creare una connessione VPN da sito a sito ([portale di Azure](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), interfaccia della riga di comando [di Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Attenuare i rischi di esfiltrazione dei dati  

**Come è possibile attenuare i rischi di esfiltrazione dei dati?**

Per ridurre i rischi di esfiltrazione dei dati, è consigliabile applicare un set di impostazioni e controlli di sicurezza:

- Attivare la [Transparent Data Encryption (TDE) in](../database/transparent-data-encryption-tde-overview.md) tutti i database.
- Disattivare Common Language Runtime (CLR). È consigliabile anche in locale.
- Usare Azure Active Directory (Azure AD) solo.
- Accedere all'istanza con un account amministratore di database con privilegi bassi.
- Configurare l'accesso jumpbox JIT per l'account sysadmin.
- Attivare il [controllo SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integrarlo con meccanismi di avviso.
- Attivare Rilevamento [minacce](../database/threat-detection-configure.md) dalla suite [Azure Defender per SQL.](../database/azure-defender-for-sql.md)

## <a name="dns"></a>DNS

**È possibile configurare un DNS personalizzato per SQL Istanza gestita?**

Sì. Vedere [Come configurare un DNS personalizzato per Istanza gestita di SQL di Azure](./custom-dns-configure.md).

**È possibile eseguire l'aggiornamento DNS?**

Sì. Vedere [Sincronizzare l'impostazione dei server DNS](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)di rete virtuale in SQL Istanza gestita cluster virtuale .

## <a name="change-time-zone"></a>Modificare il fuso orario

**È possibile modificare il fuso orario per un'istanza gestita esistente?**

La configurazione del fuso orario può essere impostata quando viene effettuato il provisioning di un'istanza gestita per la prima volta. La modifica del fuso orario di un'istanza gestita esistente non è supportata. Per informazioni dettagliate, vedere [Limitazioni del fuso orario.](timezones-overview.md#limitations)

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario appropriato e quindi l'esecuzione di un backup e un ripristino manuale oppure l'esecuzione di un ripristino temporato tra [istanze.](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)


## <a name="security-and-database-encryption"></a>Sicurezza e crittografia del database

**Il ruolo del server sysadmin è disponibile per SQL Istanza gestita?**

Sì, i clienti possono creare account di accesso membri del ruolo sysadmin.  Anche i clienti che presuppongono il privilegio sysadmin si assume la responsabilità di eseguire l'istanza, il che può influire negativamente sull'impegno del contratto di servizio. Per aggiungere l'account di accesso al ruolo del server sysadmin, [vedere Azure AD authentication](./aad-security-configure-tutorial.md#azure-ad-authentication).

**La Transparent Data Encryption è supportata per SQL Istanza gestita?**

Sì, Transparent Data Encryption è supportato per SQL Istanza gestita. Per informazioni dettagliate, [vedere Transparent Data Encryption per SQL Istanza gestita](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**È possibile sfruttare il modello "Bring Your Own Key" per TDE?**

Sì, Azure Key Vault per lo scenario BYOK è disponibile per Istanza gestita di SQL di Azure. Per informazioni dettagliate, [vedere Transparent Data Encryption con la chiave gestita dal cliente.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

**È possibile eseguire la migrazione di un database SQL Server crittografato?**

Sì, è possibile. Per eseguire la migrazione di un database SQL Server crittografato, è necessario esportare e importare i certificati esistenti in Istanza gestita, quindi eseguire un backup completo del database e ripristinarlo in Istanza gestita. 

È anche possibile usare [Servizio Migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/) per eseguire la migrazione dei database crittografati con TDE.

**Come è possibile configurare la rotazione della protezione TDE per SQL Istanza gestita?**

È possibile ruotare la protezione TDE per Istanza gestita usando Azure Cloud Shell. Per istruzioni, vedere [Transparent Data Encryption in SQL Istanza gestita using your own key from Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**È possibile ripristinare il database crittografato in SQL Istanza gestita?**

Sì, non è necessario decrittografare il database per ripristinarlo in SQL Istanza gestita. Per poter leggere i dati dal file di backup crittografato, è necessario fornire a SQL Istanza gestita un certificato o una chiave usata come protezione con chiave di crittografia nel sistema di origine. È possibile procedere in due modi:

- *Caricare la protezione del certificato in SQL Istanza gestita*. Questa operazione può essere eseguita solo tramite PowerShell. Lo [script di esempio](./tde-certificate-migrate.md) descrive l'intero processo.
- *Caricare la protezione a chiave* asimmetrica Azure Key Vault e puntare sql Istanza gestita a esso . Questo approccio è simile al caso d'uso TDE bring-your-own-key (BYOK) che usa anche l'integrazione Key Vault per archiviare la chiave di crittografia. Se non si vuole usare la chiave come protezione della chiave di crittografia e si vuole solo rendere disponibile la chiave per SQL Istanza gestita per ripristinare i database crittografati, seguire le istruzioni per la configurazione di [BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)e non selezionare la casella di controllo Imposta la chiave selezionata come protezione **TDE predefinita.**

Dopo aver rendere disponibile la protezione della crittografia per sql Istanza gestita, è possibile procedere con la procedura di ripristino del database standard.

## <a name="purchasing-models-and-benefits"></a>Modelli di acquisto e vantaggi

**Quali modelli di acquisto sono disponibili per SQL Istanza gestita?**

SQL Istanza gestita offre un [modello di acquisto basato su vCore.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Quali vantaggi di costo sono disponibili per SQL Istanza gestita?**

È possibile risparmiare sui costi con Azure SQL vantaggi nei modi seguenti:
-    Ottimizzare gli investimenti esistenti nelle licenze locali e risparmiare fino al 55% [con](../azure-hybrid-benefit.md?tabs=azure-powershell)Vantaggio Azure Hybrid . 
-    Eseguire il commit in una prenotazione per le risorse di calcolo e risparmiare fino al 33% con [il vantaggio Istanza riservata](../database/reserved-capacity-overview.md). Combinarlo con il vantaggio Azure Hybrid per risparmiare fino all'82%. 
-    Risparmiare fino al 55% rispetto ai prezzi di listino con il vantaggio Prezzi di [Sviluppo/Test](https://azure.microsoft.com/pricing/dev-test/) di Azure che offre tariffe scontate per i carichi di lavoro di sviluppo e test in corso.

**Chi è idoneo per il vantaggio Istanza riservata?**

Per essere idoneo per il vantaggio Istanza riservata, il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Per altre informazioni sulle prenotazioni, vedere [Vantaggio Istanza riservata](../database/reserved-capacity-overview.md). 

**È possibile annullare, scambiare o rimborsare prenotazioni?**

È possibile annullare, scambiare o rimborsare prenotazioni con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fatturazione per l Istanza gestita e l'archiviazione di backup

**Quali sono le opzioni relative ai prezzi Istanza gestita SQL?**

Per esplorare le Istanza gestita prezzi, vedere la [pagina dei prezzi.](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

**Come è possibile tenere traccia dei costi di fatturazione per l'istanza gestita?**

A tale scopo, è possibile [usare Gestione costi di Azure soluzione](../../cost-management-billing/index.yml). Passare a **Sottoscrizioni** nel [portale di Azure](https://portal.azure.com) e selezionare Analisi dei **costi.** 

Usare **l'opzione Costi accumulati** e quindi filtrare in base al **tipo di risorsa** come `microsoft.sql/managedinstances` .

**Quanto costano i backup automatizzati?**

Si ottiene la stessa quantità di spazio di archiviazione di backup disponibile dello spazio di archiviazione dei dati riservato acquistato, indipendentemente dal periodo di conservazione dei backup impostato. Se l'utilizzo dell'archiviazione di backup rientra nello spazio di archiviazione di backup disponibile allocato, i backup automatizzati nell'istanza gestita non avranno costi aggiuntivi, pertanto saranno gratuiti. Il superamento dell'uso dell'archiviazione di backup al di sopra dello spazio disponibile comporta costi di circa 0,20 - $ 0,24 per GB/mese nelle aree degli Stati Uniti oppure per informazioni dettagliate sulla propria area geografica, vedere la pagina dei prezzi. Per altri dettagli, vedere l'articolo [sull'utilizzo dell'archiviazione di backup illustrato](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)in .

**Come è possibile monitorare i costi di fatturazione per l'utilizzo dell'archiviazione di backup?**

È possibile monitorare i costi per l'archiviazione di backup portale di Azure. Per istruzioni, vedere [Monitorare i costi per i backup automatici.](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs) 

**Come è possibile ottimizzare i costi di archiviazione di backup nell'istanza gestita?**

Per ottimizzare i costi di archiviazione dei backup, vedere [Fine backup tuning on SQL Istanza gestita](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Casi d'uso con risparmio sui costi

**Dove è possibile trovare i casi d'uso e i risparmi sui costi risultanti con SQL Istanza gestita?**

Case study Istanza gestita SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Per comprendere meglio i vantaggi, i costi e i rischi associati alla distribuzione di Istanza gestita di SQL di Azure, è disponibile anche uno studio Forrester: [The Total Economic Impact of database SQL di Microsoft Azure Istanza gestita](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Criteri password 

**Quali criteri password vengono applicati per gli account di accesso SQL Istanza gestita SQL?**

I criteri Istanza gestita password per gli account di accesso SQL ereditano i criteri della piattaforma Azure applicati alle macchine virtuali che formano un cluster virtuale che contiene l'istanza gestita. Al momento non è possibile modificare queste impostazioni perché queste impostazioni sono definite da Azure e ereditate dall'istanza gestita.

 > [!IMPORTANT]
 > La piattaforma Azure può modificare i requisiti dei criteri senza notificare ai servizi che si basano su questi criteri.

**Che cosa sono i criteri correnti della piattaforma Azure?**

Ogni account di accesso deve impostare la propria password al momento dell'accesso e modificarne la password dopo aver raggiunto la validità massima.

| **Criteri** | **Impostazione di sicurezza** |
| --- | --- |
| Validità massima password | 42 giorni |
| Validità minima password | 1 giorno |
| Lunghezza minima password | 10 caratteri |
| Le password devono essere conformi ai requisiti di complessità | Abilitato |

**È possibile disabilitare la complessità e la scadenza delle password in SQL Istanza gestita livello di accesso?**

Sì, è possibile controllare i campi CHECK_POLICY e CHECK_EXPIRATION a livello di accesso. È possibile controllare le impostazioni correnti eseguendo il comando T-SQL seguente:

```sql
SELECT *
FROM sys.sql_logins
```

Successivamente, è possibile modificare le impostazioni di accesso specificate eseguendo :

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(sostituire "test" con il nome di accesso desiderato e modificare i criteri e i valori di scadenza)


## <a name="service-updates"></a>Aggiornamenti del servizio

**Qual è la modifica della CA radice per database SQL di Azure & SQL Istanza gestita?**

Vedere [Rotazione dei certificati per database SQL di Azure & SQL Istanza gestita](../updates/ssl-root-certificate-expiring.md). 

**Che cos'è un evento di manutenzione pianificata per SQL Istanza gestita?**

Vedere [Pianificare eventi di manutenzione di Azure in SQL Istanza gestita](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Commenti e supporto di Azure

**Dove è possibile lasciare le idee per i miglioramenti Istanza gestita SQL?**

È possibile votare per una nuova funzionalità Istanza gestita o inserire una nuova idea di miglioramento per votare nel forum di feedback [di SQL Istanza gestita](https://feedback.azure.com/forums/915676-sql-managed-instance). In questo modo è possibile contribuire allo sviluppo del prodotto e contribuire a classificare in ordine di priorità i potenziali miglioramenti.

**Come è possibile creare supporto tecnico di Azure richiesta?**

Per informazioni su come creare supporto tecnico di Azure richiesta, vedere [Come creare supporto tecnico di Azure richiesta](../../azure-portal/supportability/how-to-create-azure-support-request.md).
