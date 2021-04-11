---
title: Suggerimenti per incrementare le prestazioni
titleSuffix: Azure Cognitive Search
description: Informazioni sui suggerimenti e le procedure consigliate per ottimizzare le prestazioni in un servizio di ricerca.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582263"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Suggerimenti per migliorare le prestazioni in Azure ricerca cognitiva

Questo articolo è una raccolta di suggerimenti e procedure consigliate spesso consigliate per migliorare le prestazioni. Conoscere i fattori che hanno maggiori probabilità di influenzare le prestazioni di ricerca può aiutare a evitare inefficienze e a sfruttare al meglio il servizio di ricerca. Alcuni fattori chiave includono:

+ Composizione indice (schema e dimensioni)
+ Tipi di query
+ Capacità del servizio (livello e numero di repliche e partizioni)

## <a name="index-size-and-schema"></a>Dimensioni e schema degli indici

Le query vengono eseguite più velocemente sugli indici più piccoli. Questa è parzialmente una funzione che prevede un minor numero di campi da analizzare, ma è anche dovuta al modo in cui il sistema memorizza nella cache il contenuto per le query future. Dopo la prima query, parte del contenuto rimane in memoria dove viene eseguita una ricerca più efficiente. Poiché le dimensioni degli indici tendono a crescere nel tempo, una procedura consigliata consiste nel rivedere periodicamente la composizione dell'indice, sia dello schema che dei documenti, per cercare le opportunità di riduzione del contenuto. Tuttavia, se l'indice è di dimensioni corrette, l'unica altra calibratura che è possibile apportare consiste nell'aumentare la capacità, ovvero [aggiungendo repliche](search-capacity-planning.md#adjust-capacity) o aggiornando il livello di servizio. La sezione ["Suggerimento: eseguire l'aggiornamento a un livello S2 standard"]] (#tip-upgrade-to-a-standard-S2-Tier) Mostra come valutare la decisione di scalabilità verticale e orizzontale.

La complessità dello schema può influire negativamente sulle prestazioni di indicizzazione e query. Una quantità eccessiva di attribuzione dei campi si basa sulle limitazioni e sui requisiti di elaborazione. I [tipi complessi](search-howto-complex-data-types.md) importano più tempo per indicizzare ed eseguire query. Le sezioni successive esplorano ogni condizione.

### <a name="tip-be-selective-in-field-attribution"></a>Suggerimento: essere selettiva in attribuzione di campi

Un errore comune commesso dagli amministratori e dagli sviluppatori durante la creazione di un indice di ricerca consiste nel selezionare tutte le proprietà disponibili per i campi, anziché solo selezionare solo le proprietà necessarie. Se, ad esempio, non è necessario che sia possibile eseguire la ricerca full-text in un campo, ignorare il campo quando si imposta l'attributo ricercabile.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Attribuzione selettiva" border="true":::

Il supporto per filtri, facet e ordinamento può quadruplicare i requisiti di archiviazione. Se si aggiungono i suggerimenti, i requisiti di archiviazione diventano ancora più approfonditi. Per un'illustrazione dell'effetto degli attributi sull'archiviazione, vedere [attributi e dimensioni degli indici](search-what-is-an-index.md#index-size).

Riepilogate, le ramificazioni di sovraattribuzione includono:

+ Riduzione delle prestazioni di indicizzazione dovuta al lavoro aggiuntivo richiesto per elaborare il contenuto nel campo e quindi archiviarlo nell'indice invertito di ricerca (impostare l'attributo "ricercabile" solo nei campi che contengono contenuto ricercabile).

+ Crea una superficie maggiore che ogni query deve coprire. Tutti i campi contrassegnati come ricercabili vengono analizzati in una ricerca full-text.

+ Aumenta i costi operativi dovuti ad archiviazione aggiuntiva. L'ordinamento e il filtro richiedono spazio aggiuntivo per l'archiviazione di stringhe originali (non analizzate). Evitare l'impostazione filtrabile o ordinabile sui campi che non sono necessari.

+ In molti casi, oltre l'attribuzione limita le funzionalità del campo. Ad esempio, se un campo è facet, filtrabile e ricercabile, è possibile archiviare solo 16 KB di testo all'interno di un campo, mentre un campo ricercabile può contenere un massimo di 16 MB di testo.

> [!NOTE]
> È consigliabile evitare solo l'attribuzione non necessaria. I filtri e i facet sono spesso essenziali per l'esperienza di ricerca e nei casi in cui vengono usati i filtri, spesso è necessario eseguire l'ordinamento in modo che sia possibile ordinare i risultati (i filtri vengono restituiti in un set non ordinato).

### <a name="tip-consider-alternatives-to-complex-types"></a>Suggerimento: prendere in considerazione le alternative ai tipi complessi

I tipi di dati complessi sono utili quando i dati hanno una struttura annidata complicata, ad esempio gli elementi padre-figlio presenti nei documenti JSON. Lo svantaggio dei tipi complessi è costituito dai requisiti di archiviazione aggiuntivi e dalle risorse aggiuntive necessarie per indicizzare il contenuto, rispetto ai tipi di dati non complessi. 

In alcuni casi, è possibile evitare questi compromessi eseguendo il mapping di una struttura di dati complessa a un tipo di campo più semplice, ad esempio una raccolta. In alternativa, è possibile scegliere di rendere flat una gerarchia di campi in singoli campi a livello radice.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="struttura campo bidimensionale" border="true":::

## <a name="types-of-queries"></a>Tipi di query

I tipi di query inviati sono uno dei fattori più importanti per le prestazioni e l'ottimizzazione delle query può migliorare drasticamente le prestazioni. Quando si progettano le query, considerare i punti seguenti:

+ **Numero di campi disponibili per la ricerca.** Ogni campo ricercabile aggiuntivo richiede lavoro aggiuntivo da parte del servizio di ricerca. È possibile limitare i campi in cui viene eseguita la ricerca in fase di query utilizzando il parametro "searchFields". È preferibile specificare solo i campi a cui si è interessati per migliorare le prestazioni.

+ **Quantità di dati restituiti.** Il recupero di una grande quantità di contenuto può rendere le query più lente. Quando si struttura una query, restituire solo i campi necessari per eseguire il rendering della pagina dei risultati e quindi recuperare i campi rimanenti usando l' [API di ricerca](/rest/api/searchservice/lookup-document) una volta che un utente seleziona una corrispondenza.

+ **Uso di ricerche con termini parziali.** Le [ricerche con termini parziali](search-query-partial-matching.md), ad esempio la ricerca di prefisso, la ricerca fuzzy e la ricerca di espressioni regolari, sono più dispendiose dal punto di vista del calcolo rispetto alle tipiche ricerche di parole chiave, perché richiedono analisi complete dell'indice per produrre risultati

+ **Numero di facet.** Per aggiungere facet alle query è necessario utilizzare aggregazioni per ogni query. In generale, aggiungere solo i facet di cui si intende eseguire il rendering nell'app.

+ **Limita i campi di cardinalità elevata.**  Un *campo di cardinalità elevata* fa riferimento a un campo con facet o filtrabile con un numero significativo di valori univoci e, di conseguenza, utilizza risorse significative quando si calcolano i risultati. Ad esempio, l'impostazione di un campo ID prodotto o descrizione come facet e filtrabile viene conteggiata come alta cardinalità poiché la maggior parte dei valori dal documento al documento sono univoci.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Suggerimento: usare le funzioni di ricerca invece di sovraccaricare i criteri di filtro

Poiché una query utilizza criteri di [filtro](search-query-odata-filter.md#filter-size-limitations)sempre più complessi, le prestazioni della query di ricerca diminuiranno. Si consideri l'esempio seguente che illustra l'uso dei filtri per tagliare i risultati in base a un'identità utente:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

In questo caso, le espressioni di filtro vengono usate per verificare se un singolo campo in ogni documento è uguale a uno dei molti valori possibili di un'identità utente. È più probabile che questo modello venga individuato nelle applicazioni che implementano il [taglio di sicurezza](search-security-trimming-for-azure-search.md) (controllando un campo contenente uno o più ID entità rispetto a un elenco di ID entità che rappresentano l'utente che ha emesso la query).

Un modo più efficiente per eseguire filtri che contengono un numero elevato di valori consiste nell'usare la [ `search.in` funzione](search-query-odata-search-in-function.md), come illustrato nell'esempio seguente:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Suggerimento: aggiungere partizioni per le singole query lente

Quando si verifica un rallentamento delle prestazioni delle query in generale, l'aggiunta di più repliche risolve spesso il problema. Ma cosa accade se il problema è una singola query che richiede troppo tempo per essere completata? In questo scenario l'aggiunta di repliche non sarà utile, ma potrebbero essere presenti partizioni aggiuntive. Una partizione suddivide i dati tra le risorse di elaborazione aggiuntive. Due partizioni suddividono i dati a metà, una terza partizione la suddivide in tre e così via. 

Un effetto collaterale positivo dell'aggiunta di partizioni consiste nel fatto che le query più lente a volte vengono eseguite più velocemente a causa del calcolo parallelo. È stata annotata la parallelizzazione su query con selettività bassa, ad esempio query che corrispondono a molti documenti, o facet che forniscono conteggi su un numero elevato di documenti. Poiché è necessario un calcolo significativo per assegnare un punteggio alla pertinenza dei documenti o per conteggiare il numero di documenti, l'aggiunta di partizioni aggiuntive consente di completare più velocemente le query.  

Per aggiungere partizioni, usare [portale di Azure](search-create-service-portal.md), [PowerShell](search-manage-powershell.md), l'interfaccia della riga di comando di [Azure](search-manage-azure-cli.md)o un SDK di gestione.

## <a name="service-capacity"></a>Capacità del servizio

Un servizio viene sovraccaricato quando le query richiedono troppo tempo o quando il servizio inizia a eliminare richieste. In tal caso, è possibile risolvere il problema aggiornando il servizio o aggiungendo capacità.

Anche il livello del servizio di ricerca e il numero di repliche/partizioni hanno un notevole effetto sulle prestazioni. Ogni livello superiore fornisce CPU più veloci e una maggiore quantità di memoria, che hanno un impatto positivo sulle prestazioni.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Suggerimento: eseguire l'aggiornamento a un livello S2 standard

Il livello di ricerca S1 standard è spesso l'inizio dei clienti. Un modello comune per i servizi S1 è che gli indici crescono nel tempo, che richiede un maggior numero di partizioni. Più partizioni portano a tempi di risposta più lenti, quindi vengono aggiunte più repliche per gestire il carico di query. Come si può immaginare, il costo per l'esecuzione di un servizio S1 è stato superato a livelli superiori alla configurazione iniziale.

In questo frangente, una domanda importante da porsi è la possibilità di passare a un livello superiore, anziché aumentare progressivamente il numero di partizioni o repliche del servizio corrente. 

Si consideri la topologia seguente come esempio di un servizio che ha assunto livelli di capacità crescenti:

+ Livello S1 standard
+ Dimensioni indice: 190 GB
+ Conteggio partizioni: 8 (su S1, le dimensioni della partizione sono di 25 GB per partizione)
+ Numero di repliche: 2
+ Totale unità di ricerca: 16 (8 partizioni x 2 repliche)
+ Prezzo di vendita al dettaglio ipotetico: ~ $4.000 USD al mese (si supponga di $250 USD x 16 unità di ricerca)

Si supponga che l'amministratore del servizio stia ancora visualizzando tassi di latenza più elevati e stia valutando l'aggiunta di un'altra replica In questo modo il numero di repliche verrà modificato da 2 a 3 e, di conseguenza, il numero di unità di ricerca verrà modificato in 24 e il prezzo risultante sarà di $6.000 USD al mese.

Tuttavia, se l'amministratore ha scelto di passare a un livello S2 standard, la topologia avrà un aspetto simile al seguente:

+ Livello S2 standard
+ Dimensioni indice: 190 GB
+ Conteggio partizioni: 2 (su S2, le dimensioni della partizione sono 100 GB per partizione)
+ Numero di repliche: 2
+ Totale unità di ricerca: 4 (2 partizioni x 2 repliche)
+ Prezzo finale ipotetico: ~ $4.000 USD/mese (unità di ricerca $1000 USD x 4)

Come illustrato in questo scenario ipotetico, è possibile avere configurazioni su livelli inferiori che comportano costi simili come se si fosse scelto un livello superiore nella prima posizione. Tuttavia, i livelli più elevati sono dotati di archiviazione Premium, che rende più veloce l'indicizzazione. I livelli superiori hanno anche una maggiore potenza di calcolo, oltre a una quantità di memoria aggiuntiva. Per gli stessi costi, è possibile disporre di un'infrastruttura più potente che supporta lo stesso indice.

Un importante vantaggio della memoria aggiunta è che è possibile memorizzare nella cache un numero maggiore di indici, causando una latenza di ricerca inferiore e un numero maggiore di query al secondo. Con questa potenza aggiuntiva, è possibile che l'amministratore non debba neanche aumentare il numero di repliche e potrebbe pagare di meno rispetto al servizio S1.

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli aggiuntivi correlati alle prestazioni del servizio.

+ [Analizzare le prestazioni](search-performance-analysis.md)
+ [Scegliere un livello di servizio](search-sku-tier.md)
+ [Aggiungere capacità (repliche e partizioni)](search-capacity-planning.md#adjust-capacity)
