---
title: Creare un indicizzatore
titleSuffix: Azure Cognitive Search
description: Impostare le proprietà di un indicizzatore per determinare l'origine e le destinazioni dei dati. È possibile impostare i parametri per modificare i comportamenti del runtime.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 596eca0d73ffc4a590fae9b346658a2c31a1d68c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676470"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Creazione di indicizzatori in Azure ricerca cognitiva

Un indicizzatore di ricerca fornisce un flusso di lavoro automatizzato per il trasferimento di documenti e contenuto da un'origine dati esterna a un indice di ricerca nel servizio di ricerca. Come originariamente progettato, estrae testo e metadati da un'origine dati di Azure, serializza i documenti in JSON e passa i documenti risultanti a un motore di ricerca per l'indicizzazione. Poiché è stato esteso per supportare l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) per l'elaborazione approfondita di contenuti. 

L'uso di indicizzatori riduce significativamente la quantità e la complessità del codice che è necessario scrivere. Questo articolo è incentrato sui meccanismi di creazione di un indicizzatore come preparazione per lavorare in modo più avanzato con gli indicizzatori specifici per l'origine e con [skillsets](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>Definizione di indicizzatore

Gli indicizzatori vengono usati per l'indicizzazione basata su testo che estrae il contenuto alfanumerico dai campi di origine nei campi di indice, o l'elaborazione basata su intelligenza artificiale che analizza il testo non differenziato per la struttura, o analizza le immagini per il testo e le informazioni, aggiungendo anche tale contenuto a un indice. Le definizioni di indice seguenti sono tipiche di quelle che è possibile creare per uno scenario.

### <a name="indexers-for-text-content"></a>Indicizzatori per contenuto di testo

Lo scopo originale di un indicizzatore è quello di semplificare il processo complesso di caricamento di un indice fornendo un meccanismo per la connessione e la lettura di contenuto di testo e numerici da campi in un'origine dati, la serializzazione del contenuto come documenti JSON e la consegna di tali documenti al motore di ricerca per l'indicizzazione. Questo è ancora un caso d'uso primario e, per questa operazione, è necessario creare un indicizzatore con le proprietà definite nell'esempio seguente.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

Le **`name`** **`dataSourceName`** proprietà, e **`targetIndexName`**  sono obbligatorie e, a seconda della modalità di creazione dell'indicizzatore, per poter eseguire l'indicizzatore è necessario che sia già presente l'origine dati e l'indice. 

La **`parameters`** proprietà modifica i comportamenti in fase di esecuzione, ad esempio il numero di errori da accettare prima che l'intero processo abbia esito negativo. I parametri rappresentano anche il modo in cui si specificano i comportamenti specifici dell'origine. Se, ad esempio, l'origine è l'archiviazione BLOB, è possibile impostare un parametro per filtrare le estensioni di file: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

La **`field mappings`** proprietà viene utilizzata per eseguire il mapping esplicito dei campi da origine a destinazione se tali campi differiscono per nome o tipo. Altre proprietà (non visualizzate), vengono usate per [specificare una pianificazione](search-howto-schedule-indexers.md), creare l'indicizzatore in uno stato disabilitato o specificare una [chiave di crittografia](search-security-manage-encryption-keys.md) per la crittografia aggiuntiva dei dati inattivi.

### <a name="indexers-for-ai-indexing"></a>Indicizzatori per l'indicizzazione di intelligenza artificiale

Poiché gli indicizzatori sono il meccanismo mediante il quale un servizio di ricerca esegue le richieste in uscita, gli indicizzatori sono stati estesi per supportare le arricchimenti di intelligenza artificiale, aggiungendo l'infrastruttura e gli oggetti per implementare il caso d'uso.

Tutti i parametri e le proprietà precedenti si applicano agli indicizzatori che eseguono l'arricchimento AI. Le proprietà seguenti sono specifiche per l'arricchimento ai: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (solo anteprima e REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

L'arricchimento di intelligenza artificiale esula dall'ambito di questo articolo. Per altre informazioni, iniziare con questi articoli: [arricchimento ai](cognitive-search-concept-intro.md), [Skillsets in Azure ricerca cognitiva](cognitive-search-working-with-skillsets.md)e [creare competenze (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Scegliere un client indicizzatore e creare l'indicizzatore

Quando si è pronti per creare un indicizzatore in un servizio di ricerca remoto, è necessario un client di ricerca sotto forma di strumento, ad esempio portale di Azure o postazione, o codice che crea un'istanza di un client di indicizzatore. È consigliabile usare le API portale di Azure o REST per i test iniziali di sviluppo e di prova.

### <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate agli indicizzatori, incluse le richieste GET per lo stato o le definizioni, richiedono una [chiave API di amministrazione](search-security-api-keys.md) per la richiesta.

### <a name="limits"></a>Limiti

Tutti i [livelli di servizio limitano](search-limits-quotas-capacity.md#indexer-limits) il numero di oggetti che è possibile creare. Se si sta sperimentando il livello gratuito, è possibile disporre solo di 3 oggetti di ogni tipo e 2 minuti di elaborazione dell'indicizzatore (escluso l'elaborazione di competenze).

### <a name="use-azure-portal-to-create-an-indexer"></a>Usare portale di Azure per creare un indicizzatore

Il portale offre due opzioni per la creazione di un indicizzatore: [**importazione guidata dati**](search-import-data-portal.md) e **nuovo indicizzatore** che fornisce i campi per specificare una definizione dell'indicizzatore. La procedura guidata è univoca in quanto crea tutti gli elementi necessari. Per altri approcci è necessario avere già definito un'origine dati e un indice.

Lo screenshot seguente mostra dove è possibile trovare queste funzionalità nel portale. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Indicizzatore di hotels" border="true":::

### <a name="use-a-rest-client"></a>Usare un client REST

Sia l'utente che la Visual Studio Code (con estensione per Azure ricerca cognitiva) possono fungere da client di indicizzatore. Utilizzando uno degli strumenti, è possibile connettersi al servizio di ricerca e inviare richieste di [creazione indicizzatore (REST)](/rest/api/searchservice/create-indexer) . Sono disponibili numerose esercitazioni ed esempi che illustrano i client REST per la creazione di oggetti. 

Per informazioni su ogni client, iniziare con uno di questi articoli:

+ [Creare un indice di ricerca con REST e postazione](search-get-started-rest.md)
+ [Introduzione a Visual Studio Code e a Ricerca cognitiva di Azure](search-get-started-vs-code.md)

Per informazioni sulla formulazione di richieste di indicizzatore, vedere [operazioni sugli indicizzatori (REST)](/rest/api/searchservice/Indexer-operations) .

### <a name="use-an-sdk"></a>Usare un SDK

Per ricerca cognitiva, gli SDK di Azure implementano le funzionalità disponibili a livello generale. Di conseguenza, è possibile usare uno qualsiasi degli SDK per creare oggetti correlati all'indicizzatore. Tutti forniscono un **SearchIndexerClient** che dispone di metodi per la creazione di indicizzatori e di oggetti correlati, incluso skillsets.

| Azure SDK | Client | Esempio |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indicizzatori](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Eseguire l'indicizzatore

Un indicizzatore viene eseguito automaticamente quando si crea l'indicizzatore sul servizio. Questo è il momento della verità in cui si scopriranno gli errori di connessione all'origine dati, i problemi di mapping dei campi o i problemi di competenze. 

È possibile eseguire un indicizzatore in diversi modi:

+ Inviare una richiesta HTTP per [Crea indicizzatore](/rest/api/searchservice/create-indexer) o [aggiornare l'indicizzatore](/rest/api/searchservice/update-indexer) per aggiungere o modificare la definizione ed eseguire l'indicizzatore.

+ Inviare una richiesta HTTP per l' [esecuzione dell'indicizzatore](/rest/api/searchservice/run-indexer) per eseguire un indicizzatore senza apportare modifiche alla definizione.

+ Eseguire un programma che chiama i metodi SearchIndexerClient per la creazione, l'aggiornamento o l'esecuzione.

> [!NOTE]
> Per evitare di eseguire immediatamente un indicizzatore al momento della creazione, includere **`disabled=true`** nella definizione dell'indicizzatore.

In alternativa, inserire l'indicizzatore [in una pianificazione](search-howto-schedule-indexers.md) per richiamare l'elaborazione a intervalli regolari. 

L'elaborazione pianificata in genere coincide con la necessità di indicizzazione incrementale del contenuto modificato. La logica di rilevamento delle modifiche è una funzionalità incorporata nelle piattaforme di origine. Le modifiche apportate a un contenitore BLOB vengono rilevate automaticamente dall'indicizzatore. Per istruzioni sull'uso del rilevamento delle modifiche in altre origini dati, vedere la documentazione dell'indicizzatore per le origini dati specifiche:

+ [Database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Rilevamento delle modifiche e stato dell'indicizzatore

Gli indicizzatori possono rilevare le modifiche nei dati sottostanti e elaborare solo documenti nuovi o aggiornati su ogni indicizzatore eseguito. Se, ad esempio, lo stato dell'indicizzatore indica che un'esecuzione è stata completata con `0/0` documenti elaborati, significa che l'indicizzatore non ha trovato righe o BLOB nuovi o modificati nell'origine dati sottostante.

Il modo in cui un indicizzatore supporta il rilevamento delle modifiche varia a seconda dell'origine dati:

+ Archiviazione BLOB di Azure, archiviazione tabelle di Azure e Azure Data Lake Storage Gen2 timbrare ogni aggiornamento di BLOB o righe con una data e un'ora. I vari indicizzatori utilizzano queste informazioni per determinare quali documenti aggiornare nell'indice. Il rilevamento delle modifiche incorporato significa che un indicizzatore è in grado di riconoscere i documenti nuovi e aggiornati, senza necessità di alcuna configurazione aggiuntiva da parte dell'utente.

+ Azure SQL e Cosmos DB offrono funzionalità di rilevamento delle modifiche nelle proprie piattaforme. È possibile specificare i criteri di rilevamento delle modifiche nella definizione dell'origine dati.

Per i caricamenti di indicizzazione di grandi dimensioni, un indicizzatore tiene traccia anche dell'ultimo documento elaborato tramite un "limite massimo" interno. Il marcatore non viene mai esposto nell'API, ma internamente l'indicizzatore tiene traccia del punto in cui è stato interrotto. Quando l'indicizzazione riprende, tramite un'esecuzione pianificata o una chiamata su richiesta, l'indicizzatore fa riferimento al limite massimo, in modo che possa scegliere il punto in cui è stato interrotto.

Se è necessario cancellare il limite massimo per la reindicizzazione completa, è possibile usare [Reimposta indicizzatore](/rest/api/searchservice/reset-indexer). Per una reindicizzazione più selettiva, utilizzare [Reimposta competenze](/rest/api/searchservice/preview-api/reset-skills) o [Reimposta documenti](/rest/api/searchservice/preview-api/reset-documents). Tramite le API reset è possibile cancellare lo stato interno e scaricare la cache anche se è stata abilitata l' [arricchimento incrementale](search-howto-incremental-index.md). Per ulteriori informazioni su background e confronto di ogni opzione di reimpostazione, vedere [eseguire o reimpostare indicizzatori, competenze e documenti](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Informazioni sui dati

Gli indicizzatori prevedono un set di righe tabulare, in cui ogni riga diventa un documento di ricerca completo o parziale nell'indice. Spesso è presente una corrispondenza uno-a-uno tra una riga e il documento di ricerca risultante, in cui tutti i campi nel set di righe popolano completamente ogni documento. È tuttavia possibile usare gli indicizzatori per generare solo parte di un documento, ad esempio se si usano più indicizzatori o approcci per compilare l'indice. 

Per rendere flat i dati relazionali in un set di righe, è necessario creare una vista SQL o compilare una query che restituisca record padre e figlio nella stessa riga. Il set di dati di esempio degli Alberghi predefiniti è ad esempio un database SQL con 50 record (uno per ogni albergo), collegati a record di chat room in una tabella correlata. La query che rende Flat i dati collettivi in un set di righe incorpora tutte le informazioni sulla stanza nei documenti JSON in ogni record di un hotel. Le informazioni sulla stanza incorporata sono generate da una query che usa una clausola **for JSON auto** . Per altre informazioni su questa tecnica, vedere [definire una query che restituisce JSON incorporato](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Questo è solo un esempio. è possibile trovare altri approcci che produrranno lo stesso effetto.

Oltre ai dati flat, è importante eseguire il pull solo dei dati ricercabili. I dati ricercabili sono alfanumerici. Ricerca cognitiva non è in grado di eseguire ricerche su dati binari in qualsiasi formato, sebbene sia in grado di estrarre e dedurre descrizioni di testo dei file di immagine (vedere [arricchimento intelligenza artificiale](cognitive-search-concept-intro.md)) per creare contenuto ricercabile. Analogamente, usando l'arricchimento di intelligenza artificiale, il testo di grandi dimensioni può essere analizzato dai modelli di linguaggio naturale per trovare la struttura o le informazioni rilevanti, generando nuovo contenuto che è possibile aggiungere a un documento di ricerca.

Dato che gli indicizzatori non correggono i problemi relativi ai dati, potrebbero essere necessarie altre forme di pulizia o manipolazione dei dati. Per ulteriori informazioni, è necessario fare riferimento alla documentazione del prodotto del [database di Azure](../index.yml?product=databases).

## <a name="know-your-index"></a>Informazioni sull'indice

Ricordare che gli indicizzatori passano i documenti di ricerca al motore di ricerca per l'indicizzazione. Così come gli indicizzatori dispongono di proprietà che determinano il comportamento di esecuzione, uno schema dell'indice dispone di proprietà che influiscono in modo inverso sulla modalità di indicizzazione delle stringhe (solo le stringhe vengono analizzate e in formato token). A seconda delle assegnazioni dell'analizzatore, le stringhe indicizzate potrebbero essere diverse da quelle passate. È possibile valutare gli effetti degli analizzatori utilizzando [analizza testo (REST)](/rest/api/searchservice/test-analyzer). Per ulteriori informazioni sugli analizzatori, vedere [analizzatori per l'elaborazione del testo](search-analyzers.md).

Per il modo in cui gli indicizzatori interagiscono con un indice, un indicizzatore controlla solo i tipi e i nomi dei campi. Non esiste un passaggio di convalida che garantisce che il contenuto in ingresso sia corretto per il campo di ricerca corrispondente nell'indice. Come passaggio di verifica, è possibile eseguire query sull'indice compilato che restituiscono interi documenti o campi selezionati. Per ulteriori informazioni sull'esecuzione di query sul contenuto di un indice, vedere [creare una query di base](search-query-create.md).

## <a name="next-steps"></a>Passaggi successivi

+ [Pianificare gli indicizzatori](search-howto-schedule-indexers.md)
+ [Definire i mapping dei campi](search-indexer-field-mappings.md)
+ [Monitorare lo stato dell'indicizzatore](search-howto-monitor-indexers.md)
+ [Connettersi con identità gestite](search-howto-managed-identities-data-sources.md)