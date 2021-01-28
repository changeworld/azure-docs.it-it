---
title: Esempi .NET
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice C# di Azure ricerca cognitiva demo che usano le librerie client .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953904"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Esempi di codice .NET (C#) per Azure ricerca cognitiva

Informazioni sugli esempi di codice C# che illustrano la funzionalità e il flusso di lavoro di una soluzione Azure ricerca cognitiva. Questi esempi usano la [**libreria client di azure ricerca cognitiva**](/dotnet/api/overview/azure/search) per [**Azure SDK per .NET**](/dotnet/azure/), che è possibile esplorare tramite i collegamenti seguenti.

| Destinazione | Collegamento |
|--------|------|
| Download del pacchetto | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Informazioni di riferimento sulle API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Test case API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/test](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Codice sorgente | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Esempi di SDK

Gli esempi di codice del team di sviluppo di Azure SDK illustrano l'utilizzo dell'API. È possibile trovare questi esempi in [**Azure/Azure-SDK-for-NET/tree/master/SDK/Search/Azure.Search.Documents/Samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) su GitHub.

| Esempi | Descrizione |
|---------|-------------|
| ["Hello World", in modo sincrono](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Viene illustrato come creare un client, autenticare e gestire gli errori usando metodi sincroni.|
| ["Hello World", in modo asincrono](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Viene illustrato come creare un client, autenticare e gestire gli errori usando i metodi asincroni.  |
| [Operazioni a livello di servizio](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Viene illustrato come creare indici, indicizzatori, origini dati, skillsets e mappe sinonimo. Questo esempio illustra anche come ottenere le statistiche del servizio e come eseguire una query su un indice.  |
| [Operazioni sugli indici](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Viene illustrato come eseguire un'azione sull'indice esistente, in questo caso ottenendo un conteggio dei documenti archiviati nell'indice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Viene illustrata una tecnica per l'utilizzo di tipi di dati non supportati.  |
| [Indicizzazione di documenti (modello push)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indicizzazione del modello "push", in cui si invia un payload JSON a un indice in un servizio.   |
| [Esempio di chiave di crittografia](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Viene illustrato l'uso di una chiave di crittografia gestita dal cliente per aggiungere un livello aggiuntivo di protezione rispetto al contenuto sensibile.  |

## <a name="doc-samples"></a>Esempi di documenti

Gli esempi di codice del team di ricerca cognitiva illustrano le funzionalità e i flussi di lavoro. Molti di questi esempi fanno riferimento a esercitazioni, guide introduttive e articoli sulle procedure. È possibile trovare questi esempi in [**Azure-Samples/Azure-Search-DotNet-Samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) e in [**Azure-Samples/Search-DotNet-Getting-Started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) su GitHub.

| Esempi | Articolo  |
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca ](search-get-started-dotnet.md). Questo articolo illustra il flusso di lavoro di base per la creazione, il caricamento e l'esecuzione di query su un indice di ricerca usando dati di esempio. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Codice sorgente per [l'utilizzo della libreria client .NET](search-howto-dotnet-sdk.md). Questo articolo illustra il flusso di lavoro di base, ma in modo più dettagliato e in discussione sull'utilizzo dell'API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Codice sorgente [, ad esempio: aggiungere sinonimi in C#](search-synonyms-tutorial-sdk.md). Gli elenchi di sinonimi vengono utilizzati per l'espansione delle query, fornendo termini corrispondenti esterni a un indice. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Codice sorgente per [esercitazione: indicizzare i dati SQL di Azure con .NET SDK](search-indexer-tutorial.md). Questo articolo illustra come configurare un indicizzatore SQL di Azure con una pianificazione, i mapping dei campi e i parametri.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Codice sorgente per [la configurazione delle chiavi gestite dal cliente per la crittografia dei dati](search-security-manage-encryption-keys.md). |
| [Crea la tua prima app in C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Codice sorgente per [esercitazione: creare la prima app di ricerca](tutorial-csharp-create-first-app.md). Sebbene la maggior parte degli esempi siano applicazioni console, questo esempio MVC usa una pagina Web per individuare l'indice degli hotel di esempio, dimostrando la ricerca di base, l'impaginazione, il completamento automatico e le query suggerite, i facet e i filtri. |
| [più origini dati](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Codice sorgente per [esercitazione: Indice da più origini dati](tutorial-multiple-data-sources.md). |
|  [ottimizzazione-dati-indicizzazione](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Codice sorgente per [esercitazione: ottimizzare l'indicizzazione con l'API push](tutorial-optimize-indexing-push-api.md).  |
| [esercitazione-intelligenza artificiale](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Codice sorgente per [esercitazione: contenuto ricercabile generato da ai BLOB di Azure con .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=csharp&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="other-samples"></a>Altri esempi

Gli esempi seguenti sono pubblicati anche dal team di ricerca cognitiva, ma non vi viene fatto riferimento nella documentazione. I file Leggimi associati forniscono istruzioni di utilizzo.

| Esempi | Descrizione |
|---------|-------------|
| [Azure-Search-Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Codice sorgente per competenze personalizzate utilizzabili che è possibile incorporare nelle soluzioni vinte.  |
| [Acceleratore di soluzione Knowledge Mining](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Include modelli, file di supporto e report analitici che consentono di eseguire il prototipo di una soluzione di Knowledge mining end-to-end.  |
| [Covid-19 Cerca repository app](https://github.com/liamca/covid19search) | Repository del codice sorgente per l' [app Covid-19 Search](https://covid19search.azurewebsites.net/) basata su ricerca cognitiva |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Scopri di più sulla [soluzione JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |