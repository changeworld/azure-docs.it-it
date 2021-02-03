---
title: Eseguire ricerche in BLOB in testo normale
titleSuffix: Azure Cognitive Search
description: Configurare un indicizzatore di ricerca per estrarre testo normale dai BLOB di Azure per la ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509453"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Come indicizzare i BLOB in testo normale in Azure ricerca cognitiva

Quando si usa un [indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md) per estrarre il testo BLOB ricercabile per la ricerca full-text, è possibile assegnare una modalità di analisi per ottenere risultati di indicizzazione migliori. Per impostazione predefinita, l'indicizzatore analizza il contenuto del BLOB come singolo blocco di testo. Tuttavia, se tutti i BLOB contengono testo normale nella stessa codifica, è possibile migliorare significativamente le prestazioni di indicizzazione usando la `text` modalità di analisi.

Le indicazioni per l'uso dell' `text` analisi includono:

+ Il tipo di file è. txt
+ I file sono di qualsiasi tipo, ma il contenuto stesso è un testo, ad esempio codice sorgente del programma, HTML, XML e così via. Per i file in un linguaggio di contrassegno, qualsiasi carattere di sintassi viene attraversato come testo statico.

Ricordare che tutti gli indicizzatori serializzano in JSON. Per impostazione predefinita, il contenuto dell'intero file di testo verrà indicizzato all'interno di un campo di grandi dimensioni come `"content": "<file-contents>"` . Le nuove istruzioni di riga e di ritorno vengono incorporate nel campo del contenuto ed espresse come `\r\n\` .

Se si desidera un risultato più granulare e se il tipo di file è compatibile, prendere in considerazione le soluzioni seguenti:

+ [`delimitedText`](search-howto-index-csv-blobs.md) modalità di analisi, se l'origine è CSV
+ [ `jsonArray` o `jsonLines` ](search-howto-index-json-blobs.md), se l'origine è JSON

Una terza opzione per suddividere il contenuto in più parti richiede funzionalità avanzate sotto forma di [arricchimento intelligenza artificiale](cognitive-search-concept-intro.md). Viene aggiunta un'analisi che identifica e assegna blocchi del file a campi di ricerca diversi. È possibile trovare una soluzione completa o parziale tramite le [competenze predefinite](cognitive-search-predefined-skills.md), ma una soluzione più probabile è costituita da un modello di apprendimento che comprende il contenuto, articolato in un modello di apprendimento personalizzato, racchiuso in un' [abilità personalizzata](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Configurare l'indicizzazione di testo normale

Per indicizzare i BLOB di testo normale, creare o aggiornare una definizione dell'indicizzatore con la `parsingMode` proprietà di configurazione a `text` in una richiesta [create Indexer](/rest/api/searchservice/create-indexer) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Per impostazione predefinita, verrà utilizzata la codifica `UTF-8`. Per specificare una codifica diversa, utilizzare la proprietà di configurazione `encoding`: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Esempio di richiesta

Le modalità di analisi sono specificate nella definizione dell'indicizzatore.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Passaggi successivi

+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)