---
title: Esempi JavaScript
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice JavaScript di Azure ricerca cognitiva demo che usano Azure .NET SDK per JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955021"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Esempi di codice JavaScript per ricerca cognitiva di Azure

Informazioni sugli esempi di codice JavaScript che illustrano la funzionalità e il flusso di lavoro di una soluzione Azure ricerca cognitiva. Questi esempi usano la [**libreria client di azure ricerca cognitiva**](/javascript/api/overview/azure/search-documents-readme) per [**Azure SDK per JavaScript**](/azure/developer/javascript/), che è possibile esplorare tramite i collegamenti seguenti.

| Destinazione | Collegamento |
|--------|------|
| Download del pacchetto | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| Informazioni di riferimento sulle API | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Test case API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Codice sorgente | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>Esempi di SDK

Gli esempi di codice del team di sviluppo di Azure SDK illustrano l'utilizzo dell'API. È possibile trovare questi esempi in [**Azure-SDK-for-JS/tree/master/SDK/Search/Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) su GitHub.

### <a name="javascript-sdk-samples"></a>Esempi di JavaScript SDK

| Esempi | Descrizione |
|---------|-------------|
| [indici](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). Questa categoria di esempio include anche un esempio di statistica del servizio. |
| [dataSourceConnections (per gli indicizzatori)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [indicizzatori](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md).|
| [Competenze](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Query](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Viene illustrata l'esecuzione di query su un indice pubblico di sola lettura ospitato da Microsoft.  |

### <a name="typescript-samples"></a>Esempi di TypeScript

| Esempi | Descrizione |
|---------|-------------|
| [indici](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). Questa categoria di esempio include anche un esempio di statistica del servizio. |
| [dataSourceConnections (per gli indicizzatori)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [indicizzatori](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md).|
| [Competenze](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Query](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Viene illustrata l'esecuzione di query su un indice pubblico di sola lettura ospitato da Microsoft.  |

## <a name="doc-samples"></a>Esempi di documenti

Gli esempi di codice del team di ricerca cognitiva illustrano le funzionalità e i flussi di lavoro. Molti di questi esempi fanno riferimento a esercitazioni, guide introduttive e articoli sulle procedure. È possibile trovare questi esempi in [**Azure-Samples/Azure-Search-JavaScript-Samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) in GitHub.

| Esempi | Articolo |
|---------|---------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in JavaScript](search-get-started-javascript.md). Questo articolo illustra il flusso di lavoro di base per la creazione, il caricamento e l'esecuzione di query su un indice di ricerca usando dati di esempio. |

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=javascript&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="other-samples"></a>Altri esempi

Gli esempi seguenti sono pubblicati anche dal team di ricerca cognitiva, ma non vi viene fatto riferimento nella documentazione. I file Leggimi associati forniscono istruzioni di utilizzo.

| Esempi | Descrizione |
|---------|-------------|
| [Azure-Search-React-template](https://github.com/dereklegenzoff/azure-search-react-template) | Modello React per Azure ricerca cognitiva (github.com) |