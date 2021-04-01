---
title: Sinonimi per l'espansione di query su un indice di ricerca
titleSuffix: Azure Cognitive Search
description: Creare una mappa di sinonimi per espandere l'ambito di una query di ricerca in un indice ricerca cognitiva di Azure. L'ambito viene ampliato in modo da includere termini equivalenti forniti in un elenco.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251625"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinonimi in Azure ricerca cognitiva

Con le mappe sinonimi, è possibile associare termini equivalenti, espandendo l'ambito di una query senza che l'utente debba effettivamente fornire il termine. Si supponga, ad esempio, che "Dog", "canino" e "cucciolo" siano sinonimi, una query su "canino" corrisponderà a un documento contenente "Dog".

## <a name="create-synonyms"></a>Creare sinonimi

Una mappa di sinonimi è un asset che può essere creato una volta e usato da molti indici. Il [livello di servizio](search-limits-quotas-capacity.md#synonym-limits) determina il numero di mappe sinonimi che è possibile creare, comprese tra tre mappe sinonimi per i livelli gratuito e Basic, fino a 20 per i livelli standard. 

È possibile creare più mappe sinonimo per lingue diverse, ad esempio versioni in inglese e in francese, oppure lessico se il contenuto include una terminologia tecnica o nascosta. Sebbene sia possibile creare più mappe sinonimo nel servizio di ricerca, un campo può utilizzarne solo uno.

Una mappa di sinonimi è costituita da nome, formato e regole che funzionano come voci di mapping sinonimo. L'unico formato supportato è `solr` e il `solr` formato determina la costruzione di regole.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Per creare una mappa di sinonimi, usare la mappa per la [creazione di sinonimi (API REST)](/rest/api/searchservice/create-synonym-map) o Azure SDK. Per gli sviluppatori C#, è consigliabile iniziare con [aggiungere sinonimi in ricerca cognitiva di Azure con C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definire le regole

Le regole di mapping sono conformi alla specifica del filtro di sinonimi open source di Apache Solr, descritta in questo documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Il `solr` formato supporta due tipi di regole:

+ equivalenza (i termini sono sostituti uguali nella query)

+ mapping espliciti (in cui viene eseguito il mapping dei termini a un termine esplicito prima dell'esecuzione di query)

Ogni regola deve essere delimitata dal carattere di nuova riga ( `\n` ). È possibile definire fino a 5.000 regole per ogni mappa di sinonimi in un servizio gratuito e 20.000 regole per mappa in altri livelli. Ogni regola può avere fino a 20 espansioni (o elementi in una regola). Per ulteriori informazioni, vedere [limiti dei sinonimi](search-limits-quotas-capacity.md#synonym-limits).

Gli analizzatori di query risulteranno in minuscolo eventuali termini maiuscoli o misti, ma se si desidera mantenere i caratteri speciali nella stringa, ad esempio una virgola o un trattino, aggiungere i caratteri di escape appropriati durante la creazione della mappa di sinonimi.

### <a name="equivalency-rules"></a>Regole di equivalenza

Le regole per i termini equivalenti sono delimitate da virgole all'interno della stessa regola. Nel primo esempio, una query su `USA` si espanderà in `USA` o `"United States"` o `"United States of America"` . Si noti che se si desidera trovare la corrispondenza con una frase, la query stessa deve essere una query di frase racchiusa tra virgolette.

Nel caso di equivalenza, una query per `dog` espanderà la query in modo da includere anche `puppy` e `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Mapping esplicito

Le regole per un mapping esplicito sono identificate da una freccia `=>` . Quando specificato, una sequenza di termini di una query di ricerca che corrisponde al lato sinistro di `=>` verrà sostituita con le alternative sul lato destro in fase di query.

Nel caso esplicito, una query per `Washington` `Wash.` o `WA` verrà riscritta come `WA` e il motore di query cercherà solo le corrispondenze nel termine `WA` . Il mapping esplicito si applica solo nella direzione specificata e la query non viene riscritta `WA` `Washington` in in questo caso.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Escape dei caratteri speciali

I sinonimi vengono analizzati durante l'elaborazione delle query. Se è necessario definire sinonimi che contengono virgole o altri caratteri speciali, è possibile eseguire l'escape con una barra rovesciata, come in questo esempio:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Poiché la barra rovesciata è a sua volta un carattere speciale in altri linguaggi come JSON e C#, probabilmente sarà necessario eseguire un doppio escape. Ad esempio, il codice JSON inviato all'API REST per la mappa di sinonimi precedente avrà un aspetto simile al seguente:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Caricare e gestire mappe sinonimo

Come indicato in precedenza, è possibile creare o aggiornare una mappa di sinonimi senza compromettere i carichi di lavoro di query e indicizzazione. Una mappa di sinonimi è un oggetto autonomo, ad esempio indici o origini dati, e finché nessun campo lo utilizza, gli aggiornamenti non comportano l'esecuzione di indicizzazione o di query. Tuttavia, dopo aver aggiunto una mappa di sinonimi a una definizione di campo, se si elimina una mappa di sinonimi, le query che includono i campi in questione avranno esito negativo con un errore 404.

La creazione, l'aggiornamento e l'eliminazione di una mappa di sinonimi è sempre un'operazione di intero documento, vale a dire che non è possibile aggiornare o eliminare parti della mappa di sinonimi in modo incrementale. Per aggiornare anche una singola regola è necessario ricaricare.

## <a name="assign-synonyms-to-fields"></a>Assegnare i sinonimi ai campi

Dopo il caricamento di una mappa di sinonimi, è possibile abilitare i sinonimi nei campi del tipo `Edm.String` o `Collection(Edm.String)` nei campi con `"searchable":true` . Come indicato, una definizione di campo può usare solo una mappa di sinonimi.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Eseguire query su campi equivalenti o mappati

L'aggiunta di sinonimi non impone nuovi requisiti per la creazione delle query. È possibile eseguire query su termini e frasi esattamente come prima dell'aggiunta di sinonimi. L'unica differenza è che se un termine di query è presente nella mappa dei sinonimi, il motore di query espanderà o riscriverà il termine o la frase, a seconda della regola.

## <a name="how-synonyms-are-used-during-query-execution"></a>Modalità di utilizzo di sinonimi durante l'esecuzione di query

I sinonimi sono una tecnica di espansione delle query che integra il contenuto di un indice con termini equivalenti, ma solo per i campi che dispongono di un'assegnazione di sinonimo. Se una query con ambito campo *esclude* un campo abilitato per sinonimi, non verranno visualizzate corrispondenze dalla mappa dei sinonimi.

Per i campi abilitati per sinonimi, i sinonimi sono soggetti alla stessa analisi del testo del campo associato. Se, ad esempio, un campo viene analizzato usando l'analizzatore Lucene standard, i termini del sinonimo saranno anche soggetti all'analizzatore di Lucene standard in fase di query. Se si vuole mantenere la punteggiatura, ad esempio punti o trattini, nel termine del sinonimo, applicare un analizzatore che conserva il contenuto nel campo.

Internamente, la funzionalità sinonimi riscrive la query originale con sinonimi con l'operatore OR. Per questo motivo, l'evidenziazione dei risultati e i profili di punteggio trattano il termine originale e i sinonimi come equivalenti.

I sinonimi si applicano solo alle query di testo in formato libero e non sono supportati per i filtri, i facet, il completamento automatico o i suggerimenti. Il completamento automatico e i suggerimenti sono basati solo sul termine originale; le corrispondenze sinonimo non vengono visualizzate nella risposta.

Le espansioni dei sinonimi non si applicano ai termini di ricerca con caratteri jolly; i termini con prefisso, fuzzy e regex non vengono espansi.

Se è necessario eseguire un'unica query che comprenda espansioni sinonimiche, caratteri jolly, espressioni regolari o ricerche fuzzy, è possibile combinare le relative istruzioni con la sintassi OR. Ad esempio, per combinare sinonimi e caratteri jolly in una sintassi di query semplice, il termine è `<query> | <query>*`.

Se si ha un indice esistente in un ambiente di sviluppo (non di produzione), provare con un piccolo dizionario per vedere come l'aggiunta di sinonimi cambia l'esperienza di ricerca, compreso l'impatto sui profili di punteggio, l'evidenziazione dei risultati e i suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una mappa di sinonimi (API REST)](/rest/api/searchservice/create-synonym-map)