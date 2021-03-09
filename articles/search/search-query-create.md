---
title: Creare una query
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare una richiesta di query in ricerca cognitiva, gli strumenti e le API da usare per il test e il codice e il modo in cui le decisioni relative alle query iniziano con la progettazione degli indici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 5a89e9ae05b0733c865d537ffeb1714d3b3ebef1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489362"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Creazione di query in Azure ricerca cognitiva

Se si compila una query per la prima volta, in questo articolo vengono descritti gli approcci e i metodi per la configurazione delle query. Viene inoltre introdotta una richiesta di query e viene illustrato come gli attributi di campo e gli analizzatori linguistici possono influito sui risultati delle query.

## <a name="whats-a-query-request"></a>Che cos'è una richiesta di query?

Una query è una richiesta di sola lettura per la raccolta docs di un singolo indice di ricerca. Specifica che un parametro ' Search ' contiene l'espressione di query, costituita da termini, frasi racchiuse tra virgolette e operatori.

I parametri aggiuntivi forniscono una maggiore definizione della query e della risposta. Ad esempio,' searchFields ' consente l'esecuzione di query in campi specifici,' Select ' specifica i campi che vengono restituiti nei risultati è count ' restituisce il numero di corrispondenze trovate nell'indice.

L'esempio seguente fornisce un'idea generale di una richiesta di query mostrando un subset dei parametri disponibili. Per altre informazioni sulla composizione di query, vedere [tipi di query e composizioni](search-query-overview.md) e [cercare documenti (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Scegliere un client

È necessario uno strumento come portale di Azure o postazione oppure il codice che crea un'istanza di un client di query usando le API. È consigliabile usare le API portale di Azure o REST per i test iniziali di sviluppo e di prova.

### <a name="permissions"></a>Autorizzazioni

Qualsiasi operazione, incluse le richieste di query, funzionerà con una [chiave API di amministrazione](search-security-api-keys.md), ma le richieste di query possono facoltativamente usare una [chiave API di query](search-security-api-keys.md#create-query-keys). Le chiavi API di query sono fortemente consigliate. È possibile creare fino a 50 per servizio e assegnare chiavi diverse a diverse applicazioni.

In portale di Azure, l'accesso a strumenti, procedure guidate e oggetti richiede l'appartenenza al ruolo Collaboratore o superiore nel servizio. 

### <a name="use-azure-portal-to-query-an-index"></a>Utilizzare portale di Azure per eseguire una query su un indice

[Esplora ricerche (portale)](search-explorer.md) è un'interfaccia di query nel portale di Azure che esegue query sugli indici nel servizio di ricerca sottostante. Internamente, il portale esegue le richieste di [ricerca nei documenti](/rest/api/searchservice/search-documents) , ma non può richiamare il completamento automatico, i suggerimenti o la ricerca di documenti. 

È possibile selezionare qualsiasi indice e la versione dell'API REST, inclusa l'anteprima. Una stringa di query può usare una sintassi semplice o completa, con supporto per tutti i parametri di query (Filter, Select, searchFields e così via). Quando si apre un indice nel portale, è possibile usare Esplora ricerche insieme alla definizione JSON dell'indice nelle schede affiancate per semplificare l'accesso agli attributi dei campi. Verifica i campi disponibili per la ricerca, l'ordinamento, il filtraggio e il facet durante il test delle query.

### <a name="use-a-rest-client"></a>Usare un client REST

Sia il post che la Visual Studio Code (con estensione per Azure ricerca cognitiva) possono fungere da client di query. Utilizzando uno degli strumenti, è possibile connettersi al servizio di ricerca e inviare richieste [Rest (Search Documents)](/rest/api/searchservice/search-documents) . Numerose esercitazioni ed esempi illustrano i client REST per l'esecuzione di query sull'indicizzazione. 

Per informazioni su ogni client (incluse le istruzioni per le query), iniziare con uno di questi articoli:

+ [Creare un indice di ricerca con REST e postazione](search-get-started-rest.md)
+ [Introduzione a Visual Studio Code e a Ricerca cognitiva di Azure](search-get-started-vs-code.md)

Ogni richiesta è autonoma, pertanto è necessario fornire l'endpoint, il nome dell'indice e la versione dell'API a ogni richiesta. Altre proprietà, Content-Type e chiave API, vengono passate nell'intestazione della richiesta. Per ulteriori informazioni, vedere la pagina relativa alla [ricerca di documenti (REST)](/rest/api/searchservice/search-documents) per informazioni sulla formulazione di richieste di query.

### <a name="use-an-sdk"></a>Usare un SDK

Per ricerca cognitiva, gli SDK di Azure implementano le funzionalità disponibili a livello generale. Di conseguenza, è possibile usare uno qualsiasi degli SDK per eseguire una query su un indice. Tutti forniscono un **SearchClient** che dispone di metodi per l'interazione con un indice, dal caricamento di un indice con i documenti di ricerca per la formulazione delle richieste di query.

| Azure SDK | Client | Esempio |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Scegliere un tipo di query: semplice | completo

Se la query è una ricerca full-text, verrà usato un parser di query per elaborare qualsiasi testo passato come termini e frasi di ricerca. Azure ricerca cognitiva offre due parser di query. 

+ Il parser semplice riconosce la [sintassi di query semplice](query-simple-syntax.md). Questo parser è stato selezionato come valore predefinito per la velocità e l'efficacia nelle query di testo in formato libero. La sintassi supporta gli operatori di ricerca comuni (AND, OR, NOT) per le ricerche di termini e frasi e la ricerca di prefisso ( `*` ) (come in "Sea *" per Seattle e Seaside). Una raccomandazione generale è provare prima il parser semplice e quindi passare al parser completo se i requisiti dell'applicazione richiedono query più potenti.

+ La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), abilitata quando si aggiunge `queryType=full` alla richiesta, è basata sul [parser Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

La sintassi completa e la sintassi semplice si sovrappongono alla dimensione che supportano entrambe le stesse operazioni di prefisso e booleano, ma la sintassi completa fornisce più operatori. In completo sono presenti più operatori per le espressioni booleane e più operatori per query avanzate, ad esempio ricerca fuzzy, ricerca con caratteri jolly, ricerca di prossimità ed espressioni regolari.

## <a name="choose-query-methods"></a>Scegliere i metodi di query

La ricerca è fondamentalmente un esercizio guidato dall'utente, in cui i termini o le frasi vengono raccolti da una casella di ricerca o da eventi click in una pagina. Nella tabella seguente vengono riepilogati i meccanismi mediante i quali è possibile raccogliere l'input dell'utente e l'esperienza di ricerca prevista.

| Input | Esperienza |
|-------|---------|
| [Metodo search](/rest/api/searchservice/search-documents) | Un utente digita i termini o le frasi in una casella di ricerca, con o senza operatori, e fa clic su Cerca per inviare la richiesta. La ricerca può essere usata con i filtri nella stessa richiesta, ma non con la funzionalità di completamento automatico o con suggerimenti. |
| [Metodo di completamento automatico](/rest/api/searchservice/autocomplete) | Un utente digita alcuni caratteri e le query vengono avviate dopo la digitazione di ogni nuovo carattere. La risposta è una stringa completata dall'indice. Se la stringa specificata è valida, l'utente fa clic su Cerca per inviare la query al servizio. |
| [Metodo suggestions](/rest/api/searchservice/suggestions) | Come per la funzionalità di completamento automatico, un utente digita alcuni caratteri e vengono generate query incrementali. La risposta è un elenco a discesa di documenti corrispondenti, in genere rappresentati da alcuni campi univoci o descrittivi. Se una delle selezioni è valida, l'utente fa clic su una di esse e viene restituito il documento corrispondente. |
| [Esplorazione in base a facet](/rest/api/searchservice/search-documents#query-parameters) | Una pagina mostra i collegamenti o le tracce di navigazione selezionabili che limitano l'ambito della ricerca. Una struttura di esplorazione in base a facet è composta dinamicamente in base a una query iniziale. Ad esempio, `search=*` per popolare un albero di navigazione in base a facet costituito da ogni possibile categoria. Una struttura di esplorazione in base a facet viene creata da una risposta di query, ma è anche un meccanismo per esprimere la query successiva. n riferimento all'API REST, `facets` è documentato come parametro di query di un'operazione di ricerca dei documenti, ma può essere usato senza il `search` parametro.|
| [Metodo del filtro](/rest/api/searchservice/search-documents#query-parameters) | I filtri vengono utilizzati con i facet per restringere i risultati. È anche possibile implementare un filtro dietro la pagina, ad esempio per inizializzare la pagina con i campi specifici della lingua. Nei riferimenti all'API REST, `$filter` è documentato come parametro di query di un'operazione di ricerca dei documenti, ma può essere usato senza il `search` parametro.|

## <a name="know-your-field-attributes"></a>Conosce gli attributi dei campi

Se in precedenza sono stati rivisti i [tipi di query e la composizione](search-query-overview.md), è possibile ricordare che i parametri nella richiesta di query dipendono dalla modalità con cui i campi vengono attribuiti in un indice. Ad esempio, per essere utilizzato in una query, in un filtro o in un ordinamento, un campo deve essere *ricercabile*, *filtrabile* e *ordinabile*. Analogamente, nei risultati possono essere visualizzati solo i campi contrassegnati come *recuperabili* . Quando si inizia a specificare i `search` `filter` parametri, e `orderby` nella richiesta, assicurarsi di controllare gli attributi Man mano che si procede per evitare risultati imprevisti.

Nella schermata del portale riportata di seguito nell' [indice di esempio degli Alberghi](search-get-started-portal.md), solo gli ultimi due campi "LastRenovationDate" e "rating" possono essere usati in una `"$orderby"` sola clausola.

![Definizione di indice per l'esempio di Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione di indice per l'esempio di Hotel")

Per una descrizione degli attributi dei campi, vedere [create index (API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conosce i token

Durante l'indicizzazione, il motore di ricerca usa un analizzatore per eseguire analisi del testo sulle stringhe, massimizzando la possibilità di corrispondenza in fase di query. Come minimo, le stringhe sono in minuscolo, ma possono anche essere sottoposte a lemmatizzazione e interrompere la rimozione delle parole. Stringhe più grandi o parole composte vengono in genere interrotte da spazi vuoti, trattini o trattini e indicizzate come token separati. 

Il punto da considerare è che ciò che si ritiene venga contenuto nell'indice e che cosa è in realtà, può essere diverso. Se le query non restituiscono risultati previsti, è possibile ispezionare i token creati dall'analizzatore tramite il [testo di analisi (API REST)](/rest/api/searchservice/test-analyzer). Per altre informazioni sulla suddivisione in token e sull'effetto sulle query, vedere [ricerca a termini parziali e modelli con caratteri speciali](search-query-partial-matching.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è in grado di comprendere meglio il funzionamento delle richieste di query, provare le guide introduttive seguenti per un'esperienza pratica.

+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in REST](search-get-started-rest.md)
+ [Come eseguire query in .NET](search-get-started-dotnet.md)
+ [Come eseguire query in Python](search-get-started-python.md)
+ [Come eseguire query in JavaScript](search-get-started-javascript.md)