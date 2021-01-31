---
title: Procedure consigliate per le prestazioni-database di Azure per MySQL
description: Questo articolo descrive alcune raccomandazioni per monitorare e ottimizzare le prestazioni del database di Azure per MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 46c7952247babd528b230dfa0e70b0eb47878912
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217755"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Procedure consigliate per le prestazioni ottimali del database di Azure per MySQL-server singolo

Informazioni su come ottenere prestazioni ottimali durante l'uso di database di Azure per MySQL-server singolo. Quando si aggiungono nuove funzionalità alla piattaforma, si continuerà a perfezionare i consigli in questa sezione.

## <a name="physical-proximity"></a>Prossimità fisica

 Assicurarsi di distribuire un'applicazione e il database nella stessa area. Un controllo rapido prima di avviare un'esecuzione di benchmark delle prestazioni consiste nel determinare la latenza di rete tra il client e il database usando una semplice query SELECT 1. 

## <a name="accelerated-networking"></a>Rete accelerata

Usare la rete accelerata per il server applicazioni se si usa la macchina virtuale di Azure, Azure Kubernetes o i servizi app. La funzionalità Rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le macchine virtuali (VM), migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate esclude l'host dal percorso dati, riducendo così la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più impegnativi nei tipi di VM supportati.

## <a name="connection-efficiency"></a>Efficienza della connessione

La creazione di una nuova connessione è sempre un'attività costosa e dispendiosa in termini di tempo. Quando un'applicazione richiede una connessione al database, l'allocazione delle connessioni al database inattivo esistente viene assegnata in ordine di priorità anziché crearne una nuova.  Di seguito sono riportate alcune opzioni per le buone procedure di connessione:

- **ProxySQL**: USA [ProxySQL](https://proxysql.com/) che fornisce pool di connessioni integrato e [bilancia il carico del carico di lavoro](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) a più repliche di lettura, in base alle esigenze, con eventuali modifiche nel codice dell'applicazione.

- **Heimdall data proxy**: in alternativa, è possibile usare anche Heimdall data proxy, una soluzione proxy proprietaria indipendente dal fornitore. Supporta la memorizzazione nella cache delle query e la suddivisione in lettura/scrittura con il rilevamento del ritardo di replica. È anche possibile fare riferimento a come [accelerare le prestazioni di MySQL con il proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Connessione persistente o Long-Lived**: se l'applicazione dispone di transazioni o query brevi in genere con tempi di esecuzione < 5-10 ms, sostituire le connessioni brevi con le connessioni permanenti. La sostituzione di connessioni brevi con connessioni permanenti richiede solo modifiche minime al codice, ma ha un effetto significativo in termini di miglioramento delle prestazioni in molti scenari di applicazione tipici. Assicurarsi di impostare il timeout o chiudere la connessione al termine della transazione.

- **Replica**: se si usa la replica, usare [ProxySQL](https://proxysql.com/) per bilanciare il carico tra il server primario e il server di replica secondaria leggibile. Informazioni su [come configurare ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Configurazioni di importazione dati

- È possibile ridimensionare temporaneamente l'istanza a dimensioni dello SKU superiori prima di avviare un'operazione di importazione dei dati e quindi ridimensionarla quando l'importazione ha esito positivo.
- È possibile importare i dati con tempi di inattività minimi usando il [servizio migrazione del database di Azure](https://datamigration.microsoft.com/) per le migrazioni in linea o non in linea. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Consigli sulla memoria per database di Azure per MySQL

Una procedura consigliata per le prestazioni di database di Azure per MySQL consiste nell'allocare una quantità di RAM sufficiente, in modo che i working set risiedano quasi completamente in memoria. 

- Controllare se la percentuale di memoria usata per raggiungere i [limiti](./concepts-pricing-tiers.md) usando le [metriche per il server MySQL](./concepts-monitoring.md). 
- Configurare gli avvisi per tali numeri per assicurarsi che, quando i server raggiungono i limiti, è possibile eseguire le azioni richieste per risolvere il problema. In base ai limiti definiti, controllare se la scalabilità verticale dello SKU del database è superiore, a una dimensione di calcolo superiore o a un piano tariffario migliore, il che comporta un notevole aumento delle prestazioni. 
- Scalabilità verticale fino a quando i numeri delle prestazioni non vengono più eliminati in modo significativo dopo un'operazione di ridimensionamento. Per informazioni sul monitoraggio delle metriche di un'istanza di database, vedere [metriche del database MySQL](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>USA riscaldamento del pool di buffer InnoDB

Dopo aver riavviato il database di Azure per il server MySQL, le pagine di dati che risiedono nello spazio di archiviazione vengono caricate quando viene eseguita una query sulle tabelle, con conseguente aumento della latenza e delle prestazioni più lente per la prima esecuzione delle query. Questa operazione potrebbe non essere accettabile per i carichi di lavoro sensibili alla latenza. 

Utilizzando il riscaldamento del pool di buffer InnoDB, il periodo di riscaldamento viene ridotto ricaricando le pagine del disco presenti nel pool di buffer prima del riavvio anziché attendere DML o selezionare le operazioni per accedere alle righe corrispondenti.

È possibile ridurre il periodo di riscaldamento dopo il riavvio del database di Azure per il server MySQL, che rappresenta un vantaggio in termini di prestazioni configurando i [parametri del server del pool di buffer InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). InnoDB salva una percentuale delle pagine utilizzate più di recente per ogni pool di buffer all'arresto del server e ripristina queste pagine all'avvio del server.

È inoltre importante notare che il miglioramento delle prestazioni è a scapito del tempo di avvio più lungo per il server. Quando questo parametro è abilitato, l'avvio del server e l'ora di riavvio dovrebbero aumentare a seconda del IOPS sottoposto a provisioning nel server. 

Si consiglia di testare e monitorare il tempo di riavvio per garantire che le prestazioni di avvio/riavvio siano accettabili poiché il server non è disponibile durante tale periodo di tempo. Non è consigliabile usare questo parametro con meno di 1000 IOPS con provisioning (o, in altre parole, quando il provisioning dell'archiviazione è inferiore a 335 GB).

Per salvare lo stato del pool di buffer al momento dell'arresto del server, impostare il parametro Server `innodb_buffer_pool_dump_at_shutdown` su `ON` . In modo analogo, impostare il parametro Server `innodb_buffer_pool_load_at_startup` su `ON` per ripristinare lo stato del pool di buffer all'avvio del server. È possibile controllare l'effetto sul tempo di avvio/riavvio abbassando e ottimizzando il valore del parametro Server `innodb_buffer_pool_dump_pct` . Per impostazione predefinita, il parametro è impostato su `25`.

> [!Note]
> I parametri di riscaldamento del pool di buffer InnoDB sono supportati solo nei server di archiviazione per utilizzo generico con archiviazione fino a 16 TB. Per altre informazioni sulle [Opzioni di archiviazione per database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage), vedere qui.

## <a name="next-steps"></a>Passaggi successivi

- [Procedura consigliata per le operazioni server con database di Azure per MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Procedura consigliata per il monitoraggio del database di Azure per MySQL](concept-monitoring-best-practices.md)<br/>
- [Introduzione a database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>