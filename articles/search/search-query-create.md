---
title: Creare una query di base
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare una richiesta di query in ricerca cognitiva, gli strumenti e le API da usare per il test e il codice e il modo in cui le decisioni relative alle query iniziano con la progettazione degli indici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371175"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Creare una query di base in Azure ricerca cognitiva

Questo articolo illustra in dettaglio la creazione di query. Gli esempi sono in REST, in modo che sia possibile copiare le stringhe in **Esplora ricerche** nel portale oppure compilare query in modo interattivo, usando il post o il codice di Visual Studio. Per gli esempi in questo articolo, è possibile usare qualsiasi livello o versione di ricerca cognitiva.

## <a name="choose-a-tool-or-api"></a>Scegliere uno strumento o un'API

È possibile scegliere tra gli strumenti e le API seguenti per creare query per carichi di lavoro di test o di produzione.

| Metodologia | Description |
|-------------|-------------|
| Portale| [Esplora ricerche (portale)](search-explorer.md) fornisce una barra di ricerca e le opzioni per l'indice e le selezioni della versione API. I risultati vengono restituiti come documenti JSON. Consigliato per le prime indagini, i test e la convalida. <br/>[Altre informazioni.](search-explorer.md) |
| Strumenti di test Web| Il [post o il Visual Studio Code](search-get-started-rest.md) sono scelte sicure per la formulazione delle chiamate REST di [ricerca nei documenti](/rest/api/searchservice/search-documents) . L'API REST supporta tutte le operazioni a livello di programmazione in Azure ricerca cognitiva, quindi è possibile inviare richieste in modo interattivo per comprenderne il funzionamento prima di investire nel codice.  |
| Azure SDK | È possibile usare [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) per eseguire query su un indice di ricerca in C#.  [Altre informazioni.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) può essere usato per eseguire query in un indice di ricerca in Python. [Altre informazioni.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) può essere usato per eseguire query in un indice di ricerca in JavaScript. [Altre informazioni.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Configurare un client di ricerca

Un client di ricerca esegue l'autenticazione al servizio di ricerca, invia le richieste e gestisce le risposte. Le query vengono sempre indirizzate alla raccolta Documents di un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query.

### <a name="in-the-portal"></a>Nel portale

Esplora ricerche e altri strumenti del portale hanno una connessione client incorporata al servizio, con indici di accesso diretto e altri oggetti dalle pagine del portale. Per accedere a strumenti, procedure guidate e oggetti si presuppone che l'utente disponga dei diritti amministrativi per il servizio. Con Esplora ricerche è possibile concentrarsi specificando la stringa di ricerca e altri parametri. 

### <a name="using-rest"></a>Uso di REST

Per le chiamate REST, è possibile usare il [post o strumenti simili](search-get-started-rest.md) come il client per specificare una richiesta di [ricerca di documenti](/rest/api/searchservice/search-documents) . Ogni richiesta è autonoma, pertanto è necessario fornire l'endpoint (URL del servizio) e una chiave API di amministrazione o query per l'accesso. A seconda della richiesta, l'URL può includere anche il nome dell'indice, la raccolta Documents e altre proprietà. Alcune proprietà, ad esempio Content-Type e chiave API, vengono passate nell'intestazione della richiesta. È possibile passare altri parametri sull'URL o nel corpo della richiesta. Per tutte le chiamate REST è necessaria una chiave API per l'autenticazione e una versione API.

### <a name="using-azure-sdks"></a>Uso di Azure SDK

Azure SDK fornisce ai client di ricerca che possono rendere permanente lo stato, consentendo il riutilizzo della connessione. Per le operazioni di query, è necessario creare un'istanza di SearchClient e fornire i valori per le proprietà seguenti: endpoint, Key, index. È quindi possibile chiamare il metodo Search per fornire la stringa di query. 

| Linguaggio | Client | Esempio |
|----------|--------|---------|
| C# e .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Inviare la prima query di ricerca in C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Inviare la prima query di ricerca in Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Inviare la prima query di ricerca in Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Invia la prima query di ricerca in JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Azure ricerca cognitiva offre una scelta tra due parser di query per la gestione di query tipiche e specializzate. Le richieste che usano il parser semplice sono in genere query di ricerca full-text, formulate usando la [sintassi di query semplice](query-simple-syntax.md), selezionate come impostazione predefinita per la velocità e l'efficacia nelle query di testo in formato libero. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), che viene abilitata quando si aggiunge `queryType=full` alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). La Sintassi completa estende la sintassi semplice. Qualsiasi query scritta per la sintassi semplice viene eseguita con il parser di Lucene full. 

Gli esempi seguenti illustrano il punto: la stessa query, ma con **`queryType`** impostazioni diverse che producono risultati diversi. Nella prima query il metodo `^3` after `historic` viene considerato come parte del termine di ricerca. Il risultato in primo piano per questa query è "Marquis Plaza & Suites", che ha l' *oceano* nella sua descrizione.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

La stessa query che usa il parser Lucene completo interpreta `^3` come un richiamo di termine nel campo. Il passaggio dei parser comporta la modifica del rango, con risultati che contengono il termine spostamento *cronologico* verso l'alto.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Abilitare i comportamenti di query in un indice

La progettazione degli indici e la progettazione delle query sono strettamente associate in Azure ricerca cognitiva. Lo *schema dell'indice*, con attributi su ogni campo, determina il tipo di query che è possibile compilare.

Gli attributi dell'indice in un campo impostano le operazioni consentite - se un campo è *ricercabile* nell'indice *recuperabile* nei risultati *ordinabile*, *filtrabile* e così via. Nelle query di esempio, `"$orderby": "Rating desc"` funziona solo perché il campo rating è contrassegnato come *ordinabile* nello schema dell'indice.

![Definizione di indice per l'esempio di Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione di indice per l'esempio di Hotel")

Lo screenshot precedente è un elenco parziale degli attributi di indice per l' [indice di esempio Hotels](search-get-started-portal.md). È possibile creare e visualizzare l'intero schema dell'indice nel portale. Per altre informazioni sugli attributi degli indici, vedere [create index (API REST)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come viene costruita la richiesta, provare esempi con la sintassi semplice e completa.

+ [Esempi di query semplici](search-query-simple-examples.md)
+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)