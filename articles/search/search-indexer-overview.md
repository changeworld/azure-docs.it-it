---
title: Indicizzatori per la ricerca per indicizzazione dei dati durante l'importazione
titleSuffix: Azure Cognitive Search
description: Esegui la ricerca per indicizzazione di database SQL di Azure, SQL Istanza gestita, Azure Cosmos DB o archiviazione di Azure per estrarre dati ricercabili e popolare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 5861e79054bed0d9d75258dfa9cb39b198f0f93d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216445"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indicizzatori in Ricerca cognitiva di Azure

Un *indicizzatore* in ricerca cognitiva di Azure è un crawler che estrae dati e metadati ricercabili da un'origine dati di Azure esterna e popola un indice di ricerca usando mapping da campo a campo tra i dati di origine e l'indice. Questo approccio viene talvolta definito "modello pull" perché il servizio estrae i dati senza che sia necessario scrivere codice che aggiunga dati a un indice.

Gli indicizzatori sono solo Azure, con singoli indicizzatori per [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md) e [archiviazione BLOB](search-howto-indexing-azure-blob-storage.md). Quando si configura un indicizzatore, è necessario specificare un'origine dati (Origin), oltre a un indice (destinazione). Diverse origini, ad esempio l'archiviazione BLOB, presentano proprietà di configurazione aggiuntive specifiche per quel tipo di contenuto.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni cinque minuti. Gli aggiornamenti più frequenti richiedono un modello push che Aggiorna contemporaneamente i dati sia in ricerca cognitiva di Azure che nell'origine dati esterna.

## <a name="usage-scenarios"></a>Scenari di utilizzo

È possibile utilizzare un indicizzatore come unico mezzo per l'inserimento dei dati oppure utilizzare una combinazione di tecniche che includano solo alcuni dei campi dell'indice, trasformando facoltativamente o arricchindo il contenuto lungo il percorso. Nella tabella seguente sono riepilogati gli scenari principali.

| Scenario |Strategia |
|----------|---------|
| Origine singola | Questo modello è il più semplice: un'origine dati è l'unico provider di contenuti per un indice di ricerca. Dall'origine si identificherà un campo contenente valori univoci da utilizzare come chiave del documento nell'indice di ricerca. Il valore univoco verrà usato come identificatore. Tutti gli altri campi di origine vengono mappati in modo implicito o esplicito ai campi corrispondenti in un indice. </br></br>Un aspetto importante è che il valore di una chiave di documento ha origine dai dati di origine. Un servizio di ricerca non genera valori di chiave. Nelle esecuzioni successive i documenti in ingresso con nuove chiavi vengono aggiunti, mentre i documenti in ingresso con chiavi esistenti vengono uniti o sovrascritti, a seconda che i campi dell'indice siano null o popolati. |
| Più origini| Un indice può accettare contenuto da più origini, in cui ogni esecuzione riporta nuovo contenuto da un'origine differente. </br></br>Un risultato può essere un indice che ottiene i documenti dopo l'esecuzione di ogni indicizzatore, con tutti i documenti creati completamente da ogni origine. Ad esempio, i documenti 1-100 rientrano nell'archivio BLOB, i documenti 101-200 appartengono a SQL di Azure e così via. La sfida per questo scenario è la progettazione di uno schema di indice che funziona per tutti i dati in ingresso e una struttura di chiavi del documento uniforme nell'indice di ricerca. In modo nativo, i valori che identificano in modo univoco un documento vengono metadata_storage_path in un contenitore BLOB e in una chiave primaria in una tabella SQL. È possibile immaginare che una o entrambe le origini debbano essere modificate per fornire valori di chiave in un formato comune, indipendentemente dall'origine del contenuto. Per questo scenario, è necessario eseguire un certo livello di pre-elaborazione per omogeneizzare i dati in modo da poterli estrarre in un singolo indice.</br></br>Un risultato alternativo potrebbe essere costituito da documenti di ricerca che vengono popolati parzialmente alla prima esecuzione e successivamente popolati da esecuzioni successive per inserire valori da altre origini. Ad esempio, i campi 1-10 sono dall'archiviazione BLOB, da 11-20 a SQL di Azure e così via. La sfida di questo modello è garantire che ogni esecuzione dell'indicizzazione sia destinata allo stesso documento. Per unire i campi in un documento esistente è necessaria una corrispondenza nella chiave del documento. Per una dimostrazione di questo scenario, vedere [esercitazione: Indice da più origini dati](tutorial-multiple-data-sources.md). |
| Trasformazione contenuto | Ricerca cognitiva supporta i comportamenti di [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) facoltativi che aggiungono l'analisi delle immagini e l'elaborazione del linguaggio naturale per creare nuovi contenuti e strutture ricercabili. L'arricchimento di intelligenza artificiale è basato sull'indicizzatore, tramite un [skillt](cognitive-search-working-with-skillsets.md)collegato. Per eseguire l'arricchimento di intelligenza artificiale, l'indicizzatore necessita ancora di un indice e di un'origine dati, ma in questo scenario aggiunge l'elaborazione di competenze all'indicizzatore. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Approcci per la creazione e la gestione degli indicizzatori

È possibile creare e gestire gli indicizzatori nei modi seguenti:

+ [Importazione guidata dati > portale](search-import-data-portal.md)
+ [API REST del servizio](/rest/api/searchservice/Indexer-operations)
+ [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Se si usa un SDK, creare un [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) per lavorare con gli indicizzatori, le origini dati e skillsets. Il collegamento precedente è per .NET SDK, ma tutti gli SDK forniscono un SearchIndexerClient e API simili.

Inizialmente, le nuove origini dati vengono annunciate come funzionalità di anteprima e sono solo REST. Dopo aver completato la disponibilità a livello generale, il supporto completo è integrato nel portale e nei vari SDK, ciascuno dei quali si basa sulle proprie pianificazioni di rilascio.

## <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono una [chiave API di amministrazione](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Gli indicizzatori eseguono ricerche per indicizzazione negli archivi dati in Azure.

+ [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake storage Gen2](search-howto-index-azure-data-lake-storage.md) (in anteprima)
+ [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Istanza gestita di SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server in Macchine virtuali di Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Fasi di indicizzazione

In un'esecuzione iniziale, quando l'indice è vuoto, un indicizzatore verrà letto in tutti i dati forniti nella tabella o nel contenitore. Nelle esecuzioni successive, l'indicizzatore può in genere rilevare e recuperare solo i dati che sono stati modificati. Per i dati BLOB, il rilevamento delle modifiche è automatico. Per altre origini dati come Azure SQL o Cosmos DB, è necessario abilitare il rilevamento delle modifiche.

Per ogni documento che inserisce, un indicizzatore implementa o coordina più passaggi, dal recupero dei documenti a un motore di ricerca finale per l'indicizzazione. Facoltativamente, un indicizzatore è anche strumentale per la guida dell'esecuzione e degli output delle competenze, supponendo che venga definito un valore di competenze.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Fasi dell'indicizzatore" border="false":::

### <a name="stage-1-document-cracking"></a>Fase 1: cracking del documento

Il cracking dei documenti è il processo di apertura di file ed estrazione di contenuto. A seconda del tipo di origine dati, l'indicizzatore proverà a eseguire diverse operazioni per estrarre contenuto potenzialmente indicizzabile.  

Esempi:  

+ Quando il documento è un record in un' [origine dati SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), l'indicizzatore estrae ognuno dei campi per il record.
+ Quando il documento è un file PDF in un' [origine dati di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md), l'indicizzatore estrae il testo, le immagini e i metadati.
+ Quando il documento è un record in un' [origine dati Cosmos DB](search-howto-index-cosmosdb.md), l'indicizzatore estrae i campi e i sottocampi dal documento Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fase 2: mapping dei campi 

Un indicizzatore estrae il testo da un campo di origine e lo invia a un campo di destinazione in un indice o in un archivio informazioni. Quando i tipi e i nomi dei campi coincidono, il percorso è chiaro. Tuttavia, è possibile che si vogliano nomi o tipi diversi nell'output, nel qual caso è necessario indicare all'indicizzatore come eseguire il mapping del campo. Questo passaggio si verifica dopo il cracking del documento, ma prima delle trasformazioni, quando l'indicizzatore legge i documenti di origine. Quando si definisce un [mapping di campi](search-indexer-field-mappings.md), il valore del campo di origine viene inviato così com'è al campo di destinazione senza alcuna modifica. I mapping dei campi sono facoltativi.

### <a name="stage-3-skillset-execution"></a>Fase 3: esecuzione delle competenze

L'esecuzione delle competenze è un passaggio facoltativo che richiama l'elaborazione incorporata o personalizzata di intelligenza artificiale. Potrebbe essere necessario per il riconoscimento ottico dei caratteri (OCR) sotto forma di analisi delle immagini oppure potrebbe essere necessaria la traduzione della lingua. Qualunque sia la trasformazione, l'esecuzione delle competenze è la posizione in cui si verifica l'arricchimento. Se un indicizzatore è una pipeline, è possibile pensare a un [skillt](cognitive-search-defining-skillset.md) come una "pipeline all'interno della pipeline". Un skillt ha una propria sequenza di passaggi chiamati Skills.

### <a name="stage-4-output-field-mappings"></a>Fase 4: mapping dei campi di output

Se si include un valore di competenze, è molto probabile che sia necessario includere i mapping dei campi di output. L'output di un skillt è effettivamente un albero di informazioni denominato documento arricchito. I mapping dei campi di output consentono di selezionare le parti di questo albero da mappare ai campi dell'indice. Informazioni su come [definire i mapping dei campi di output](cognitive-search-output-field-mapping.md).

Mentre i mapping dei campi associano i valori Verbatim dall'origine dati ai campi di destinazione, i mapping dei campi di output indicano all'indicizzatore come associare i valori trasformati nel documento arricchito ai campi di destinazione nell'indice. A differenza dei mapping dei campi, che sono considerati facoltativi, sarà sempre necessario definire un mapping del campo di output per qualsiasi contenuto trasformato che deve risiedere in un indice.

L'immagine successiva mostra una rappresentazione della [sessione di debug](cognitive-search-debug-session.md) dell'indicizzatore di esempio delle fasi dell'indicizzatore: cracking del documento, mapping dei campi, esecuzione di competenze e mapping dei campi di output.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sessione di debug di esempio" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Procedura di configurazione di base

Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

Un indicizzatore ottiene una connessione all'origine dati da un oggetto *origine dati* . La definizione dell'origine dati fornisce una stringa di connessione e possibilmente le credenziali. Chiamare l'API REST [create DataSource](/rest/api/searchservice/create-data-source) o la [classe SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) per creare la risorsa.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice

Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice in genere non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. I campi devono corrispondere per nome e tipo di dati. Per altre informazioni sulla strutturazione di un indice, vedere [creare un indice (API REST di Azure ricerca cognitiva)](/rest/api/searchservice/Create-Index) o una [classe SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex). Per informazioni sulle associazioni di campi, vedere [mapping dei campi in Azure ricerca cognitiva Indexers](search-indexer-field-mappings.md).

> [!Tip]
> Anche se gli indicizzatori non possono generare automaticamente un indice, la procedura guidata **Importa dati** nel portale può risultare utile. Nella maggior parte dei casi, la procedura guidata può dedurre uno schema di indice dai metadati esistenti nell'origine, presentando uno schema dell'indice preliminare che è possibile modificare inline mentre la procedura guidata è attiva. Dopo la creazione dell'indice nel servizio, le ulteriori modifiche nel portale sono per lo più limitate all'aggiunta di nuovi campi. Prendere in considerazione la procedura guidata per la creazione, ma non per la revisione di un indice. Per un'esperienza di apprendimento pratico, seguire le indicazioni della [procedura dettagliata per il portale](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passaggio 3: Creare e pianificare l'indicizzatore

La definizione dell'indicizzatore è un costrutto che riunisce tutti gli elementi correlati all'inserimento di dati. Gli elementi necessari includono un'origine dati e un indice. Gli elementi facoltativi includono una pianificazione e mapping dei campi. I mapping dei campi sono facoltativi solo se i campi di origine e di indice corrispondono chiaramente. Per altre informazioni sulla struttura di un indicizzatore, vedere [creare un indicizzatore (API REST di Azure ricerca cognitiva)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Eseguire gli indicizzatori su richiesta

Sebbene sia normale pianificare l'indicizzazione, un indicizzatore può anche essere richiamato su richiesta tramite il [comando Run](/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Quando Run API restituisce un codice di esito positivo, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva avviene in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l' [API per ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status). 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Ottenere lo stato dell'indicizzatore

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore tramite il [comando Get Indexer status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

La cronologia di esecuzione contiene fino alle 50 più recenti esecuzioni completate, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito queste informazioni di base, il passaggio successivo prevede l'analisi dei requisiti e delle attività specifici per ogni tipo di origine dati.

+ [Database SQL di Azure, Istanza gestita SQL o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
+ [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-csv-blobs.md)
+ [Indicizzazione di BLOB JSON con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-json-blobs.md)