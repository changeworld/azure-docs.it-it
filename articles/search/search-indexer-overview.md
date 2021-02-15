---
title: Panoramica degli indicizzatori
titleSuffix: Azure Cognitive Search
description: Esegui la ricerca per indicizzazione di database SQL di Azure, SQL Istanza gestita, Azure Cosmos DB o archiviazione di Azure per estrarre dati ricercabili e popolare un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098096"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indicizzatori in Ricerca cognitiva di Azure

Un *indicizzatore* in ricerca cognitiva di Azure è un crawler che estrae testo e metadati ricercabili da un'origine dati di Azure esterna e popola un indice di ricerca usando mapping da campo a campo tra i dati di origine e l'indice. Questo approccio viene talvolta definito "modello pull" perché il servizio estrae i dati senza che sia necessario scrivere codice che aggiunga dati a un indice. Gli indicizzatori guidano anche le funzionalità di arricchimento dei ricerca cognitiva di [intelligenza artificiale](cognitive-search-concept-intro.md) , integrando l'elaborazione esterna del contenuto in una route a un indice.

Gli indicizzatori sono solo Azure, con singoli indicizzatori per [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md) e [archiviazione BLOB](search-howto-indexing-azure-blob-storage.md). Quando si configura un indicizzatore, è necessario specificare un'origine dati (Origin), oltre a un indice (destinazione). Diverse origini, ad esempio l'archiviazione BLOB, presentano proprietà di configurazione aggiuntive specifiche per quel tipo di contenuto.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni cinque minuti. Gli aggiornamenti più frequenti richiedono un ["modello push"](search-what-is-data-import.md) che Aggiorna contemporaneamente i dati sia in ricerca cognitiva di Azure che nell'origine dati esterna.

## <a name="usage-scenarios"></a>Scenari di utilizzo

È possibile usare un indicizzatore come unico mezzo per l'inserimento dei dati o come parte di una combinazione di tecniche che caricano e, facoltativamente, consentono di trasformare o arricchire il contenuto lungo il percorso. Nella tabella seguente sono riepilogati gli scenari principali.

| Scenario |Strategia |
|----------|---------|
| Singola origine dati | Questo modello è il più semplice: un'origine dati è l'unico provider di contenuti per un indice di ricerca. Dall'origine si identificherà un campo contenente valori univoci da utilizzare come chiave del documento nell'indice di ricerca. Il valore univoco verrà usato come identificatore. Tutti gli altri campi di origine vengono mappati in modo implicito o esplicito ai campi corrispondenti in un indice. </br></br>Un aspetto importante è che il valore di una chiave di documento ha origine dai dati di origine. Un servizio di ricerca non genera valori di chiave. Nelle esecuzioni successive i documenti in ingresso con nuove chiavi vengono aggiunti, mentre i documenti in ingresso con chiavi esistenti vengono uniti o sovrascritti, a seconda che i campi dell'indice siano null o popolati. |
| Più origini dati | Un indice può accettare contenuto da più origini, in cui ogni esecuzione riporta nuovo contenuto da un'origine differente. </br></br>Un risultato può essere un indice che ottiene i documenti dopo l'esecuzione di ogni indicizzatore, con tutti i documenti creati completamente da ogni origine. Ad esempio, i documenti 1-100 rientrano nell'archivio BLOB, i documenti 101-200 appartengono a SQL di Azure e così via. La sfida per questo scenario è la progettazione di uno schema di indice che funziona per tutti i dati in ingresso e una struttura di chiavi del documento uniforme nell'indice di ricerca. In modo nativo, i valori che identificano in modo univoco un documento vengono metadata_storage_path in un contenitore BLOB e in una chiave primaria in una tabella SQL. È possibile immaginare che una o entrambe le origini debbano essere modificate per fornire valori di chiave in un formato comune, indipendentemente dall'origine del contenuto. Per questo scenario, è necessario eseguire un certo livello di pre-elaborazione per omogeneizzare i dati in modo da poterli estrarre in un singolo indice. </br></br>Un risultato alternativo potrebbe essere costituito da documenti di ricerca che vengono popolati parzialmente alla prima esecuzione e successivamente popolati da esecuzioni successive per inserire valori da altre origini. Ad esempio, i campi 1-10 sono dall'archiviazione BLOB, da 11-20 a SQL di Azure e così via. La sfida di questo modello è garantire che ogni esecuzione dell'indicizzazione sia destinata allo stesso documento. Per unire i campi in un documento esistente è necessaria una corrispondenza nella chiave del documento. Per una dimostrazione di questo scenario, vedere [esercitazione: Indice da più origini dati](tutorial-multiple-data-sources.md). |
| Più indicizzatori | Se si usano più origini dati, potrebbero essere necessari più indicizzatori se è necessario variare i parametri della fase di esecuzione, la pianificazione o i mapping dei campi. Sebbene più set di origini dati dell'indicizzatore possano avere come destinazione lo stesso indice, prestare attenzione alle esecuzioni dell'indicizzatore che possono sovrascrivere i valori esistenti nell'indice. Se un secondo indicizzatore-data-source ha come destinazione gli stessi documenti e campi, tutti i valori della prima esecuzione verranno sovrascritti. I valori dei campi vengono sostituiti completamente; un indicizzatore non può unire i valori di più esecuzioni nello stesso campo.</br></br>Un altro caso d'uso multiindicizzatore è la [scalabilità orizzontale tra più aree ricerca cognitiva](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Si potrebbero avere copie dello stesso indice di ricerca in aree diverse. Per sincronizzare il contenuto dell'indice di ricerca, è possibile avere più indicizzatori che effettuano il pull dalla stessa origine dati, in cui ogni indicizzatore è destinato a un indice di ricerca diverso.</br></br>L' [indicizzazione parallela](search-howto-large-index.md#parallel-indexing) di set di dati di grandi dimensioni richiede anche una strategia per più indicizzatori. Ogni indicizzatore è destinato a un subset dei dati. |
| Trasformazione contenuto | Ricerca cognitiva supporta i comportamenti di [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) facoltativi che aggiungono l'analisi delle immagini e l'elaborazione del linguaggio naturale per creare nuovi contenuti e strutture ricercabili. L'arricchimento di intelligenza artificiale è basato sull'indicizzatore, tramite un [skillt](cognitive-search-working-with-skillsets.md)collegato. Per eseguire l'arricchimento di intelligenza artificiale, l'indicizzatore necessita ancora di un indice e di un'origine dati di Azure, ma in questo scenario aggiunge l'elaborazione di competenze all'indicizzatore. |

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

Le connessioni dell'indicizzatore a origini dati remote possono essere effettuate usando connessioni Internet standard (pubblico) o connessioni private crittografate quando si usano reti virtuali di Azure per le app client. È anche possibile configurare le connessioni per l'autenticazione usando un'identità del servizio attendibile. Per altre informazioni sulle connessioni protette, vedere [concessione dell'accesso tramite endpoint privati](search-indexer-securing-resources.md#granting-access-via-private-endpoints) e [connettersi a un'origine dati usando un'identità gestita](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Fasi di indicizzazione

In un'esecuzione iniziale, quando l'indice è vuoto, un indicizzatore verrà letto in tutti i dati forniti nella tabella o nel contenitore. Nelle esecuzioni successive, l'indicizzatore può in genere rilevare e recuperare solo i dati che sono stati modificati. Per i dati BLOB, il rilevamento delle modifiche è automatico. Per altre origini dati come Azure SQL o Cosmos DB, è necessario abilitare il rilevamento delle modifiche.

Per ogni documento ricevuto, un indicizzatore implementa o coordina più passaggi, dal recupero dei documenti a un motore di ricerca finale per l'indicizzazione. Facoltativamente, un indicizzatore è anche strumentale per la guida dell'esecuzione e degli output delle competenze, supponendo che venga definito un valore di competenze.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Fasi dell'indicizzatore" border="false":::

### <a name="stage-1-document-cracking"></a>Fase 1: cracking del documento

Il cracking dei documenti è il processo di apertura di file ed estrazione di contenuto. A seconda del tipo di origine dati, l'indicizzatore proverà a eseguire diverse operazioni per estrarre contenuto potenzialmente indicizzabile.  

Esempi:  

+ Quando il documento è un record in un' [origine dati SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), l'indicizzatore estrae ognuno dei campi per il record.
+ Quando il documento è un file PDF in un' [origine dati di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md), l'indicizzatore estrae il testo, le immagini e i metadati.
+ Quando il documento è un record in un' [origine dati Cosmos DB](search-howto-index-cosmosdb.md), l'indicizzatore estrae i campi e i sottocampi dal documento Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fase 2: mapping dei campi 

Un indicizzatore estrae il testo da un campo di origine e lo invia a un campo di destinazione in un indice o in un archivio informazioni. Quando i tipi e i nomi dei campi coincidono, il percorso è chiaro. Tuttavia, è possibile che si vogliano nomi o tipi diversi nell'output, nel qual caso è necessario indicare all'indicizzatore come eseguire il mapping del campo. 

Questo passaggio si verifica dopo il cracking del documento, ma prima delle trasformazioni, quando l'indicizzatore legge i documenti di origine. Quando si definisce un [mapping di campi](search-indexer-field-mappings.md), il valore del campo di origine viene inviato così com'è al campo di destinazione senza alcuna modifica. 

### <a name="stage-3-skillset-execution"></a>Fase 3: esecuzione delle competenze

L'esecuzione delle competenze è un passaggio facoltativo che richiama l'elaborazione incorporata o personalizzata di intelligenza artificiale. Potrebbe essere necessario per il riconoscimento ottico dei caratteri (OCR) sotto forma di analisi dell'immagine se i dati di origine sono un'immagine binaria o se il contenuto si trova in lingue diverse. 

Qualunque sia la trasformazione, l'esecuzione delle competenze è la posizione in cui si verifica l'arricchimento. Se un indicizzatore è una pipeline, è possibile pensare a un [skillt](cognitive-search-defining-skillset.md) come una "pipeline all'interno della pipeline".

### <a name="stage-4-output-field-mappings"></a>Fase 4: mapping dei campi di output

Se si include un valore di competenze, è molto probabile che sia necessario includere i mapping dei campi di output. L'output di un skillt è effettivamente un albero di informazioni denominato *documento arricchito*. I mapping dei campi di output consentono di selezionare le parti di questo albero da mappare ai campi dell'indice. Informazioni su come [definire i mapping dei campi di output](cognitive-search-output-field-mapping.md).

Mentre i mapping dei campi associano i valori Verbatim dall'origine dati ai campi di destinazione, i mapping dei campi di output indicano all'indicizzatore come associare i valori trasformati nel documento arricchito ai campi di destinazione nell'indice. A differenza dei mapping dei campi, che sono considerati facoltativi, sarà sempre necessario definire un mapping del campo di output per qualsiasi contenuto trasformato che deve risiedere in un indice.

L'immagine successiva mostra una rappresentazione della [sessione di debug](cognitive-search-debug-session.md) dell'indicizzatore di esempio delle fasi dell'indicizzatore: cracking del documento, mapping dei campi, esecuzione di competenze e mapping dei campi di output.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sessione di debug di esempio" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Flusso di lavoro di base

Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

Gli indicizzatori richiedono un oggetto *origine dati* che fornisca una stringa di connessione e possibilmente le credenziali. Chiamare la classe [Create data source (REST)](/rest/api/searchservice/create-data-source) o [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) per creare la risorsa.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice

Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice in genere non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. I campi devono corrispondere per nome e tipo di dati. In caso contrario, è possibile [definire i mapping dei campi](search-indexer-field-mappings.md) per stabilire l'associazione. Per altre informazioni sulla strutturazione di un indice, vedere [creare una classe index (REST)](/rest/api/searchservice/Create-Index) o [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Anche se gli indicizzatori non possono generare automaticamente un indice, la procedura guidata **Importa dati** nel portale può risultare utile. Nella maggior parte dei casi, la procedura guidata può dedurre uno schema di indice dai metadati esistenti nell'origine, presentando uno schema dell'indice preliminare che è possibile modificare inline mentre la procedura guidata è attiva. Dopo la creazione dell'indice nel servizio, le ulteriori modifiche nel portale sono per lo più limitate all'aggiunta di nuovi campi. Prendere in considerazione la procedura guidata per la creazione, ma non per la revisione di un indice. Per un'esperienza di apprendimento pratico, seguire le indicazioni della [procedura dettagliata per il portale](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Passaggio 3: creare ed eseguire (o pianificare) l'indicizzatore

Un indicizzatore viene eseguito quando si [crea prima un indicizzatore](/rest/api/searchservice/Create-Indexer) nel servizio di ricerca. È solo quando si crea o si esegue l'indicizzatore che si scoprirà se l'origine dati è accessibile o se il livello di competenze è valido. Dopo la prima esecuzione, è possibile eseguirla di nuovo su richiesta tramite [Esegui indicizzatore](/rest/api/searchservice/run-indexer)oppure è possibile [definire una pianificazione ricorrente](search-howto-schedule-indexers.md). 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l' [API per ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status). È inoltre consigliabile [eseguire query sull'indice](search-query-create.md) per verificare che il risultato sia quello previsto.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato introdotto, un passaggio successivo consiste nell'esaminare le proprietà e i parametri dell'indicizzatore, la pianificazione e il monitoraggio dell'indicizzatore. In alternativa, è possibile tornare all'elenco delle [origini dati supportate](#supported-data-sources) per ulteriori informazioni su un'origine specifica.

+ [Crea indicizzatori](search-howto-create-indexers.md)
+ [Reimpostare ed eseguire gli indicizzatori](search-howto-run-reset-indexers.md)
+ [Pianificare gli indicizzatori](search-howto-schedule-indexers.md)
+ [Definire i mapping dei campi](search-indexer-field-mappings.md)
+ [Monitorare lo stato dell'indicizzatore](search-howto-monitor-indexers.md)