---
title: Configurare l'algoritmo di somiglianza
titleSuffix: Azure Cognitive Search
description: Informazioni su come abilitare BM25 nei servizi di ricerca meno recenti e su come modificare i parametri di BM25 per adattarli meglio al contenuto degli indici.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232835"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Configurare l'algoritmo di classificazione della somiglianza in Azure ricerca cognitiva

Azure ricerca cognitiva supporta due algoritmi di classificazione di somiglianza:

+ Algoritmo di *somiglianza classico* , usato da tutti i servizi di ricerca fino al 15 luglio 2020.
+ Implementazione dell'algoritmo *BM25 di Okapi* , usata in tutti i servizi di ricerca creati dopo il 15 luglio.

BM25 ranking è il nuovo valore predefinito perché tende a produrre classificazioni di ricerca che si allineano meglio con le aspettative degli utenti. Viene fornita con [parametri](#set-bm25-parameters) per l'ottimizzazione dei risultati in base a fattori quali le dimensioni del documento. 

Per i nuovi servizi creati dopo il 15 luglio 2020, BM25 viene usato automaticamente ed è l'unico algoritmo di somiglianza. Se si tenta di impostare la somiglianza su ClassicSimilarity in un nuovo servizio, verrà restituito un errore HTTP 400 poiché tale algoritmo non è supportato dal servizio.

Per i servizi meno recenti creati prima del 15 luglio 2020, la somiglianza classica rimane l'algoritmo predefinito. I servizi meno recenti possono eseguire l'aggiornamento a BM25 in base all'indice, come illustrato di seguito. Se si passa dal modello classico a BM25, è possibile che si verifichino alcune differenze nel modo in cui vengono ordinati i risultati della ricerca.

> [!NOTE]
> La classificazione semantica, attualmente in anteprima per i servizi standard in aree selezionate, rappresenta un ulteriore passo avanti nella produzione di risultati più rilevanti. Diversamente dagli altri algoritmi, si tratta di una funzionalità di componente aggiuntivo che consente di scorrere un set di risultati esistente. Per altre informazioni, vedere [Panoramica della ricerca semantica](semantic-search-overview.md) e [classificazione semantica](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>Abilitare il Punteggio di BM25 nei servizi meno recenti

Se si esegue un servizio di ricerca creato prima del 15 luglio 2020, è possibile abilitare BM25 impostando una proprietà di somiglianza nei nuovi indici. La proprietà viene esposta solo nei nuovi indici, pertanto se si desidera BM25 su un indice esistente, è necessario eliminare e [ricompilare l'indice](search-howto-reindex.md) con una nuova proprietà di somiglianza impostata su "Microsoft. Azure. search. BM25Similarity".

Quando esiste un indice con una proprietà di somiglianza, è possibile passare da BM25Similarity o ClassicSimilarity. 

I collegamenti seguenti descrivono la proprietà di somiglianza negli SDK di Azure. 

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

## <a name="set-bm25-parameters"></a>Imposta parametri BM25

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