---
title: Query Store - Database di Azure per PostgreSQL - Flex Server
description: Questo articolo descrive la funzionalità Query Store in Database di Azure per PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559083"
---
# <a name="monitor-performance-with-query-store"></a>Monitorare le prestazioni con Query Store
**Si applica a:** Database di Azure per PostgreSQL - Flex Server versione 11 e successive

La funzionalità Query Store di Database di Azure per PostgreSQL offre la possibilità di tenere traccia delle prestazioni delle query nel tempo. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query a esecuzione più lunga e a elevato utilizzo di risorse. Query Store acquisisce automaticamente una cronologia delle query e le statistiche di runtime e le conserva a scopo di verifica. Seziona i dati in base al tempo per poter visualizzare i modelli di utilizzo temporali. I dati per tutti gli utenti, i database e le query vengono archiviati in un database denominato **azure_sys** nell'istanza di Database di Azure per PostgreSQL.

> [!IMPORTANT]
> Non modificare il **database azure_sys** o il relativo schema. Tale modifica impedirebbe il corretto funzionamento di Query Store e delle funzionalità per le prestazioni correlate.
## <a name="enabling-query-store"></a>Abilitazione di Query Store
Query Store è una funzionalità con consenso esplicito, quindi non è abilitata per impostazione predefinita in un server. Query Store è abilitato o disabilitato a livello globale per tutti i database in un determinato server e non può essere attivato o disattivato per ogni database.
### <a name="enable-query-store-using-the-azure-portal"></a>Abilitare Query Store con il portale di Azure
1. Accedere al portale di Azure e selezionare il server di Database di Azure per PostgreSQL.
2. Selezionare **Parametri del server** nella sezione **Impostazioni** del menu.
3. Cercare il `pg_qs.query_capture_mode` parametro.
4. Impostare il valore su `TOP` o `ALL` e **salvare**.
Per il salvataggio permanente del primo batch di dati nel database azure_sys possono essere necessari fino a 20 minuti.
## <a name="information-in-query-store"></a>Informazioni in Query Store
Query Store ha un archivio:
- Un archivio delle statistiche di runtime per il salvataggio permanente delle informazioni delle statistiche di esecuzione delle query.

Gli scenari comuni per l'uso di Query Store includono:
- Determinazione del numero di volte in cui una query è stata eseguita in un determinato intervallo di tempo
- Confronto del tempo di esecuzione di una query nei diversi intervalli di tempo per identificare differenze significative
- Identificazione delle query con esecuzione più lunga nelle ultime ore Per ridurre al minimo l'utilizzo dello spazio, le statistiche di esecuzione di runtime nell'archivio delle statistiche di runtime vengono aggregate in un intervallo di tempo fisso e configurabile. È possibile eseguire query sulle informazioni in questi archivi usando le visualizzazioni.
## <a name="access-query-store-information"></a>Accedere Query Store informazioni
Query Store dati vengono archiviati nel database azure_sys nel server Postgres. La query seguente restituisce informazioni sulle query in Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Opzioni di configurazione
Quando è abilitato, Query Store salva i dati in intervalli di aggregazione di 15 minuti, con un massimo di 500 query distinte per intervallo. Per la configurazione dei parametri di Query Store sono disponibili le opzioni seguenti.

| **Parametro** | **Descrizione** | **Default** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Imposta le istruzioni di cui verrà tenuta traccia. | none | none, top, all |
| pg_qs.max_query_text_length | Imposta la lunghezza massima di query che è possibile salvare. Le query più lunghe verranno troncate. | 6000 | 100-10000 |
| pg_qs.retention_period_in_days | Imposta il periodo di conservazione. | 7 | 1-30 |
| pg_qs.track_utility | Imposta se deve essere tenuta traccia dei comandi dell'utilità. | on | on, off |

Usare il [portale di Azure](howto-configure-server-parameters-using-portal.md) per ottenere o impostare un valore diverso per un parametro.

## <a name="views-and-functions"></a>Viste e funzioni
Visualizzare e gestire Query Store usando le viste e le funzioni seguenti. Queste viste possono essere usate da qualsiasi membro del ruolo pubblico di PostgreSQL per visualizzare i dati in Query Store e sono disponibili solo nel database **azure_sys**.
Le query vengono normalizzate esaminandone la struttura dopo la rimozione di valori letterali e costanti. Se due query sono identiche, ad eccezione dei valori letterali, avranno lo stesso queryId.
### <a name="query_storeqs_view"></a>query_store.qs_view
Questa vista restituisce tutti i dati in Query Store. Contiene una riga per ogni specifico ID database, ID utente e ID query. 

|**Nome**   |**Tipo** | **Riferimenti**  | **Descrizione**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID nella tabella runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID dell'utente che ha eseguito l'istruzione|
|db_id  |oid    |pg_database.oid    |OID del database in cui l'istruzione è stata eseguita|
|query_id   |bigint  || Codice hash interno, calcolato dall'albero di analisi dell'istruzione|
|query_sql_text |Varchar(10000)  || Testo di un'istruzione rappresentativa. Query diverse con la stessa struttura vengono raggruppate e questo è il testo per la prima query del gruppo.|
|plan_id    |bigint |   |ID del piano corrispondente alla query, non ancora disponibile|
|start_time | timestamp  ||  Le query vengono aggregate per intervalli di tempo. La durata di un intervallo è di 15 minuti per impostazione predefinita. Questo timestamp è l'ora di inizio corrispondente all'intervallo di tempo della voce.|
|end_time   | timestamp  ||  Ora di fine corrispondente all'intervallo di tempo della voce|
|calls  |bigint  || Numero di volte in cui la query è stata eseguita|
|total_time |double precision   ||  Tempo totale di esecuzione della query, in millisecondi|
|min_time   |double precision   ||  Tempo minimo di esecuzione della query, in millisecondi|
|max_time   |double precision   ||  Tempo massimo di esecuzione della query, in millisecondi|
|mean_time  |double precision   ||  Tempo medio di esecuzione della query, in millisecondi|
|stddev_time|   double precision    ||  Deviazione standard del tempo di esecuzione della query, in millisecondi |
|rows   |bigint ||  Numero totale di righe recuperate o interessate dall'istruzione|
|shared_blks_hit|   bigint  ||  Numero totale di riscontri nella cache dei blocchi condivisi ottenuto dall'istruzione|
|shared_blks_read|  bigint  ||  Numero totale dei blocchi condivisi letti dall'istruzione|
|shared_blks_dirtied|   bigint   || Numero totale dei blocchi condivisi modificati ma non salvati dall'istruzione |
|shared_blks_written|   bigint  ||  Numero totale dei blocchi condivisi scritti dall'istruzione|
|local_blks_hit|    bigint ||   Numero totale di riscontri nella cache dei blocchi locali ottenuto dall'istruzione|
|local_blks_read|   bigint   || Numero totale dei blocchi locali letti dall'istruzione|
|local_blks_dirtied|    bigint  ||  Numero totale dei blocchi locali modificati ma non salvati dall'istruzione|
|local_blks_written|    bigint  ||  Numero totale dei blocchi locali scritti dall'istruzione|
|temp_blks_read |bigint  || Numero totale dei blocchi temporanei letti dall'istruzione|
|temp_blks_written| bigint   || Numero totale dei blocchi temporanei scritti dall'istruzione|
|blk_read_time  |double precision    || Tempo totale impiegato dall'istruzione per la lettura dei blocchi, in millisecondi, se il parametro track_io_timing è abilitato. In caso contrario, è zero.|
|blk_write_time |double precision    || Tempo totale impiegato dall'istruzione per la scrittura dei blocchi, in millisecondi, se il parametro track_io_timing è abilitato. In caso contrario, è zero.|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Questa vista restituisce i dati del testo delle query in Query Store. Contiene una riga per ogni specifico query_text.

| **Nome** | **Tipo** | **Descrizione** |
|--|--|--|
| query_text_id | bigint | ID della tabella query_texts |
| query_sql_text | Varchar(10000) | Testo di un'istruzione rappresentativa. Query diverse con la stessa struttura vengono raggruppate e questo è il testo per la prima query del gruppo. |

### <a name="functions"></a>Funzioni
`qs_reset` rimuove tutte le statistiche finora raccolte da Query Store. Questa funzione può essere eseguita solo dal ruolo di amministratore del server.

`staging_data_reset` rimuove tutte le statistiche raccolte in memoria da Query Store, ossia i dati in memoria che non sono ancora stati scaricati nel database. Questa funzione può essere eseguita solo dal ruolo di amministratore del server.

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti
- Se un server PostgreSQL ha il parametro default_transaction_read_only, Query Store non acquisisce alcun dato.
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [scenari in cui Query Store può essere particolarmente utile](concepts-query-store-scenarios.md).
- Altre informazioni sulle [procedure consigliate per l'uso di Query Store](concepts-query-store-best-practices.md).
