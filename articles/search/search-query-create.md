---
title: Creare una query di base
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare una richiesta di query in ricerca cognitiva, gli strumenti e le API da usare per il test e il codice e il modo in cui le decisioni relative alle query iniziano con la progettazione degli indici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: ad572905d9864083466049fd602e24d9f3632ea3
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387429"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Creare una query in Azure ricerca cognitiva

Vengono fornite informazioni sugli strumenti e sulle API per la compilazione di una query, sui metodi utilizzati per creare una query e sul modo in cui la struttura e il contenuto dell'indice possono influito sui risultati della query. Per un'introduzione all'aspetto di una richiesta di query, iniziare con i [tipi di query e le composizioni](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Scegliere gli strumenti e le API

È possibile usare uno qualsiasi degli strumenti e delle API seguenti per creare query per carichi di lavoro di test o di produzione.

| Metodologia | Descrizione |
|-------------|-------------|
| Portale| [Esplora ricerche (portale)](search-explorer.md) è un'interfaccia di query nel portale di Azure che può essere usata per eseguire query sugli indici nel servizio di ricerca sottostante. Il portale esegue le chiamate API REST dietro le quinte. È possibile selezionare qualsiasi indice e qualsiasi versione dell'API REST supportata, incluse le versioni di anteprima. Una stringa di query può essere con sintassi semplice e completa e può includere espressioni di filtro, facet, istruzioni SELECT e searchField e searchMode. Quando si apre un indice nel portale, è possibile usare Esplora ricerche insieme alla definizione JSON dell'indice nelle schede affiancate per semplificare l'accesso agli attributi dei campi. È possibile controllare quali campi sono ricercabili, ordinabili, filtrabili e con facet durante il test delle query. Consigliato per le prime indagini, i test e la convalida. <br/>[Altre informazioni.](search-explorer.md) |
| Strumenti di test Web| Il [post o il Visual Studio Code](search-get-started-rest.md) sono scelte sicure per la formulazione di una richiesta di [ricerca dei documenti](/rest/api/searchservice/search-documents) in REST. L'API REST supporta tutte le operazioni a livello di codice in Azure ricerca cognitiva e quando si usa uno strumento come l'utente o la Visual Studio Code, è possibile inviare le richieste in modo interattivo per comprenderne il funzionamento prima di investire nel codice. Uno strumento di test Web è una scelta ottimale se non si dispone dei diritti di collaboratore o di amministratore nella portale di Azure. Fino a quando si dispone di un URL di ricerca e una chiave API di query, è possibile utilizzare gli strumenti per eseguire query su un indice esistente. |
| Azure SDK | Quando si è pronti a scrivere il codice, è possibile usare le librerie client di Azure.Search.Document negli SDK di Azure per .NET, Python, JavaScript o Java. Ogni SDK si trova nella propria pianificazione di rilascio, ma è possibile creare ed eseguire query sugli indici in tutti gli SDK. <br/><br/>È possibile usare [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) per eseguire query su un indice di ricerca in C#.  [Altre informazioni.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) può essere usato per eseguire query in un indice di ricerca in Python. [Altre informazioni.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) può essere usato per eseguire query in un indice di ricerca in JavaScript. [Altre informazioni.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Configurare un client di ricerca

Un client di ricerca esegue l'autenticazione al servizio di ricerca, invia le richieste e gestisce le risposte. Indipendentemente dallo strumento o dall'API usata, un client di ricerca deve avere gli elementi seguenti:

| Proprietà | Descrizione |
|------------|-------------|
| Endpoint | Un servizio di ricerca è indirizzabile all'URL nel formato seguente: `https://[service-name].search.windows.net` . |
| Chiave di accesso API (amministrazione o query) | Autentica la richiesta al servizio di ricerca. |
| Nome dell'indice | Le query vengono sempre indirizzate alla raccolta Documents di un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query. |
| Versione dell'API | Le chiamate REST richiedono in modo esplicito nella `api-version` richiesta. Al contrario, le librerie client in Azure SDK vengono sottoposizioni a una versione specifica dell'API REST. Per gli SDK, `api-version` è implicito. |

### <a name="in-the-portal"></a>Nel portale

Esplora ricerche e altri strumenti del portale hanno una connessione client incorporata al servizio, con indici di accesso diretto e altri oggetti dalle pagine del portale. L'accesso a strumenti, procedure guidate e oggetti richiede l'appartenenza al ruolo di collaboratore o superiore nel servizio. 

### <a name="using-rest"></a>Uso di REST

Per le chiamate REST, è possibile usare il [post o strumenti simili](search-get-started-rest.md) come il client per specificare una richiesta di [ricerca di documenti](/rest/api/searchservice/search-documents) . Ogni richiesta è autonoma, pertanto è necessario fornire l'endpoint, il nome dell'indice e la versione dell'API a ogni richiesta. Altre proprietà, Content-Type e chiave API, vengono passate nell'intestazione della richiesta. 

È possibile utilizzare POST o GET per eseguire una query su un indice. Il POST, con i parametri specificati nel corpo della richiesta, è più semplice da usare. Se si utilizza POST, assicurarsi di includere `docs/search` nell'URL:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Uso di Azure SDK

Se si usa Azure SDK, il client verrà creato nel codice. Tutti gli SDK forniscono client di ricerca che possono rendere permanente lo stato, consentendo il riutilizzo della connessione. Per le operazioni di query, è necessario creare un'istanza di **`SearchClient`** e assegnare valori per le proprietà seguenti: endpoint, Key, index. È quindi possibile chiamare il metodo **`Search method`** per passare la stringa di query. 

| Linguaggio | Client | Esempio |
|----------|--------|---------|
| C# e .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Inviare la prima query di ricerca in C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Inviare la prima query di ricerca in Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Inviare la prima query di ricerca in Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Invia la prima query di ricerca in JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Se la query è una ricerca full-text, verrà utilizzato un parser per elaborare il contenuto del parametro di ricerca. Azure ricerca cognitiva offre due parser di query. Il parser semplice riconosce la [sintassi di query semplice](query-simple-syntax.md). Questo parser è stato selezionato come valore predefinito per la velocità e l'efficacia nelle query di testo in formato libero. La sintassi supporta gli operatori di ricerca comuni (AND, OR, NOT) per le ricerche di termini e frasi e la ricerca di prefisso ( `*` ) (come in "Sea *" per Seattle e Seaside). Una raccomandazione generale è provare prima il parser semplice e quindi passare al parser completo se i requisiti dell'applicazione richiedono query più potenti.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), abilitata quando si aggiunge `queryType=full` alla richiesta, è basata sul [parser Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

La sintassi completa è un'estensione della sintassi semplice, con più operatori che consentono di creare query avanzate, ad esempio ricerca fuzzy, ricerca con caratteri jolly, ricerca di prossimità ed espressioni regolari. Gli esempi seguenti illustrano il punto: la stessa query, ma con **`queryType`** impostazioni diverse che producono risultati diversi. Nella prima query semplice, `^3` dopo `historic` viene considerato come parte del termine di ricerca. Il risultato in primo piano per questa query è "Marquis Plaza & Suites", che ha l' *oceano* nella sua descrizione.

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

Se in precedenza sono state esaminate le [nozioni di base di una richiesta di query](search-query-overview.md), è possibile ricordare che i parametri nella richiesta di query dipendono dalla modalità con cui i campi vengono attribuiti in un indice. Ad esempio, per essere utilizzato in una query, in un filtro o in un ordinamento, un campo deve essere *ricercabile*, *filtrabile* e *ordinabile*. Analogamente, nei risultati possono essere visualizzati solo i campi contrassegnati come *recuperabili* . Quando si inizia a specificare i `search` `filter` parametri, e `orderby` nella richiesta, assicurarsi di controllare gli attributi Man mano che si procede per evitare risultati imprevisti.

Nella schermata del portale riportata di seguito nell' [indice di esempio degli Alberghi](search-get-started-portal.md), solo gli ultimi due campi "LastRenovationDate" e "rating" possono essere usati in una `"$orderby"` sola clausola.

![Definizione di indice per l'esempio di Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione di indice per l'esempio di Hotel")

Per una descrizione degli attributi dei campi, vedere [create index (API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conosce i token

Durante l'indicizzazione, il motore di query utilizza un analizzatore per eseguire analisi del testo sulle stringhe, massimizzando la possibilità di corrispondenza in fase di query. Come minimo, le stringhe sono in minuscolo, ma possono anche essere sottoposte a lemmatizzazione e interrompere la rimozione delle parole. Stringhe più grandi o parole composte vengono in genere interrotte da spazi vuoti, trattini o trattini e indicizzate come token separati. 

Il punto da considerare è che ciò che si ritiene venga contenuto nell'indice e che cosa è in realtà, può essere diverso. Se le query non restituiscono risultati previsti, è possibile ispezionare i token creati dall'analizzatore tramite il [testo di analisi (API REST)](/rest/api/searchservice/test-analyzer). Per altre informazioni sulla suddivisione in token e sull'effetto sulle query, vedere [ricerca a termini parziali e modelli con caratteri speciali](search-query-partial-matching.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con le modalità di costruzione di una richiesta di query, provare le guide introduttive seguenti per un'esperienza pratica.

+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in REST](search-get-started-rest.md)
+ [Come eseguire query in .NET](search-get-started-dotnet.md)
+ [Come eseguire query in Python](search-get-started-python.md)
+ [Come eseguire query in JavaScript](search-get-started-javascript.md)