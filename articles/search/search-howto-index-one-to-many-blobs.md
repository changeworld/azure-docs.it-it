---
title: BLOB di indici contenenti più documenti
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione di BLOB di Azure per contenuti di testo tramite l'indicizzatore BLOB di Azure ricerca cognitiva, in cui ogni BLOB può restituire uno o più documenti dell'indice
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430981"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indicizzazione di BLOB per produrre più documenti di ricerca

Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come singolo documento di ricerca. Se si vuole una rappresentazione più granulare del BLOB in un indice di ricerca, è possibile impostare i valori **parsingMode** per creare più documenti di ricerca da un BLOB. I valori di **parsingMode** che generano molti documenti di ricerca includono `delimitedText` (per [CSV](search-howto-index-csv-blobs.md)) e `jsonArray` o `jsonLines` (per [JSON](search-howto-index-json-blobs.md)).

Quando si usa una di queste modalità di analisi, i nuovi documenti di ricerca che emergono devono avere chiavi di documento univoche e si verifica un problema nella determinazione della provenienza del valore. Il BLOB padre ha almeno un valore univoco nel formato `metadata_storage_path property` , ma se contribuisce a tale valore a più di un documento di ricerca, la chiave non è più univoca nell'indice.

Per risolvere questo problema, l'indicizzatore BLOB genera un `AzureSearch_DocumentKey` che identifica in modo univoco ogni documento di ricerca figlio creato dal singolo elemento padre del BLOB. Questo articolo illustra il funzionamento di questa funzionalità.

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti

Ogni documento visualizzato in un indice di ricerca cognitiva di Azure viene identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e non esiste alcun [mapping esplicito dei campi](search-indexer-field-mappings.md) nella definizione dell'indicizzatore per la chiave del documento di ricerca, l'indicizzatore BLOB esegue automaticamente il mapping `metadata_storage_path property` di come chiave del documento. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto e consente di evitare di dover creare questo mapping del campo. in genere, vengono automaticamente mappati solo i campi con nomi e tipi identici.

Quando si usa una delle modalità di analisi sopra elencate, un BLOB viene mappato a "molti" documenti di ricerca, rendendo una chiave del documento esclusivamente basata sui metadati del BLOB non idonei. Per ovviare a questo vincolo, Azure ricerca cognitiva è in grado di generare una chiave di documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà è denominata AzureSearch_DocumentKey e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è sicuramente univoco per ogni singola entità nei BLOB e le entità verranno visualizzate come documenti di ricerca distinti.

Per impostazione predefinita, quando non viene specificato alcun mapping esplicito dei campi per il campo indice chiave, `AzureSearch_DocumentKey` viene eseguito il mapping di a tale campo, usando la `base64Encode` funzione di mapping dei campi.

## <a name="example"></a>Esempio

Si supponga di avere una definizione di indice con i campi seguenti:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB contiene BLOB con la struttura seguente:

_Blob1.js_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.js_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Quando si crea un indicizzatore e si imposta **parsingMode** su `jsonLines` -senza specificare alcun mapping esplicito dei campi per il campo chiave, il mapping seguente verrà applicato in modo implicito.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Questa configurazione genera chiavi del documento ambiguità, in modo simile alla figura seguente (l'ID con codifica Base64 viene abbreviato per brevità).

| ID | temperatura | pressione |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave di indice

Supponendo la stessa definizione di indice dell'esempio precedente, si supponga che il contenitore BLOB disponga di BLOB con la struttura seguente:

_Blob1.js_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.js_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, potrebbe sembrare naturale impostare una funzione di mapping dei campi nel campo chiave come indicato di seguito:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Tuttavia, questo mapping _non_ comporterà la visualizzazione di 4 documenti nell'indice, perché il `recordid` campo non è univoco _tra i BLOB_. È quindi consigliabile usare il mapping di campi implicito applicato dalla `AzureSearch_DocumentKey` proprietà al campo indice chiave per le modalità di analisi "uno-a-molti".

Se si vuole impostare un mapping di campi esplicito, assicurarsi che _campoOrigine_ sia distinto per ogni singola entità **in tutti i BLOB**.

> [!NOTE]
> L'approccio utilizzato da `AzureSearch_DocumentKey` per garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è necessario basarsi sul valore per le esigenze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se non si ha già familiarità con la struttura di base e il flusso di lavoro di indicizzazione BLOB, è consigliabile esaminare prima di tutto l' [indicizzazione dell'archiviazione BLOB di Azure con azure ricerca cognitiva](search-howto-index-json-blobs.md) Per altre informazioni sulle modalità di analisi per i diversi tipi di contenuto BLOB, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) 
>  [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md)
