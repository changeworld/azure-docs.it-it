---
title: Procedure consigliate per il pool SQL serverless
description: Raccomandazioni e procedure consigliate per l'uso di pool SQL serverless.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477469"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Procedure consigliate per il pool SQL serverless in Azure Synapse Analytics

Questo articolo contiene una raccolta di procedure consigliate per l'uso di pool SQL serverless. Il pool SQL serverless è una risorsa in Azure Synapse Analytics.

Il pool SQL serverless consente di eseguire query su file negli account di archiviazione di Azure. Non include funzionalità di archiviazione o inserimento in locale. Tutti i file di destinazione della query sono quindi esterni al pool SQL serverless. Tutte le operazioni correlate alla lettura di file dall'archiviazione possono avere un impatto sulle prestazioni delle query.

Alcune linee guida generiche sono:
- Assicurarsi che le applicazioni client siano collocate con il pool SQL serverless.
  - Se si usano applicazioni client all'esterno di Azure (ad esempio Power BI Desktop, SSMS, ADS), assicurarsi di usare il pool serverless in un'area vicina al computer client.
- Assicurarsi che l'archiviazione (Azure Data Lake, Cosmos DB) e il pool SQL serverless si siano nella stessa area.
- Provare a [ottimizzare il layout di](#prepare-files-for-querying) archiviazione usando il partizionamento e mantenendo i file nell'intervallo compreso tra 100 MB e 10 GB.
- Se si restituisce un numero elevato di risultati, assicurarsi di usare SSMS o ADS e non Synapse Studio. Synapse Studio è uno strumento Web non progettato per set di risultati di grandi dimensioni. 
- Se si filtrano i risultati in base alla colonna stringa, provare a usare alcune `BIN2_UTF8` regole di confronto.
- Provare a memorizzare nella cache i risultati sul lato client usando Power BI modalità di importazione o Azure Analysis Services e aggiornarli periodicamente. I pool SQL serverless non possono offrire esperienza interattiva Power BI modalità Direct Query se si usano query complesse o si elaborano grandi quantità di dati.

## <a name="client-applications-and-network-connections"></a>Applicazioni client e connessioni di rete

Assicurarsi che l'applicazione client sia connessa all'area di lavoro Synapse più vicina possibile con la connessione ottimale.
- Spostare un'applicazione client con l'area di lavoro Synapse. Se si usano applicazioni come Power BI o Azure Analysis Service, assicurarsi che si trovano nella stessa area in cui è stata inserita l'area di lavoro synapse. Se necessario, creare le aree di lavoro separate associate alle applicazioni client. L'inserimento di un'applicazione client e dell'area di lavoro Synapse in un'area diversa può causare una latenza maggiore e uno streaming più lento dei risultati.
- Se si leggono dati dall'applicazione locale, assicurarsi che l'area di lavoro Synapse si trova nell'area vicina alla posizione.
- Assicurarsi di non avere problemi di larghezza di banda di rete durante la lettura di una grande quantità di dati.
- Non usare Synapse Studio per restituire una grande quantità di dati. Synapse Studio è uno strumento Web che usa il protocollo HTTPS per trasferire i dati. Usare Azure Data Studio o SQL Server Management Studio leggere una grande quantità di dati.

## <a name="storage-and-content-layout"></a>Layout di archiviazione e contenuto

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Trasferire l'archiviazione e il pool SQL serverless

Per ridurre al minimo la latenza, trasferire l'account di archiviazione di Azure o l'archiviazione analitica di CosmosDB e l'endpoint del pool SQL serverless. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per prestazioni ottimali, se si accede ad altri account di archiviazione con pool SQL serverless, assicurarsi che si siano nella stessa area. Se non si trovano nella stessa area, si verificherà un aumento della latenza per il trasferimento in rete dei dati tra l'area remota e l'area dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione delle richieste di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione dell'archiviazione si verifica quando la velocità effettiva o le operazioni di I/O al secondo combinate generate da applicazioni, servizi e carico di lavoro del pool SQL serverless superano i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni delle query.

Quando viene rilevata la limitazione, il pool SQL serverless dispone di una gestione incorporata per risolverla. Il pool SQL serverless effettua richieste all'archiviazione a un ritmo più lento fino a quando la limitazione non viene risolta.

> [!TIP]
> Per un'esecuzione ottimale delle query, evitare di sovraccaricare l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query.

### <a name="azure-ad-pass-through-performance"></a>Prestazioni del pass-through di Azure AD

Il pool SQL serverless consente di accedere ai file nell'archiviazione usando Azure Active Directory (Azure AD) pass-through o di firma di accesso condiviso. È possibile riscontrare prestazioni inferiori con il pass-through di Azure AD rispetto alla firma di accesso condiviso.

Se sono necessarie prestazioni migliori, provare a usare le credenziali di firma di accesso condiviso per accedere all'archiviazione.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, preparare i file per migliorare le prestazioni:

- Convertire CSV e JSON di grandi dimensioni in Parquet. Parquet è un formato a colonne. Poiché è compresso, le dimensioni dei file sono ridotte rispetto a quelle dei file CSV o JSON che contengono gli stessi dati. Il pool SQL serverless è in grado di ignorare le colonne e le righe che non sono necessarie nella query se si leggono i file Parquet. Il pool SQL serverless richiede meno tempo e meno richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile dividerla in più file di dimensioni ridotte.
- Provare a mantenere le dimensioni del file CSV tra 100 MB e 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o per la proprietà LOCATION di una tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi. Vedere [Usare le funzioni filename e filepath per indicare come destinazione partizioni specifiche](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Spostare l'archiviazione analitica di CosmosDB e il pool SQL serverless

Assicurarsi che l'archiviazione analitica di CosmosDB si trova nella stessa area dell'area di lavoro synapse. Le query tra aree possono causare latenze enormi. Usare la proprietà region nella stringa di connessione per specificare in modo esplicito l'area in cui viene inserito l'archivio analitico (vedere Eseguire query su [CosmosDb usando il pool SQL serverless):](query-cosmos-db-analytical-store.md#overview)

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>Ottimizzazioni CSV

### <a name="use-parser_version-20-to-query-csv-files"></a>Usare PARSER_VERSION 2.0 per eseguire query su file CSV

Quando si eseguono query su file CSV, è possibile usare un parser ottimizzato per le prestazioni. Per informazioni dettagliate, vedere [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Creare manualmente statistiche per i file CSV

Il pool SQL serverless si basa sulle statistiche per generare piani di esecuzione delle query ottimali. Le statistiche verranno create automaticamente per le colonne nei file Parquet quando necessario. Al momento, le statistiche non vengono create automaticamente per le colonne nei file CSV ed è necessario creare manualmente le statistiche per le colonne usate nelle query, in particolare quelle usate in DISTINCT, JOIN, WHERE, ORDER BY e GROUP BY. Per [informazioni dettagliate, controllare le statistiche nel pool SQL serverless.](develop-tables-statistics.md#statistics-in-serverless-sql-pool)


## <a name="data-types"></a>Tipi di dati

### <a name="use-appropriate-data-types"></a>Usare i tipi di dati appropriati

I tipi di dati usati nella query influiscono sulle prestazioni. Per migliorare le prestazioni, seguire queste linee guida: 

- Usare le dimensioni di dati minime che supportano il valore massimo possibile.
  - Se la lunghezza massima del valore in caratteri è 30, usare un tipo di dati carattere di lunghezza 30.
  - Se tutti i valori delle colonne carattere sono di dimensioni fisse, usare **char** o **nchar**. In caso contrario, usare **varchar** o **nvarchar**.
  - Se il valore massimo di una colonna di numeri interi è 500, usare **smallint**, perché è il tipo di caratteri più piccolo in grado di supportare questo valore. Per informazioni sugli intervalli dei tipi di dati di numeri interi, vedere [questo articolo](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Se possibile, usare **varchar** e **char** invece di **nvarchar** e **nchar**.
- Se possibile, usare tipi di dati basati su integer. Le operazioni SORT, JOIN e GROUP BY vengono completate più velocemente su numeri interi che non su dati di tipo carattere.
- Se si usa l'inferenza dello schema, [controllare i tipi di dati dedotti](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Controllare i tipi di dati dedotti

L'[inferenza dello schema](query-parquet-files.md#automatic-schema-inference) consente di scrivere rapidamente query e di esplorare i dati senza conoscere gli schemi dei file. Il costo di questa comodità è che i tipi di dati derivati possono essere più grandi dei tipi di dati effettivi. Questa situazione si verifica quando nei file di origine non sono disponibili informazioni sufficienti per assicurare che venga usato il tipo di dati appropriato. Ad esempio, i file Parquet non contengono metadati sulla lunghezza massima delle colonne di caratteri. Il pool SQL serverless lo deduce quindi come varchar(8000).

È possibile usare [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) per controllare i tipi di dati risultanti della query.

L'esempio seguente mostra come ottimizzare i tipi di dati dedotti. Questa procedura viene usata per visualizzare i tipi di dati dedotti: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Il set di risultati è il seguente:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

Se si conoscono i tipi di dati dedotti per la query, è possibile specificare quelli appropriati:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Ottimizzazione dei filtri

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Eseguire il push di caratteri jolly per ridurre i livelli nel percorso

È possibile usare caratteri jolly nel percorso per [eseguire query su più file e cartelle](query-data-storage.md#query-multiple-files-or-folders). Il pool SQL serverless elenca i file nell'account di archiviazione, a partire dal primo * tramite l'API di archiviazione. Elimina i file che non corrispondono al percorso specificato. La riduzione dell'elenco iniziale di file può migliorare le prestazioni nel caso in cui molti file corrispondano al percorso specificato fino al primo carattere jolly.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni filename e filepath per indicare come destinazione partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile indicare al pool SQL serverless di eseguire query su cartelle e file specifici. Questa operazione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che si otterranno prestazioni più elevate.

Per altre informazioni, vedere le informazioni sulle funzioni [filename](query-data-storage.md#filename-function) e [filepath](query-data-storage.md#filepath-function) e vedere gli esempi relativi a [query su file specifici](query-specific-files.md).

> [!TIP]
> Eseguire sempre il cast dei risultati delle funzioni filepath e filename al tipo di dati appropriato. Se si usano tipi di dati carattere, assicurarsi di usare la lunghezza appropriata.

> [!NOTE]
> Le funzioni usate per l'eliminazione di partizioni, filepath e filename, non sono attualmente supportate per tabelle esterne diverse da quelle create automaticamente per ogni tabella creata in Apache Spark per Azure Synapse Analytics.

Se i dati archiviati non sono partizionati, prendere in considerazione di partizionarli. In questo modo è possibile usare queste funzioni per ottimizzare le query da eseguire su tali file. Quando si [esegue una query Apache Spark](develop-storage-files-spark-tables.md) partizionate per Azure Synapse tabelle dal pool SQL serverless, la query verrà automaticamente impostata come destinazione solo per i file necessari.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Usare regole di confronto appropriate per usare la propagazione del predicato per le colonne di tipo carattere

I dati nel file Parquet sono organizzati in gruppi di righe. Il pool SQL serverless ignora i gruppi di righe in base al predicato specificato nella clausola WHERE e quindi riduce le operazioni di I/O, con un aumento delle prestazioni delle query. 

Si noti che la propagazione del predicato per le colonne di tipo carattere nei file parquet è supportata solo per Latin1_General_100_BIN2_UTF8 regole di confronto. È possibile specificare regole di confronto per una determinata colonna usando la clausola WITH. Se non si specificano queste regole di confronto usando la clausola WITH, verranno usate le regole di confronto del database.

## <a name="optimize-repeating-queries"></a>Ottimizzare le query ripetute

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per ottimizzare le prestazioni e i join delle query

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili nel pool SQL serverless. CETAS è un'operazione parallela che crea metadati di tabelle esterne ed esporta i risultati delle query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per materializzare parti di query usate di frequente, ad esempio tabelle di riferimento unite in join, in un nuovo set di file. È quindi possibile creare un join a questa sola tabella esterna invece di ripetere join comuni in più query.

Quando CETAS genera file Parquet, le statistiche verranno create automaticamente quando la prima query è destinata a questa tabella esterna, con conseguente miglioramento delle prestazioni per le query successive che hanno come destinazione la tabella generata con CETAS.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo sulla [risoluzione dei problemi](resources-self-help-sql-on-demand.md) per la risoluzione di problemi comuni. Se si lavora con un pool SQL dedicato anziché con un pool SQL senza server, vedere Procedure consigliate per i pool [SQL](best-practices-dedicated-sql-pool.md) dedicati per indicazioni specifiche.
