---
title: Esempi per Python
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice Python di Azure ricerca cognitiva demo che usano Azure .NET SDK per Python o REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955123"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Esempi di codice Python per Azure ricerca cognitiva

Informazioni sugli esempi di codice Python che illustrano la funzionalità e il flusso di lavoro di una soluzione Azure ricerca cognitiva. Questi esempi usano la [**libreria client di azure ricerca cognitiva**](/python/api/overview/azure/search-documents-readme) per [**Azure SDK per Python**](/azure/developer/python/), che è possibile esplorare tramite i collegamenti seguenti.

| Destinazione | Collegamento |
|--------|------|
| Download del pacchetto | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| Informazioni di riferimento sulle API | [azure-search-documents](/python/api/azure-search-documents)  |
| Test case API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Codice sorgente | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>Esempi di SDK

Gli esempi di codice del team di sviluppo di Azure SDK illustrano l'utilizzo dell'API. È possibile trovare questi esempi in [**Azure-SDK-for-Python/tree/master/SDK/Search/Azure-search-documents/Samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) su GitHub.

| Esempi | Descrizione |
|---------|-------------|
| [Autentica](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Viene illustrato come configurare un client ed eseguire l'autenticazione al servizio. | 
| [Operazioni di creazione dell'indice-lettura-aggiornamento-eliminazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). |
| [Operazioni di creazione dell'indicizzatore-lettura-aggiornamento-eliminazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md). |
| [Cerca origini dati dell'indicizzatore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [Sinonimi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Caricare i documenti](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Viene illustrato come caricare o unire i documenti in un indice in un'operazione di [importazione dati](search-what-is-data-import.md) . |
| [Query semplice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Viene illustrato come impostare una [query di base](search-query-overview.md). |
| [Filtra query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Viene illustrata la configurazione di un' [espressione di filtro](search-filters.md). |
| [Query facet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Viene illustrato l'utilizzo di [facet](search-filters-facets.md). |

## <a name="doc-samples"></a>Esempi di documenti

Gli esempi di codice del team di ricerca cognitiva illustrano le funzionalità e i flussi di lavoro. Molti di questi esempi fanno riferimento a esercitazioni, guide introduttive e articoli sulle procedure. È possibile trovare questi esempi in [**Azure-Samples/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) in GitHub.

| Esempi | Articolo |
|---------|---------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in Python](search-get-started-python.md). Questo articolo illustra il flusso di lavoro di base per la creazione, il caricamento e l'esecuzione di query su un indice di ricerca usando dati di esempio. |
| [esercitazione-intelligenza artificiale](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Codice sorgente per [esercitazione: usare Python e intelligenza artificiale per generare contenuto ricercabile da BLOB di Azure](cognitive-search-tutorial-blob-python.md). Questo articolo illustra come creare un indicizzatore BLOB con un insieme di competenze cognitive, in cui il livello di competenze crea e trasforma contenuto non elaborato per renderlo ricercabile o utilizzabile. |
| [AzureML-Custom-skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Codice sorgente [, ad esempio, creare una competenza personalizzata usando Python](cognitive-search-custom-skill-python.md). Questo articolo illustra l'integrazione di indicizzatori e competenze con modelli di apprendimento avanzato in Azure Machine Learning. |

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=python&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.