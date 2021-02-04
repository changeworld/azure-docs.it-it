---
title: Creare un indice
titleSuffix: Azure Cognitive Search
description: Introduce concetti e strumenti di indicizzazione in ricerca cognitiva di Azure, incluse le definizioni dello schema e la struttura dei dati fisici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d0cc7630a3bea67a99c3cb65d2015e934e8ac2da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539095"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Creazione di indici di ricerca in Azure ricerca cognitiva

Ricerca cognitiva archivia contenuto ricercabile usato per le query full-text e filtrate in un *indice di ricerca*. Un indice viene definito da uno schema e salvato nel servizio, con l'importazione dei dati successiva come secondo passaggio. 

Gli indici contengono *documenti di ricerca*. A livello concettuale, un documento è un singola unità di dati ricercabili nell'indice. Un rivenditore potrebbe avere un documento per ogni prodotto, un'organizzazione di notizie potrebbe avere un documento per ogni articolo e così via. Mapping di questi concetti a più familiari equivalenti di database: un *indice di ricerca* equivale a una *tabella* e i *documenti* sono approssimativamente equivalenti alle *righe* di una tabella.

## <a name="whats-an-index-schema"></a>Che cos'è uno schema di indice?

La struttura fisica di un indice è determinata dallo schema. La raccolta ' Fields ' è in genere la parte più grande di un indice, in cui ogni campo è denominato, assegna un [tipo di dati](/rest/api/searchservice/Supported-data-types)e attribuito con comportamenti consentiti che ne determinano la modalità di utilizzo.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Gli altri elementi sono compressi per brevità, ma i collegamenti seguenti possono fornire i dettagli: [suggerimenti](index-add-suggesters.md), [profili di Punteggio](index-add-scoring-profiles.md), [analizzatori](search-analyzers.md) usati per elaborare le stringhe in token in base alle regole linguistiche o ad altre caratteristiche supportate dall'analizzatore e le impostazioni di [Scripting remoto tra le origini (CORS)](#corsoptions) .

## <a name="choose-a-client"></a>Scegliere un client

Sono disponibili diverse metodologie per la creazione di un indice di ricerca. È consigliabile usare le API portale di Azure o REST per i test iniziali di sviluppo e di prova.

Durante lo sviluppo, pianificare le frequenti ricompilazioni. Poiché le strutture fisiche vengono create nel servizio, è necessario [eliminare e ricreare gli indici](search-howto-reindex.md) per la maggior parte delle modifiche apportate a una definizione di campo esistente. È possibile prendere in considerazione l'uso di un subset di dati per velocizzare le ricompilazioni.

### <a name="permissions"></a>Autorizzazioni

Tutte le operazioni correlate a un indice di ricerca, incluse le richieste GET con la relativa definizione, richiedono una [chiave API di amministrazione](search-security-api-keys.md) per la richiesta.

### <a name="limits"></a>Limiti

Tutti i [livelli di servizio limitano](search-limits-quotas-capacity.md#index-limits) il numero di oggetti che è possibile creare. Se si sta sperimentando il livello gratuito, è possibile avere solo 3 indici in un determinato momento.

### <a name="use-azure-portal-to-create-a-search-index"></a>Usare portale di Azure per creare un indice di ricerca

Il portale offre due opzioni per la creazione di un indice di ricerca: [**importazione guidata dati**](search-import-data-portal.md) e aggiunta di un **Indice** che fornisce campi per la specifica di uno schema di indice. La procedura guidata consente di creare operazioni aggiuntive creando anche un indicizzatore, un'origine dati e il caricamento dei dati. Se si tratta di una quantità di dati superiore a quella desiderata, è sufficiente utilizzare **Add index** o un altro approccio.

Lo screenshot seguente mostra dove è possibile trovare **Aggiungi indice** nel portale. L' **importazione dei dati** è la porta immediatamente successiva.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Comando Aggiungi indice" border="true":::

> [!Tip]
> La progettazione di indici tramite il portale impone requisiti e regole dello schema per tipi di dati specifici, ad esempio la disattivazione delle funzionalità di ricerca full-text in campi numerici. Quando si dispone di un indice praticabile, è possibile copiare il codice JSON dal portale e aggiungerlo alla soluzione.

### <a name="use-a-rest-client"></a>Usare un client REST

Sia l'utente che la Visual Studio Code (con estensione per Azure ricerca cognitiva) possono fungere da client dell'indice di ricerca. Utilizzando uno degli strumenti, è possibile connettersi al servizio di ricerca e inviare richieste [create index (REST)](/rest/api/searchservice/create-index) . Sono disponibili numerose esercitazioni ed esempi che illustrano i client REST per la creazione di oggetti. 

Per informazioni su ogni client, iniziare con uno di questi articoli:

+ [Creare un indice di ricerca con REST e postazione](search-get-started-rest.md)
+ [Introduzione a Visual Studio Code e a Ricerca cognitiva di Azure](search-get-started-vs-code.md)

Per informazioni sulla formulazione di richieste di indice, vedere [operazioni sugli indici (REST)](/rest/api/searchservice/index-operations) .

### <a name="use-an-sdk"></a>Usare un SDK

Per ricerca cognitiva, gli SDK di Azure implementano le funzionalità disponibili a livello generale. Di conseguenza, è possibile usare uno qualsiasi degli SDK per creare un indice di ricerca. Tutti forniscono un **SearchIndexClient** che dispone di metodi per la creazione e l'aggiornamento degli indici.

| Azure SDK | Client | Esempio |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-DotNet-Samples/QuickStart/V11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indici](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Definire i campi

Un documento di ricerca viene definito dalla `fields` raccolta. Sono necessari campi per query e chiavi. Probabilmente saranno necessari anche campi per supportare filtri, facet e ordinamenti. Potrebbero inoltre essere necessari campi per i dati che non vengono mai rilevati da un utente. ad esempio, è possibile che si desiderino campi per i margini di profitto o promozioni di marketing che è possibile utilizzare per modificare la classificazione di

Un campo di tipo EDM. String deve essere designato come chiave del documento. Viene usato per identificare in modo univoco ogni documento di ricerca. È possibile recuperare un documento in base alla relativa chiave per popolare una pagina di dettagli.  

Se i dati in ingresso sono di natura gerarchica, assegnare il tipo di dati del [tipo complesso](search-howto-complex-data-types.md) per rappresentare le strutture nidificate. Il set di dati di esempio predefinito, Hotels, illustra i tipi complessi usando un indirizzo (contiene più sottocampi) che presenta una relazione uno-a-uno con ogni hotel e una raccolta complessa di chat, in cui più stanze sono associate a ogni hotel. 

Assegnare tutti gli analizzatori ai campi stringa prima della creazione dell'indice. Eseguire la stessa operazione per i suggerimenti se si vuole abilitare il completamento automatico in campi specifici.

<a name="index-attributes"></a>

### <a name="attributes"></a>Attributi

Gli attributi del campo determinano le modalità in cui un campo viene usato, ad esempio se viene usato nella ricerca full-text, nella navigazione con facet, nelle operazioni di ordinamento e così via. 

I campi stringa sono spesso contrassegnati come "ricercabili" e "recuperabili". I campi utilizzati per limitare i risultati della ricerca includono "ordinabile", "filtrabile" e "facet".

|Attributo|Descrizione|  
|---------------|-----------------|  
|ricercabile |Ricercabile full-text, soggetto ad analisi lessicali, ad esempio alla scomposizione delle parole durante l'indicizzazione. Se si imposta un campo ricercabile su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". Per informazioni vedere [Funzionamento della ricerca full-text](search-lucene-query-architecture.md).|  
|filtrabili |A cui si fa riferimento nelle query $filter. I campi filtrabili di tipo `Edm.String` o `Collection(Edm.String)` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. |  
|ordinabile |Per impostazione predefinita il sistema ordina i risultati in base al punteggio, ma è possibile configurare l'ordine in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere "ordinabili". |  
|con facet |In genere usato in una presentazione dei risultati della ricerca che include un numero di passaggi per categoria, ad esempio, gli hotel in una specifica città. Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. I campi di tipo `Edm.String` filtrabile, "ordinabile" o "facet" possono avere una lunghezza massima di 32 KB. Per altri dettagli, vedere [Create Index (REST API)](/rest/api/searchservice/create-index)(Creare un indice: API REST).|  
|chiave |Identificatore univoco per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come campo chiave e questo deve essere di tipo `Edm.String`.|  
|recuperabile |Specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio *margine di profitto*, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` for `key` .|  

Sebbene sia possibile aggiungere nuovi campi in qualsiasi momento, le definizioni del campo esistente vengono bloccate per la durata dell'indice. Per questo motivo, gli sviluppatori in genere usano il portale per la creazione di indici semplici, idee di test o usano le pagine del portale per cercare un'impostazione. L'iterazione frequente su una progettazione degli indici è più efficiente se si segue un approccio basato sul codice in modo che sia possibile ricompilare l'indice con facilità.

> [!NOTE]
> Le API usate per compilare un indice hanno comportamenti predefiniti variabili. Per le [API REST](/rest/api/searchservice/Create-Index), la maggior parte degli attributi è abilitata per impostazione predefinita (ad esempio, "Searchable" e "Retrievable" sono true per i campi di stringa) ed è spesso necessario impostarli solo se si desidera disattivarli. Per .NET SDK, il valore opposto è true. Per le proprietà non impostate in modo esplicito, per impostazione predefinita viene disabilitato il comportamento di ricerca corrispondente, a meno che non venga abilitato in modo specifico.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attributi e dimensioni degli indici (implicazioni dell'archiviazione)

Le dimensioni di un indice sono determinate dalla dimensione dei documenti caricati, oltre alla configurazione dell'indice, ad esempio se si includono i suggerimenti e come si impostano gli attributi nei singoli campi. 

Lo screenshot seguente illustra i modelli di archiviazione dell'indice derivanti da diverse combinazioni di attributi. L'indice è basato sull' **indice di esempio Real Immobiliare**, che è possibile creare facilmente tramite la procedura guidata Importa dati. Anche se non vengono visualizzati gli schemi dell'indice, è possibile dedurre gli attributi in base al nome dell'indice. Ad esempio, è stato selezionato l'attributo "ricercabile" nell'indice di *ricerca* per *indicizzazione* e non è possibile selezionare l'attributo "recuperabile" con l'attributo "recuperabile" e così via.

![Dimensioni dell'indice basate sulla selezione degli attributi](./media/search-what-is-an-index/realestate-index-size.png "Dimensioni dell'indice basate sulla selezione degli attributi")

Sebbene queste varianti di indice siano artificiali, è possibile farvi riferimento per una considerazione generale sul modo in cui gli attributi influiscono sull'archiviazione. L'impostazione "recuperabile" aumenta le dimensioni dell'indice? No. L'aggiunta di campi a un **Suggerimento** aumenta le dimensioni dell'indice? Sì. 

La creazione di un campo filtrabile o ordinabile aggiunge anche il consumo di spazio di archiviazione perché i campi filtrati e ordinati non vengono suddivisi in token in modo che le sequenze di caratteri possano corrispondere alla lettera.

Inoltre, la tabella precedente non riflette l'effetto degli [analizzatori](search-analyzers.md). Se si usa il tokenizer edgeNgram per archiviare sequenze di caratteri Verbatim (a, AB, ABC, ABCD), le dimensioni dell'indice saranno maggiori rispetto a quando si usa un analizzatore standard.

> [!Note]
> L'architettura di archiviazione è considerata un dettaglio di implementazione di Azure ricerca cognitiva e può cambiare senza preavviso. Non è garantito che il comportamento attuale verrà mantenuto in futuro.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Informazioni su `corsOptions`

Gli schemi di indice includono una sezione per l'impostazione di `corsOptions` . JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo **corsOptions**. Per motivi di sicurezza, solo le API di query supportano CORS. 

Per CORS è possibile impostare le opzioni seguenti:

+ **allowedOrigins** (obbligatorio): si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://<fully-qualified-domain-name>:<port>` anche se `<port>` spesso viene omessa. Vedere [Utilizzare la condivisione di risorse tra origini (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) per altre informazioni.

  Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice **allowedOrigins**. *Non si consiglia questa pratica per i servizi di ricerca della produzione* ma spesso è utile per lo sviluppo e il debug.

+ **maxAgeInSeconds** (facoltativo): i browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

## <a name="next-steps"></a>Passaggi successivi

È possibile acquisire dimestichezza con la creazione di un indice usando praticamente qualsiasi esempio o procedura dettagliata per ricerca cognitiva. Per i principianti, è possibile scegliere una qualsiasi delle guide introduttive dal sommario.

È tuttavia necessario acquisire familiarità con le metodologie per il caricamento di un indice con i dati. La definizione dell'indice e le strategie di importazione dei dati sono definite in tandem. Negli articoli seguenti vengono fornite ulteriori informazioni sul caricamento di un indice.

+ [Panoramica dell'importazione dei dati](search-what-is-data-import.md)

+ [Aggiungere, aggiornare o eliminare documenti (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 