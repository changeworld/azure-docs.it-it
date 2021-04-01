---
title: Procedure consigliate per lo sviluppo di app-database di Azure per PostgreSQL
description: Informazioni sulle procedure consigliate per la compilazione di un'app con database di Azure per PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364602"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Procedure consigliate per la compilazione di un'applicazione con database di Azure per PostgreSQL

Di seguito sono riportate alcune procedure consigliate per la creazione di un'applicazione predisposta per il cloud con database di Azure per PostgreSQL. Queste procedure consigliate possono ridurre i tempi di sviluppo per l'app.

## <a name="configuration-of-application-and-database-resources"></a>Configurazione delle risorse dell'applicazione e del database

### <a name="keep-the-application-and-database-in-the-same-region"></a>Mantieni l'applicazione e il database nella stessa area
Quando si distribuisce l'applicazione in Azure, verificare che tutte le dipendenze si trovino nella stessa area. La distribuzione di istanze tra aree o zone di disponibilità crea una latenza di rete che può influire sulle prestazioni complessive dell'applicazione.

### <a name="keep-your-postgresql-server-secure"></a>Mantieni sicuro il server PostgreSQL
Configurare il server PostgreSQL in modo che sia [protetto](./concepts-security.md) e non accessibile pubblicamente. Usare una di queste opzioni per proteggere il server:
- [Regole del firewall](./concepts-firewall-rules.md)
- [Reti virtuali](./concepts-data-access-and-security-vnet.md)
- [Collegamento privato di Azure](./concepts-data-access-and-security-private-link.md)

Per la sicurezza, è sempre necessario connettersi al server PostgreSQL tramite SSL e configurare il server PostgreSQL e l'applicazione per l'uso di TLS 1,2. Vedere [come configurare SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Ottimizzare i parametri del server
Per i carichi di lavoro con utilizzo intensivo di lettura `tmp_table_size` che `max_heap_table_size` ottimizzano i parametri del server e consentono di ottimizzare le prestazioni. Per calcolare i valori richiesti per queste variabili, esaminare i valori totali della memoria per connessione e la memoria di base. Somma dei parametri di memoria per connessione, esclusi `tmp_table_size` , combinati con gli account di memoria di base per la memoria totale del server.

### <a name="use-environment-variables-for-connection-information"></a>Usare le variabili di ambiente per le informazioni di connessione
Non salvare le credenziali del database nel codice dell'applicazione. A seconda dell'applicazione front-end, seguire le indicazioni per configurare le variabili di ambiente. Per informazioni sull'uso del servizio app, vedere[come configurare le impostazioni dell'app](../app-service/configure-common.md#configure-app-settings) e per il servizio Azure Kuberentes, vedere [come usare i segreti Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/).

## <a name="performance-and-resiliency"></a>Prestazioni e resilienza
Ecco alcuni strumenti e procedure che è possibile usare per facilitare il debug dei problemi di prestazioni con l'applicazione.

### <a name="use-connection-pooling"></a>Utilizzare il pool di connessioni
Con il pool di connessioni, un set fisso di connessioni viene stabilito al momento dell'avvio e mantenuto. Ciò consente inoltre di ridurre la frammentazione della memoria nel server causata dalle nuove connessioni dinamiche stabilite sul server di database. Il pool di connessioni può essere configurato sul lato applicazione se il Framework app o il driver del database lo supporta. Se questa opzione non è supportata, l'altra opzione consigliata consiste nell'utilizzare un servizio pool di connessione proxy come [PgBouncer](https://pgbouncer.github.io/) o [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) in esecuzione all'esterno dell'applicazione e la connessione al server di database. Sia PgBouncer che pgpool sono strumenti basati su community che funzionano con database di Azure per PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Logica di ripetizione dei tentativi per gestire gli errori temporanei
È possibile che l'applicazione riscontri errori temporanei in cui le connessioni al database vengono eliminate o perse in modo intermittente. In tali situazioni, il server è attivo e in esecuzione dopo uno o due tentativi tra 5 e 10 secondi. È consigliabile attendere 5 secondi prima del primo tentativo. Quindi, seguire ogni nuovo tentativo aumentando gradualmente l'attesa, fino a 60 secondi. Limitare il numero massimo di tentativi a cui l'applicazione considera l'operazione non riuscita, quindi è possibile approfondire ulteriormente la ricerca. Per ulteriori informazioni [, vedere come risolvere gli errori di connessione](./concepts-connectivity.md) .

### <a name="enable-read-replication-to-mitigate-failovers"></a>Abilitare la replica di lettura per attenuare i failover
È possibile utilizzare [replica dei dati in ingresso](./concepts-read-replicas.md) per gli scenari di failover. Quando si usano le repliche di lettura, non viene eseguito alcun failover automatico tra i server di origine e di replica. Si noterà un ritardo tra l'origine e la replica perché la replica è asincrona. Il ritardo di rete può essere influenzato da molti fattori, ad esempio le dimensioni del carico di lavoro in esecuzione nel server di origine e la latenza tra i Data Center. Nella maggior parte dei casi, il ritardo di replica varia da alcuni secondi a un paio di minuti.


## <a name="database-deployment"></a>Distribuzione di database

### <a name="configure-cicd-deployment-pipeline"></a>Configurare la pipeline di distribuzione CI/CD
In alcuni casi, è necessario distribuire le modifiche nel database. In questi casi, è possibile usare l'integrazione continua (CI) tramite [azioni di GitHub](https://github.com/Azure/postgresql/blob/master/README.md) per il server PostgreSQL per aggiornare il database eseguendo uno script personalizzato su di esso.

### <a name="define-manual-database-deployment-process"></a>Definire il processo di distribuzione manuale del database
Durante la distribuzione manuale del database, attenersi alla seguente procedura per ridurre al minimo i tempi di inattività o ridurre il rischio di una distribuzione non riuscita:

- Creare una copia di un database di produzione in un nuovo database utilizzando pg_dump.
- Aggiornare il nuovo database con le nuove modifiche dello schema o gli aggiornamenti necessari per il database.
- Posizionare il database di produzione in uno stato di sola lettura. Non è necessario eseguire operazioni di scrittura nel database di produzione fino al completamento della distribuzione.
- Testare l'applicazione con il database appena aggiornato dal passaggio 1.
- Distribuire le modifiche apportate all'applicazione e assicurarsi che l'applicazione stia ora usando il nuovo database con gli aggiornamenti più recenti.
- Conservare il vecchio database di produzione in modo che sia possibile eseguire il rollback delle modifiche. È quindi possibile valutare di eliminare il database di produzione precedente o di esportarlo in archiviazione di Azure, se necessario.

>  [!NOTE]
> Se l'applicazione è simile a un'app di e-commerce e non è possibile inserirla nello stato di sola lettura, distribuire le modifiche direttamente nel database di produzione dopo avere eseguito un backup. Queste modifiche devono verificarsi durante gli orari di minore utilizzo con traffico ridotto all'app per ridurre al minimo l'effetto, perché alcuni utenti potrebbero riscontrare richieste non riuscite. Verificare che il codice dell'applicazione gestisca anche le richieste non riuscite.

## <a name="database-schema-and-queries"></a>Schema di database e query
Ecco alcuni suggerimenti da tenere presenti durante la compilazione dello schema del database e delle query.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Usare BIGINT o UUID per le chiavi primarie
Quando si compila un'applicazione personalizzata o alcuni Framework che potrebbero usare `INT` anziché `BIGINT` per le chiavi primarie. Quando si utilizza ```INT``` , è possibile eseguire il rischio che il valore nel database superi la capacità di archiviazione del ```INT``` tipo di dati. L'esecuzione di questa modifica in un'applicazione di produzione esistente può richiedere molto tempo con costi di sviluppo. Un'altra opzione consiste nell'usare [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) per le chiavi primarie. Questo identificatore utilizza, ad esempio, una stringa a 128 bit generata automaticamente ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Altre informazioni sui [tipi di dati PostgreSQL](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Usa indici

Sono disponibili molti tipi di [indici](https://www.postgresql.org/docs/9.1/indexes.html) in Postgres che possono essere usati in modi diversi. L'utilizzo di un indice consente al server di trovare e recuperare righe specifiche molto più velocemente rispetto a un indice. Tuttavia, anche gli indici aggiungono overhead al server di database, evitando così la presenza di indici troppo numerosi.

### <a name="use-autovacuum"></a>USA autovacuum
È possibile ottimizzare il server con autovacuum in un database di Azure per il server PostgreSQL. PostgreSQL consente una maggiore concorrenza di database ma con ogni aggiornamento i risultati vengono inseriti ed eliminati. Per l'eliminazione, i record vengono contrassegnati come soft, che verranno eliminati in un secondo momento. Per eseguire queste attività, PostgreSQL esegue un processo vacuum. Se i processi non vengono eseguiti con una certa frequenza, le tuple inattive che si accumulano possono provocare i problemi seguenti:

- Aumento della quantità di dati, ad esempio database e tabelle di dimensioni maggiori.
- Indici di dimensioni maggiori non ottimali.
- Aumento delle operazioni di I/O.

Altre informazioni su [come eseguire l'ottimizzazione con autovacuum](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Usare pg_stats_statements
Pg_stat_statements è un'estensione di PostgreSQL abilitata per impostazione predefinita in Database di Azure per PostgreSQL. Questa estensione consente di tenere traccia delle statistiche di esecuzione per tutte le istruzioni SQL eseguite da un server. Vedere [come usare pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Usare Query Store
La funzionalità [Query Store](./concepts-query-store.md) di Database di Azure per PostgreSQL offre un metodo più efficace per tenere traccia delle statistiche query. È consigliabile usare questa funzionalità come alternativa a pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Ottimizzare gli inserimenti bulk e utilizzare dati temporanei
In caso di operazioni di carico di lavoro che coinvolgono dati temporanei o che prevedono l'inserimento bulk di grandi set di dati, prendere in considerazione l'uso di tabelle non registrate. Per impostazione predefinita, offre atomicità e durabilità. Atomicità, coerenza, isolamento e durabilità costituiscono le proprietà ACID. Vedere [come ottimizzare gli inserimenti bulk](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Passaggi successivi
[Guida per Postgres](http://postgresguide.com/)
