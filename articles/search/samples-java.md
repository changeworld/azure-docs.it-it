---
title: Esempi di Java
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice Java ricerca cognitiva demo di Azure che usano Azure .NET SDK per Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955038"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Esempi di codice Java per Azure ricerca cognitiva

Informazioni sugli esempi di codice Java che illustrano la funzionalità e il flusso di lavoro di una soluzione Azure ricerca cognitiva. Questi esempi usano la [**libreria client di azure ricerca cognitiva**](/java/api/overview/azure/search-documents-readme) per [**Azure SDK per Java**](/azure/developer/java/sdk), che è possibile esplorare tramite i collegamenti seguenti.

| Destinazione | Collegamento |
|--------|------|
| Download del pacchetto | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Informazioni di riferimento sulle API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Test case API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Codice sorgente | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Esempi di SDK

Gli esempi di codice del team di sviluppo di Azure SDK illustrano l'utilizzo dell'API. È possibile trovare questi esempi in [**Azure/Azure-SDK-for-Java/tree/master/SDK/Search/Azure-search-documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) su GitHub.

| Esempi | Descrizione |
|---------|-------------|
| [Creazione dell'indice di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Viene illustrato come creare [indici di ricerca](search-what-is-an-index.md). |
| [Creazione di sinonimi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Viene illustrato come creare [mappe sinonimo](search-synonyms.md).  |
| [Creazione dell'indicizzatore di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Viene illustrato come creare gli [indicizzatori](search-indexer-overview.md). |
| [Creazione dell'origine dati dell'indicizzatore di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Viene illustrato come creare origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [Creazione di competenze](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Viene illustrato come creare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [Caricare i documenti](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Viene illustrato come caricare o unire i documenti in un indice in un'operazione di [importazione dati](search-what-is-data-import.md) . |
| [Sintassi di query](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Viene illustrato come impostare una [query di base](search-query-overview.md). |

## <a name="doc-samples"></a>Esempi di documenti

Gli esempi di codice del team di ricerca cognitiva illustrano le funzionalità e i flussi di lavoro. Molti di questi esempi fanno riferimento a esercitazioni, guide introduttive e articoli sulle procedure. È possibile trovare questi esempi in [**Azure-Samples/Azure-Search-java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) in GitHub.

| Esempi | Articolo | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in Java e REST](search-get-started-java.md). Questo esempio non è stato aggiornato per Java SDK. Chiama le API REST. |

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=java&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="other-samples"></a>Altri esempi

Gli esempi seguenti sono pubblicati anche dal team di ricerca cognitiva, ma non vi viene fatto riferimento nella documentazione. I file Leggimi associati forniscono istruzioni di utilizzo.

| Esempi | Descrizione |
|---------|-------------|
| [ricerca-Java-Getting-Started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Usa la libreria client Java SDK per creare, caricare ed eseguire query su un indice di ricerca. Questo esempio è attualmente autonomo. |
| [ricerca-Java-Indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Viene illustrato un indicizzatore Azure Cosmos DB in Java. Questo esempio non è stato aggiornato per Java SDK. Chiama le API REST.|