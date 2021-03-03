---
title: Configurare l'algoritmo di somiglianza di rango
titleSuffix: Azure Cognitive Search
description: Come impostare l'algoritmo di somiglianza per provare il nuovo algoritmo di somiglianza per la classificazione
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677794"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Configurare gli algoritmi di classificazione in Azure ricerca cognitiva

Azure ricerca cognitiva supporta due algoritmi di classificazione di somiglianza:

+ Algoritmo di *somiglianza classico* , usato da tutti i servizi di ricerca fino al 15 luglio 2020.
+ Implementazione dell'algoritmo *BM25 di Okapi* , usata in tutti i servizi di ricerca creati dopo il 15 luglio.

BM25 ranking è il nuovo valore predefinito perché tende a produrre classificazioni di ricerca che si allineano meglio con le aspettative degli utenti. Abilita anche le opzioni di configurazione per l'ottimizzazione dei risultati in base a fattori quali le dimensioni del documento. Per i nuovi servizi creati dopo il 15 luglio 2020, BM25 viene usato automaticamente ed è l'unico algoritmo di somiglianza. Se si tenta di impostare la somiglianza su ClassicSimilarity in un nuovo servizio, verrà restituito un errore HTTP 400 poiché tale algoritmo non è supportato dal servizio.

Per i servizi meno recenti creati prima del 15 luglio 2020, la somiglianza classica rimane l'algoritmo predefinito. I servizi meno recenti possono impostare le proprietà in un indice di ricerca per richiamare BM25, come illustrato di seguito. Se si passa dal modello classico a BM25, è possibile che si verifichino alcune differenze nel modo in cui vengono ordinati i risultati della ricerca.

> [!NOTE]
> La ricerca semantica è un algoritmo di riclassificazione semantico aggiuntivo che restringe ulteriormente il gap tra le aspettative e i risultati. Diversamente dagli altri algoritmi, si tratta di una funzionalità di componente aggiuntivo che consente di scorrere un set di risultati esistente. Per utilizzare l'algoritmo di ricerca semantica di anteprima, è necessario creare un nuovo servizio ed è necessario specificare un [tipo di query semantico](semantic-how-to-query-request.md). Per altre informazioni, vedere [Cenni preliminari sulla ricerca semantica](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Creare un indice di ricerca per il Punteggio di BM25

Se si esegue un servizio di ricerca creato prima del 15 luglio 2020, è possibile impostare la proprietà di somiglianza su BM25Similarity o ClassicSimilarity nella definizione dell'indice. Se la proprietà di somiglianza viene omessa o impostata su null, l'indice utilizzerà l'algoritmo classico.

L'algoritmo di somiglianza può essere impostato solo in fase di creazione dell'indice. Tuttavia, una volta creato un indice con BM25, è possibile aggiornare l'indice esistente per impostare o modificare i parametri di BM25.

| Libreria client | Proprietà somiglianza |
|----------------|---------------------|
| .NET  | [SearchIndex. somiglianza](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex. sesimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. somiglianza](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [Proprietà di somiglianza in SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Esempio REST

È anche possibile usare l' [API REST](/rest/api/searchservice/create-index), come illustrato nell'esempio seguente:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="bm25-similarity-parameters"></a>Parametri di somiglianza di BM25

La somiglianza di BM25 aggiunge due parametri personalizzabili dall'utente per controllare il Punteggio di pertinenza calcolato. È possibile impostare i parametri BM25 durante la creazione dell'indice o come aggiornamento dell'indice se l'algoritmo BM25 è stato specificato durante la creazione dell'indice.

| Proprietà | Type | Descrizione |
|----------|------|-------------|
| k1 | d'acquisto | Controlla la funzione di ridimensionamento tra la frequenza dei termini di ogni termine corrispondente e il Punteggio di pertinenza finale di una coppia di query documento. I valori sono in genere compresi tra 0,0 e 3,0 e 1,2 come valore predefinito. </br></br>Il valore 0,0 rappresenta un "modello binario", in cui il contributo di un singolo termine corrispondente è lo stesso per tutti i documenti corrispondenti, indipendentemente dal numero di volte in cui il termine viene visualizzato nel testo, mentre un valore K1 maggiore consente al Punteggio di continuare ad aumentare quando nel documento sono presenti più istanze dello stesso termine. </br></br>L'uso di un valore K1 superiore può essere importante nei casi in cui ci si aspetta che più termini facciano parte di una query di ricerca. In questi casi, è consigliabile preferire i documenti che corrispondono a molti dei diversi termini di query in cui viene eseguita la ricerca sui documenti che corrispondono solo a una singola, più volte. Ad esempio, quando si esegue una query sull'indice per i documenti che contengono i termini "Apollo spaziale", potrebbe essere necessario ridurre il Punteggio di un articolo sulla mitologia greca che contiene il termine "Apollo" alcune dozzine di volte, senza menzionare il "volo", rispetto a un altro articolo che indica in modo esplicito sia "Apollo" che "volo" solo alcune volte. |
| b | d'acquisto | Controlla il modo in cui la lunghezza di un documento influiscono sul punteggio di pertinenza. I valori sono compresi tra 0 e 1, con 0,75 come valore predefinito. </br></br>Il valore 0,0 indica che la lunghezza del documento non influenzerà il punteggio, mentre il valore 1,0 indica che l'impatto della frequenza dei termini sul punteggio di pertinenza verrà normalizzato in base alla lunghezza del documento. </br></br>La normalizzazione della frequenza dei termini in base alla lunghezza del documento è utile nei casi in cui si vuole penalizzare i documenti più lunghi. In alcuni casi, è più probabile che documenti più lunghi, ad esempio un romanzo completo, contengano molti termini irrilevanti rispetto a documenti molto più brevi. |

### <a name="setting-k1-and-b-parameters"></a>Impostazione dei parametri K1 e b

Per impostare o modificare i valori b o K1, aggiungerli all'oggetto somiglianza BM25. Se si imposta o si modificano questi valori in un indice esistente, l'indice verrà portato offline per almeno alcuni secondi, causando un errore di indicizzazione e richieste di query attive. Di conseguenza, è necessario impostare il parametro "allowIndexDowntime = true" della richiesta di aggiornamento:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Vedi anche  

+ [Informazioni di riferimento sull'API REST](/rest/api/searchservice/)
+ [Aggiungere profili di Punteggio all'indice](index-add-scoring-profiles.md)
+ [Create index API](/rest/api/searchservice/create-index)
+ [.NET SDK di Ricerca cognitiva di Azure](/dotnet/api/overview/azure/search)