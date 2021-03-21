---
title: Eseguire ricerche su BLOB CSV
titleSuffix: Azure Cognitive Search
description: Estrarre e Importare CSV dall'archiviazione BLOB di Azure usando la modalità di analisi delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430480"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Come indicizzare i BLOB CSV usando la modalità di analisi delimitedText e gli indicizzatori BLOB in Azure ricerca cognitiva

L' [indicizzatore](search-howto-indexing-azure-blob-storage.md) Azure ricerca cognitiva BLOB fornisce una `delimitedText` modalità di analisi per i file CSV che tratta ogni riga nel volume condiviso cluster come documento di ricerca separato. Ad esempio, dato il seguente testo delimitato da virgole, `delimitedText` comporterebbe due documenti nell'indice di ricerca: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Senza la `delimitedText` modalità di analisi, l'intero contenuto del file CSV verrebbe trattato come un unico documento di ricerca.

Ogni volta che si creano più documenti di ricerca da un singolo BLOB, assicurarsi di esaminare i [BLOB di indicizzazione per produrre più documenti di ricerca](search-howto-index-one-to-many-blobs.md) per comprendere il funzionamento delle assegnazioni di chiavi dei documenti. L'indicizzatore BLOB è in grado di trovare o generare valori che definiscono in modo univoco ogni nuovo documento. In particolare, è possibile creare un transitorio `AzureSearch_DocumentKey` che viene generato quando un BLOB viene analizzato in parti più piccole, in cui il valore viene quindi utilizzato come chiave del documento di ricerca nell'indice.

## <a name="setting-up-csv-indexing"></a>Configurazione dell'indicizzazione di CSV

Per indicizzare i BLOB CSV, creare o aggiornare una definizione dell'indicizzatore con la `delimitedText` modalità di analisi in una richiesta [create Indexer](/rest/api/searchservice/create-indexer) :

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` indica che la prima riga (non vuota) di ogni BLOB contiene un'intestazione.
Se i BLOB non contengono una riga di intestazione iniziale, è necessario specificare le intestazioni nella configurazione dell'indicizzatore: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

È possibile personalizzare il carattere di delimitazione usando l'impostazione di configurazione `delimitedTextDelimiter`. Ad esempio:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> È attualmente supportata solo la codifica UTF-8. Se è necessario il supporto per altre codifiche, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando si usa la modalità di analisi del testo delimitata, Azure ricerca cognitiva presuppone che tutti i BLOB nell'origine dati siano CSV. Se è necessario supportare una combinazione di BLOB di tipo CSV e non CSV nella stessa origine dati, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Esempi di richiesta

Per concludere, ecco gli esempi completi del payload. 

Origine dati: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indicizzatore:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


