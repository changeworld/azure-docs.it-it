---
title: Eseguire ricerche su BLOB JSON
titleSuffix: Azure Cognitive Search
description: Eseguire l'indicizzazione di BLOB JSON di Azure per il contenuto di testo usando l'indicizzatore Azure ricerca cognitiva BLOB. Gli indicizzatori automatizzano l'inserimento di dati per le origini dati selezionate, ad esempio l'archiviazione BLOB di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259051"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Come indicizzare i BLOB JSON usando un indicizzatore BLOB in Azure ricerca cognitiva

Questo articolo illustra come [configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md) per i BLOB che sono costituiti da documenti JSON. I BLOB JSON nell'archiviazione BLOB di Azure presuppongono comunemente uno di questi formati:

+ Un documento JSON singolo
+ Un documento JSON contenente una matrice di elementi JSON ben formati
+ Un documento JSON contenente più entità, separate da una nuova riga

L'indicizzatore BLOB fornisce un **`parsingMode`** parametro per ottimizzare l'output del documento di ricerca in base alle modalità di analisi della struttura, costituito dalle opzioni seguenti:

| parsingMode | Documento JSON | Descrizione |
|--------------|-------------|--------------|
| **`json`** | Un solo documento per BLOB | predefinita Analizza i BLOB JSON come singolo blocco di testo. Ogni BLOB JSON diventa un documento di ricerca singolo. |
| **`jsonArray`** | Più documenti per BLOB | Analizza una matrice JSON nel BLOB, in cui ogni elemento della matrice diventa un documento di ricerca separato.  |
| **`jsonLines`** | Più documenti per BLOB | Analizza un BLOB che contiene più entità JSON (anche una matrice) con singoli elementi separati da una nuova riga. L'indicizzatore avvia un nuovo documento di ricerca dopo ogni nuova riga. |

Per **`jsonArray`** e **`jsonLines`** , è necessario esaminare l' [indicizzazione di un BLOB per produrre molti documenti di ricerca](search-howto-index-one-to-many-blobs.md) per comprendere in che modo l'indicizzatore BLOB gestisce la risoluzione dell'ambiguità della chiave del documento per più documenti di ricerca generati dallo stesso BLOB.

All'interno della definizione dell'indicizzatore è possibile impostare facoltativamente i [mapping dei campi](search-indexer-field-mappings.md) per scegliere quali proprietà del documento JSON di origine vengono usate per popolare l'indice di ricerca di destinazione. Quando si usa la modalità di **`jsonArray`** analisi, ad esempio, se la matrice esiste come proprietà di livello inferiore, è possibile impostare una **`document root`** proprietà che indica il punto in cui la matrice viene posizionata all'interno del BLOB.

Le sezioni seguenti descrivono ogni modalità in modo più dettagliato. Se non si ha familiarità con i client e i concetti relativi all'indicizzatore, vedere [creare un indicizzatore di ricerca](search-howto-create-indexers.md). È anche necessario avere familiarità con i dettagli della [configurazione dell'indicizzatore BLOB di base](search-howto-indexing-azure-blob-storage.md), che non è ripetuta qui.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indicizzare singoli documenti JSON (uno per BLOB)

Per impostazione predefinita, gli indicizzatori BLOB analizzano i BLOB JSON come singolo blocco di testo, un documento di ricerca per ogni BLOB in un contenitore. Se il file JSON è strutturato, il documento di ricerca può riflettere tale struttura, con singoli elementi rappresentati come singoli campi. Si supponga, ad esempio, che in Archiviazione BLOB di Azure sia presente il documento JSON seguente:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

L'indicizzatore BLOB analizza il documento JSON in un singolo documento di ricerca, caricando un indice mediante la corrispondenza di "Text", "datePublished" e "Tags" dall'origine con i campi di indice di destinazione con nome identico e tipizzato. Dato un indice con i campi "text", "datePublished" e "tag", l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza che sia definito un mapping di campi nella richiesta.

Sebbene il comportamento predefinito sia un documento di ricerca per ogni BLOB JSON, l'impostazione della modalità di analisi "JSON" modifica i mapping dei campi interni per il contenuto, innalzando di livello i campi all'interno `content` dei campi effettivi nell'indice di ricerca. Un esempio di definizione di indicizzatore per la **`json`** modalità di analisi potrebbe essere simile al seguente:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Come per tutti gli indicizzatori, se i campi non corrispondono chiaramente, è necessario specificare in modo esplicito i singoli [mapping](search-indexer-field-mappings.md) dei campi a meno che non si usi i mapping dei campi impliciti disponibili per i metadati e il contenuto del BLOB, come descritto in [configurazione dell'indicizzatore BLOB di base](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>esempio JSON (file JSON singoli)

Il [set di dati del documento JSON dell'Hotel](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) in GitHub è utile per il test dell'analisi JSON, dove ogni BLOB rappresenta un file JSON strutturato. È possibile caricare i file di dati nell'archiviazione BLOB e utilizzare la procedura guidata **Importa dati** per valutare rapidamente il modo in cui questo contenuto viene analizzato nei singoli documenti di ricerca. 

Il set di dati è costituito da cinque BLOB, ognuno dei quali contiene un documento di un hotel con una raccolta di indirizzi e una raccolta di chat room. L'indicizzatore BLOB rileva entrambe le raccolte e riflette la struttura dei documenti di input nello schema dell'indice.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizza matrici JSON

In alternativa, è possibile usare l'opzione di matrice JSON. Questa opzione è utile quando i BLOB contengono una matrice di oggetti JSON ben formati e si vuole che ogni elemento diventi un documento di ricerca separato. Usando **`jsonArrays`** , il BLOB JSON seguente produce tre documenti distinti, ognuno con `"id"` `"text"` campi e.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

La **`parameters`** proprietà nell'indicizzatore contiene i valori della modalità di analisi. Per una matrice JSON, la definizione di indicizzatore dovrebbe avere un aspetto simile all'esempio seguente.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>esempio jsonArrays (dati di esempio di Trials clinici)

Il [set di dati JSON Trials clinici](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) su GitHub è utile per il test dell'analisi di matrici JSON. È possibile caricare i file di dati nell'archiviazione BLOB e utilizzare la procedura guidata **Importa dati** per valutare rapidamente il modo in cui questo contenuto viene analizzato nei singoli documenti di ricerca. 

Il set di dati è costituito da otto BLOB, ognuno dei quali contiene una matrice JSON di entità, per un totale di 100 entità. Le entità variano a seconda dei campi popolati, ma il risultato finale è un documento di ricerca per entità, da tutte le matrici, in tutti i BLOB.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analisi di matrici JSON annidate

Per le matrici JSON con elementi annidati, è possibile specificare un **`documentRoot`** per indicare una struttura a più livelli. Ad esempio, se i BLOB sono simili a questo:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Usare questa configurazione per indicizzare la matrice contenuta nella proprietà `level2`:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analizza entità JSON separate da nuove righe

Se il BLOB contiene più entità JSON separate da una nuova riga e si vuole che ogni elemento diventi un documento di ricerca separato, usare **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Per le righe JSON, la definizione dell'indicizzatore dovrebbe essere simile all'esempio seguente.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>esempio di jsonLines (dati di esempio giurisprudenza)

Il [set di dati JSON giurisprudenza](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) su GitHub è utile per il test dell'analisi di nuove righe JSON. Come per gli altri esempi, è possibile caricare i dati nell'archivio BLOB e usare la procedura guidata **Importa dati** per valutare rapidamente l'effetto della modalità di analisi nei singoli BLOB.

Il set di dati è costituito da un BLOB contenente 10 entità JSON separate da una nuova riga, in cui ogni entità descrive un solo caso legale. Il risultato finale è un documento di ricerca per entità.

## <a name="map-json-fields-to-search-fields"></a>Eseguire il mapping di campi JSON ai campi di ricerca

I mapping dei campi vengono utilizzati per associare un campo di origine a un campo di destinazione in situazioni in cui i nomi dei campi e i tipi non sono identici. Tuttavia, i mapping dei campi possono essere usati anche per trovare una corrispondenza tra parti di un documento JSON e "elevarle" ai campi di primo livello del documento di ricerca.

Nell'esempio seguente viene illustrato questo scenario. Per ulteriori informazioni sui mapping dei campi in generale, vedere [mapping dei campi](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Si supponga di avere un indice di ricerca con i campi seguenti: `text` di tipo `Edm.String`, `date` di tipo `Edm.DateTimeOffset` e `tags` di tipo `Collection(Edm.String)`. Si noti la discrepanza tra "datePublished" nell'origine e il campo `date` nell'indice. Per eseguire il mapping del file JSON per ottenere la forma desiderata, usare i mapping dei campi seguenti:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

I campi di origine vengono specificati usando la notazione del [puntatore JSON](https://tools.ietf.org/html/rfc6901) . Iniziare con una barra per fare riferimento alla radice del documento JSON, quindi passare alla proprietà desiderata, a un livello di annidamento arbitrario, usando un percorso separato da barre.

È anche possibile fare riferimento a singoli elementi della matrice usando un indice a base zero. Ad esempio, per selezionare il primo elemento della matrice "tags" dall'esempio precedente, usare un mapping dei campi analogo al seguente:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Se **`sourceFieldName`** fa riferimento a una proprietà che non esiste nel BLOB JSON, il mapping viene ignorato senza errori. Questo comportamento consente l'indicizzazione per continuare per i BLOB JSON con uno schema diverso, ovvero un caso d'uso comune. Poiché non è presente alcun controllo di convalida, verificare con attenzione i mapping per gli errori di digitazione in modo da non perdere i documenti per un motivo errato.
>

## <a name="next-steps"></a>Passaggi successivi

+ [Configurare gli indicizzatori BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Definire i mapping dei campi](search-indexer-field-mappings.md)
+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Come indicizzare i BLOB CSV con un indicizzatore BLOB](search-howto-index-csv-blobs.md)
+ [Esercitazione: eseguire la ricerca di dati semistrutturati dall'archivio BLOB di Azure](search-semi-structured-data.md)