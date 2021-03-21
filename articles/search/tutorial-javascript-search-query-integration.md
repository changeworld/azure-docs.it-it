---
title: "Esercitazione su JavaScript: ricerca in evidenza dell'integrazione"
titleSuffix: Azure Cognitive Search
description: Informazioni sulle query di ricerca di JavaScript SDK usate nel sito Web abilitato per la ricerca
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726876"
---
# <a name="4---search-integration-highlights"></a>4-ricerca in evidenza di integrazione

Nelle lezioni precedenti è stata aggiunta la ricerca a un'app Web statica. In questa lezione vengono illustrati i passaggi essenziali per la definizione dell'integrazione. Se si sta cercando un foglio informativo su come integrare la ricerca nell'app JavaScript, questo articolo spiega cosa è necessario conoscere.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

L'app per le funzioni USA Azure SDK per ricerca cognitiva:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Documentazione di riferimento: [libreria client](/javascript/api/overview/azure/search-documents-readme)

L'app per le funzioni esegue l'autenticazione tramite l'SDK per l'API ricerca cognitiva basata sul cloud usando il nome della risorsa, la chiave di risorsa e il nome dell'indice. I segreti vengono archiviati nelle impostazioni dell'app Web statica ed estratti nella funzione come variabili di ambiente. 

## <a name="configure-secrets-in-a-configuration-file"></a>Configurare i segreti in un file di configurazione

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Funzione di Azure: eseguire una ricerca nel catalogo

L' `Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) accetta un termine di ricerca e Cerca tra i documenti nell'indice di ricerca, restituendo un elenco di corrispondenze. 

Il routing per l'API di ricerca è contenuto nel [function.jssulle](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) associazioni.

La funzione di Azure estrae le informazioni di configurazione della ricerca e soddisfa la query.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Client: eseguire la ricerca dal catalogo

Chiamare la funzione di Azure nel client React con il codice seguente. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Funzione di Azure: suggerimenti del catalogo

L' `Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) accetta un termine di ricerca durante la digitazione di un utente e suggerisce i termini di ricerca, ad esempio titoli di libri e autori nei documenti nell'indice di ricerca, restituendo un piccolo elenco di corrispondenze. 

Il suggerimento per la ricerca, `sg` , è definito nel [file di schema](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) usato durante il caricamento bulk.

Il routing per l'API suggest è contenuto nel [function.jssulle](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) associazioni.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Client: suggerimenti dal catalogo

L'API della funzione Suggerisci viene chiamata nell'app React nell' `\src\components\SearchBar\SearchBar.js` ambito dell'inizializzazione dei componenti:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Funzione di Azure: ottenere un documento specifico 

L' `Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) accetta un ID e restituisce l'oggetto documento dall'indice di ricerca. 

Il routing per l'API di ricerca è contenuto nel [function.jssulle](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) associazioni.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Client: ottenere un documento specifico 

Questa API della funzione viene chiamata nell'app React nell' `\src\pages\Details\Detail.js` ambito dell'inizializzazione dei componenti:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzare i dati SQL di Azure](search-indexer-tutorial.md)
