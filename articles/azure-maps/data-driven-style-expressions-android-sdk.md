---
title: Espressioni di stile basate sui dati nelle mappe Android | Mappe Microsoft Azure
description: Informazioni sulle espressioni di stile basate sui dati. Per modificare gli stili nelle mappe, vedere come usare queste espressioni nelle mappe di Azure Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 61d7a295d86fd7da74dee03cd35c79feea0218ed
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681580"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Espressioni di stile basate sui dati (Android SDK)

Le espressioni consentono di applicare la logica di business alle opzioni di stile che osservano le proprietà definite in ogni forma di un'origine dati. Le espressioni possono filtrare i dati in un'origine dati o in un livello. Le espressioni possono essere costituite da logica condizionale, ad esempio If-Statements. E possono essere usati per modificare i dati usando gli operatori di stringa, gli operatori logici e gli operatori matematici.

Gli stili basati sui dati consentono di ridurre la quantità di codice necessario per implementare la logica di business per lo stile. Quando vengono utilizzati con i livelli, le espressioni vengono valutate in fase di rendering in un thread separato. Questa funzionalità garantisce un miglioramento delle prestazioni rispetto alla valutazione della logica di business nel thread dell'interfaccia utente.

Azure Maps Android SDK supporta quasi tutte le stesse espressioni di stile di Azure Maps Web SDK, pertanto tutti gli stessi concetti illustrati nelle [espressioni di stile basate sui dati (Web SDK)](data-driven-style-expressions-web-sdk.md) possono essere trasferiti in un'app Android. Tutte le espressioni di stile nell'Android SDK mappe di Azure sono disponibili nello `com.microsoft.azure.maps.mapcontrol.options.Expression` spazio dei nomi. Esistono molti tipi diversi di espressioni di stile.

| Tipo di espressioni | Descrizione |
|---------------------|-------------|
| [Espressioni booleane](#boolean-expressions) | Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani. |
| [Espressioni colore](#color-expressions) | Le espressioni colore semplificano la creazione e la modifica dei valori dei colori. |
| [Espressioni condizionali](#conditional-expressions) | Le espressioni condizionali forniscono operazioni logiche simili a If-Statements. |
| [Espressioni di dati](#data-expressions) | Consente di accedere ai dati delle proprietà in una funzionalità. |
| [Espressioni interpolate e Step](#interpolate-and-step-expressions) | Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. |
| [Espressioni basate su JSON](#json-based-expressions) | Semplifica il riutilizzo dello stile di espressioni basate su JSON non elaborate create per Web SDK con la Android SDK. |  
| [Espressioni specifiche del livello](#layer-specific-expressions) | Espressioni speciali applicabili solo a un singolo livello. |
| [Espressioni matematiche](#math-expressions) | Fornisce gli operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni. |
| [Espressioni operatore stringa](#string-operator-expressions) | Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. |
| [Espressioni di tipo](#type-expressions) | Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani. |
| [Espressioni di associazione variabili](#variable-binding-expressions) | Le espressioni di associazione variabili consentono di archiviare i risultati di un calcolo in una variabile e di farvi riferimento altrove in un'espressione più volte senza dover ricalcolare il valore archiviato. |
| [Espressione zoom](#zoom-expression) | Recupera il livello di zoom corrente della mappa in fase di rendering. |

Tutti gli esempi in questa sezione del documento utilizzano la seguente funzionalità per illustrare i diversi modi in cui è possibile utilizzare queste espressioni.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

Il codice seguente illustra come creare manualmente questa funzionalità GeoJSON in un'app.

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

Il codice seguente illustra come deserializzare la versione file dell'oggetto JSON in una funzionalità GeoJSON in un'app.

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>Espressioni basate su JSON

Azure Maps Web SDK supporta inoltre le espressioni di stile basate sui dati rappresentate mediante una matrice JSON. Queste stesse espressioni possono essere ricreate usando la `Expression` classe nativa nel Android SDK. In alternativa, queste espressioni basate su JSON possono essere convertite in una stringa usando una funzione Web come `JSON.stringify` e passate nel `Expression.raw(String rawExpression)` metodo. Si prenda ad esempio la seguente espressione JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

La versione file dell'espressione precedente è `"['get','title']"` e può essere letta nel Android SDK come indicato di seguito.

```java
Expression exp = Expression.raw("['get','title']")
```

L'uso di questo approccio può semplificare il riutilizzo delle espressioni di stile tra app Web e per dispositivi mobili che usano le mappe di Azure.

Questo video offre una panoramica dello stile basato sui dati nelle mappe di Azure.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Espressioni di dati

Le espressioni di dati consentono di accedere ai dati delle proprietà di una funzionalità.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `accumulated()` | d'acquisto | Ottiene il valore di una proprietà cluster accumulata finora. |
| `at(number | Expression, Expression)` | Valore | Recupera un elemento da una matrice. |
| `geometryType()` | string | Ottiene il tipo di geometria della funzionalità: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | Valore | Ottiene il valore della proprietà dalle proprietà dell'oggetto specificato. Restituisce null se la proprietà richiesta è mancante. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Determina se le proprietà di una funzionalità dispongono della proprietà specificata. |
| `id()` | Valore | Ottiene l'ID della funzionalità se ne è presente uno. |
| `in(string | number | Expression, Expression)` | boolean | Determina se un elemento esiste in una matrice |
| `length(string | Expression)` | d'acquisto | Ottiene la lunghezza di una stringa o di una matrice. |
| `properties()`| Valore | Ottiene l'oggetto delle proprietà della funzionalità. |

Le seguenti espressioni di stile Web SDK non sono supportate nel Android SDK:

- Indice di
- slice

**Esempi**

È possibile accedere alle proprietà di una funzionalità direttamente in un'espressione tramite un' `get` espressione. In questo esempio viene utilizzato il `zoneColor` valore della funzionalità per specificare la proprietà Color di un livello Bubble.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

L'esempio precedente funzionerà correttamente, se tutte le funzionalità punto hanno la `zoneColor` Proprietà. In caso contrario, il colore sarà probabilmente il "nero". Per modificare il colore di fallback, utilizzare un' `switchCase` espressione in combinazione con l' `has` espressione per verificare se la proprietà esiste. Se la proprietà non esiste, restituisce un colore di fallback.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

Per impostazione predefinita, i livelli Bubble e Symbol eseguiranno il rendering delle coordinate di tutte le forme in un'origine dati. Questo comportamento può evidenziare i vertici di un poligono o di una linea. L' `filter` opzione del livello può essere usata per limitare il tipo di geometria delle funzionalità di cui esegue il rendering, usando un' `geometryType` espressione in un'espressione booleana. Nell'esempio seguente viene limitato un livello Bubble in modo che `Point` venga eseguito il rendering solo delle funzionalità.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

L'esempio seguente consente di eseguire il `Point` `MultiPoint` rendering di entrambe le funzionalità e. 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

Analogamente, la struttura dei poligoni viene sottoposta a rendering nei livelli linea. Per disabilitare questo comportamento in un livello linea, aggiungere un filtro che consenta solo `LineString` le `MultiLineString` funzionalità e.  

Di seguito sono riportati alcuni esempi aggiuntivi di come usare le espressioni di dati:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Espressioni matematiche

Le espressioni matematiche forniscono operatori matematici per eseguire calcoli basati sui dati all'interno del Framework di espressioni.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `abs(number | Expression)` | d'acquisto | Calcola il valore assoluto del numero specificato. |
| `acos(number | Expression)` | d'acquisto | Calcola l'arcoseno del numero specificato. |
| `asin(number | Expression)` | d'acquisto | Calcola l'arcoseno del numero specificato. |
| `atan(number | Expression)` | d'acquisto | Calcola l'arcotangente del numero specificato. |
| `ceil(number | Expression)` | d'acquisto | Arrotonda il numero per eccesso al numero intero successivo. |
| `cos(number | Expression)` | d'acquisto | Calcola il coseno del numero specificato. |
| `division(number, number)` \| `division(Expression, Expression)` | d'acquisto | Divide il primo numero per il secondo numero. Espressione equivalente di Web SDK: `/` |
| `e()` | d'acquisto | Restituisce la costante matematica `e` . |
| `floor(number | Expression)` | d'acquisto | Arrotonda il numero per difetto all'intero intero precedente. |
| `log10(number | Expression)` | d'acquisto | Calcola il logaritmo in base 10 del numero specificato. |
| `log2(number | Expression)` | d'acquisto | Calcola il logaritmo in base due del numero specificato. |
| `ln(number | Expression)` | d'acquisto | Calcola il logaritmo naturale del numero specificato. |
| `ln2()` | d'acquisto | Restituisce la costante matematica `ln(2)` . |
| `max(numbers... | expressions...)` | d'acquisto | Calcola il numero massimo nel set di numeri specificato. |
| `min(numbers... | expressions...)` | d'acquisto | Calcola il numero minimo nel set di numeri specificato. |
| `mod(number, number)` \| | `mod(Expression, Expression)` | d'acquisto | Calcola il resto quando si divide il primo numero per il secondo numero. Espressione equivalente di Web SDK: `%` |
| `pi()` | d'acquisto | Restituisce la costante matematica `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | d'acquisto | Calcola il valore del primo valore elevato alla potenza del secondo numero. |
| `product(numbers... | expressions...)` | d'acquisto | Moltiplica i numeri specificati insieme. Espressione equivalente di Web SDK: `*` |
| `round(number | Expression)` | d'acquisto | Arrotonda il numero all'intero più vicino. I valori a metà vengono arrotondati per eccesso da zero. Ad esempio, `round(-1.5)` restituisce `-2` . |
| `sin(number | Expression)` | d'acquisto | Calcola il seno del numero specificato. |
| `sqrt(number | Expression)` | d'acquisto | Calcola la radice quadrata del numero specificato. |
| `subtract(number | Expression` | d'acquisto | Sottrae 0 per il numero specificato. |
| `subtract(number | Expression, number | Expression)` | d'acquisto | Sottrae i primi numeri per il secondo numero. |
| `sum(numbers... | expressions...)` | d'acquisto | Calcola la somma dei numeri specificati. |
| `tan(number | Expression)` | d'acquisto | Calcola la tangente del numero specificato. |

## <a name="boolean-expressions"></a>Espressioni booleane

Le espressioni booleane forniscono un set di espressioni di operatori booleani per la valutazione di confronti booleani.

Quando si confrontano i valori, il confronto è fortemente tipizzato. I valori di tipi diversi sono sempre considerati non uguali. I casi in cui i tipi sono noti come diversi in fase di analisi sono considerati non validi e genereranno un errore di analisi.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Restituisce `true` se tutti gli input sono `true` ; `false` in caso contrario,. |
| `any(Expression...)` | boolean | Restituisce `true` se uno degli input è `true` ; `false` in caso contrario,. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se i valori di input sono uguali; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se il primo input è rigorosamente maggiore della seconda; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se il primo input è maggiore o uguale al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se il primo input è strettamente inferiore al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se il primo input è minore o uguale al secondo; `false` in caso contrario,. È necessario che gli argomenti siano entrambi stringhe o entrambi numeri. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Restituisce `true` se i valori di input non sono uguali; `false` in caso contrario,. |
| `not(Expression | boolean)` | boolean | Negazione logica. Restituisce `true` se l'input è `false` e `false` se l'input è `true` . |

## <a name="conditional-expressions"></a>Espressioni condizionali

Le espressioni condizionali forniscono operazioni logiche simili a If-Statements.

Le espressioni seguenti eseguono operazioni di logica condizionale sui dati di input. Ad esempio, l' `switchCase` espressione fornisce la logica "if/then/else" mentre l' `match` espressione è simile a "switch-Statement". 

### <a name="switch-case-expression"></a>Espressione cambio case

Un' `switchCase` espressione è un tipo di espressione condizionale che fornisce la logica "if/then/else". Questo tipo di espressione segue un elenco di condizioni booleane. Restituisce il valore di output della prima condizione booleana per restituire true.

Lo pseudocodice seguente definisce la struttura dell' `switchCase` espressione.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Esempio**

Nell'esempio seguente vengono illustrate le diverse condizioni booleane fino a quando non ne viene trovata una che restituisce `true` , quindi viene restituito il valore associato. Se nessuna condizione booleana restituisce `true` , verrà restituito un valore di fallback.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Espressione di corrispondenza

Un' `match` espressione è un tipo di espressione condizionale che fornisce un'istruzione switch come la logica. L'input può essere qualsiasi espressione, ad esempio, `get( "entityType")` che restituisce una stringa o un numero. Ogni arresto deve avere un'etichetta che sia un singolo valore letterale o una matrice di valori letterali, i cui valori devono essere costituiti da tutte le stringhe o da tutti i numeri. L'input corrisponde a se uno dei valori nella matrice corrisponde. Ogni etichetta di arresto deve essere univoca. Se il tipo di input non corrisponde al tipo delle etichette, il risultato sarà il valore predefinito di fallback.

Lo pseudocodice seguente definisce la struttura dell' `match` espressione.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Esempi**

Nell'esempio seguente viene analizzata la `entityType` proprietà di una funzionalità punto in un livello a bolle per cercare una corrispondenza. Se viene trovata una corrispondenza, viene restituito il valore specificato o viene restituito il valore di fallback.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

Nell'esempio seguente viene utilizzata una matrice per elencare un set di etichette che devono restituire tutti lo stesso valore. Questo approccio è molto più efficiente rispetto all'inserimento di ogni etichetta singolarmente. In questo caso, se la `entityType` proprietà è "Restaurant" o "grocery_store", verrà restituito il colore "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>Espressione COALESCE

Un' `coalesce` espressione scorre un set di espressioni fino a quando non viene ottenuto il primo valore non null e restituisce tale valore.

Lo pseudocodice seguente definisce la struttura dell' `coalesce` espressione.

```java
coalesce(Expression... input)
```

**Esempio**

Nell'esempio seguente viene utilizzata un' `coalesce` espressione per impostare l' `textField` opzione di un livello di simbolo. Se la `title` proprietà non è presente nella funzionalità o è impostata su `null` , l'espressione tenterà di cercare la `subTitle` proprietà, se manca o, eseguirà il `null` fallback a una stringa vuota. 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>Espressioni di tipo

Le espressioni di tipo forniscono strumenti per il test e la conversione di tipi di dati diversi, ad esempio stringhe, numeri e valori booleani.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `array(Expression)` | Oggetto [] | Dichiara che l'input è una matrice. |
| `bool(Expression)` | boolean | Asserisce che il valore di input è un valore booleano. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | Collator | Restituisce un oggetto COLLATE da utilizzare nelle operazioni di confronto dipendenti dalle impostazioni locali. Per impostazione predefinita, le opzioni distinzione maiuscole/minuscole e con distinzione diacritici sono false. Nell'argomento locale viene specificato il tag di lingua IETF delle impostazioni locali da utilizzare. Se non viene specificato alcun valore, vengono utilizzate le impostazioni locali predefinite. Se le impostazioni locali richieste non sono disponibili, l'utilità di confronto utilizzerà impostazioni locali di fallback definite dal sistema. Utilizzare risolto-impostazioni locali per verificare i risultati del comportamento di fallback delle impostazioni locali.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | \|oggetto stringa numero booleano \| \| \| [] | Restituisce una matrice di valori letterali o un valore dell'oggetto. Utilizzare questa espressione per impedire la valutazione di una matrice o di un oggetto come espressione. Questa operazione è necessaria quando un'espressione deve restituire una matrice o un oggetto. |
| `number(Expression)` | d'acquisto | Asserisce che il valore di input è un numero. |
| `object(Expression)` | Oggetto | Asserisce che il valore di input è un oggetto. |
| `string(Expression)` | string | Asserisce che il valore di input è una stringa. |
| `toArray(Expression)` | Oggetto [] | Converte l'espressione in una matrice di oggetti JSON. |
| `toBool(Expression)` | boolean | Converte il valore di input in un valore booleano. |
| `toNumber(Expression)` | d'acquisto | Converte il valore di input in un numero, se possibile. |
| `toString(Expression)` | string | Converte il valore di input in una stringa. |
| `typeoOf(Expression)` | string | Restituisce una stringa che descrive il tipo del valore specificato. |

## <a name="color-expressions"></a>Espressioni colore

Le espressioni colore semplificano la creazione e la modifica dei valori dei colori.

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `color(int)` | color | Converte un valore integer del colore in un'espressione colore. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Crea un valore di colore dai componenti *rosso*, *verde* e *blu* che devono essere compresi tra `0` e e `255` un componente alfa di `1` . Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Crea un valore di colore dai componenti *rosso*, *verde*, *blu* che devono essere compresi tra `0` e e `255` un componente alfa in un intervallo `0` di `1` e. Se un componente non è compreso nell'intervallo, l'espressione è un errore. |
| `toColor(Expression)` | color  | Converte il valore di input in un colore. |
| `toRgba(Expression)` | color | Restituisce una matrice a quattro elementi contenente i componenti rosso, verde, blu e alfa del colore di input, in questo ordine. |

**Esempio**

Nell'esempio seguente viene creato un valore di colore RGB con un valore *rosso* di `255` e i valori *verde* e *blu* calcolati moltiplicando `2.5` per il valore della `temperature` Proprietà. Con la variazione della temperatura, il colore cambierà in tonalità diverse di *rosso*.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

Se tutti i parametri di colore sono numeri, non è necessario eseguirne il wrapping con l' `literal` espressione. Ad esempio:

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> I valori dei colori delle stringhe possono essere convertiti in un colore utilizzando il `android.graphics.Color.parseColor` metodo. Il codice seguente converte una stringa di colore esadecimale in un'espressione colore che può essere usata con un livello.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Espressioni operatore stringa

Le espressioni dell'operatore String eseguono operazioni di conversione su stringhe quali la concatenazione e la conversione del case. 

| Expression | Tipo restituito | Descrizione |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | Concatena più stringhe insieme. Ogni valore deve essere una stringa. `toString`Se necessario, utilizzare l'espressione di tipo per convertire altri tipi valore in stringa. |
| `downcase(string)` \| `downcase(Expression)` | string | Converte la stringa specificata in minuscolo. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Determina se la stringa di input utilizza un set di caratteri supportato dallo stack del tipo di carattere corrente. ad esempio `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | Restituisce il tag di lingua IETF delle impostazioni locali utilizzate dalle regole di confronto fornite. Può essere utilizzato per determinare le impostazioni locali predefinite del sistema o per determinare se le impostazioni locali richieste sono state caricate correttamente. |
| `upcase(string)` \| `upcase(Expression)` | string | Converte la stringa specificata in maiuscolo. |

**Esempio**

Nell'esempio seguente la `temperature` proprietà della funzione Point viene convertita in una stringa e quindi viene concatenato "° f" alla fine.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

L'espressione precedente esegue il rendering di un pin sulla mappa con il testo "64 ° f" sovrapposto, come illustrato nell'immagine seguente.

![Esempio di espressione dell'operatore String](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Espressioni interpolate e Step

Le espressioni interpolate e Step possono essere utilizzate per calcolare i valori lungo una curva o una funzione Step interpolata. Queste espressioni accettano un'espressione che restituisce un valore numerico come input, ad esempio `get("temperature")` . Il valore di input viene valutato in base alle coppie di valori di input e output, per determinare il valore che meglio si adatta alla curva o alla funzione Step interpolata. I valori di output sono denominati "Stops". I valori di input per ogni interruzione devono essere un numero ed essere in ordine crescente. I valori di output devono essere un numero, una matrice di numeri o un colore.

### <a name="interpolate-expression"></a>Espressione interpolate

Un' `interpolate` espressione può essere utilizzata per calcolare un set continuo e uniforme di valori mediante l'interpolazione tra valori di interruzione. Un' `interpolate` espressione che restituisce i valori dei colori produce una sfumatura di colore in cui vengono selezionati i valori dei risultati. L' `interpolate` espressione presenta i formati seguenti:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Sono disponibili tre tipi di metodi di interpolazione che possono essere utilizzati in un' `interpolate` espressione:

| Nome | Descrizione | 
|------|-------------|
| `linear()` | Esegue l'interpolazione lineare tra la coppia di arresti.  |
| `exponential(number)` \| `exponential(Expression)` | Esegue l'interpolazione esponenziale tra le interruzioni. Viene specificata una "base" e controlla la frequenza con cui l'output aumenta. I valori più elevati rendono l'output maggiore verso l'estremità superiore dell'intervallo. Un valore "base" vicino a 1 produce un output che aumenta in modo lineare.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Esegue l'interpolazione utilizzando una [curva di Bezier cubica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definita dai punti di controllo specificati. |

Il `stop` formato dell'espressione è `stop(stop, value)` .
 
Di seguito è riportato un esempio di come appaiono questi diversi tipi di interpolazioni. 

| Lineari  | Esponenziale | Curva di Bézier cubica |
|---------|-------------|--------------|
| ![Grafico interpolazione lineare](media/how-to-expressions/linear-interpolation.png) | ![Grafico di interpolazione esponenziale](media/how-to-expressions/exponential-interpolation.png) | ![Grafico di interpolazione Bezier cubica](media/how-to-expressions/bezier-curve-interpolation.png) |

**Esempio**

Nell'esempio seguente viene usata un' `linear interpolate` espressione per impostare la `bubbleColor` proprietà di un livello Bubble in base alla `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, viene restituito "Blue". Se è compreso tra 60 e 70, viene restituito Yellow. Se è compreso tra 70 e 80, viene restituito "Orange" ( `#FFA500` ). Se è maggiore o uguale a 80, verrà restituito "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.

![Esempio di espressione di interpolazione](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Espressione Step

Un' `step` espressione può essere utilizzata per calcolare valori discreti di risultati con rientri mediante la valutazione di una [funzione a tratti-Constant](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definita da stops. 

L' `interpolate` espressione presenta i formati seguenti:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Le espressioni Step restituiscono il valore di output dell'arresto immediatamente prima del valore di input oppure il primo valore di input se l'input è minore del primo arresto. 

**Esempio**

Nell'esempio seguente viene usata un' `step` espressione per impostare la `bubbleColor` proprietà di un livello Bubble in base alla `temperature` proprietà della funzionalità punto. Se il `temperature` valore è minore di 60, viene restituito "Blue". Se è compreso tra 60 e 70, viene restituito "Yellow". Se è compreso tra 70 e 80, viene restituito "Orange". Se è maggiore o uguale a 80, verrà restituito "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Nell'immagine seguente viene illustrata la scelta dei colori per l'espressione precedente.
 
![Esempio di espressione Step](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Espressioni specifiche del livello

Espressioni speciali che si applicano solo a livelli specifici.

### <a name="heat-map-density-expression"></a>Espressione densità mappa termica

Un'espressione di densità della mappa termica Recupera il valore della densità della mappa termica per ogni pixel in un livello mappa termica e viene definito come `heatmapDensity` . Questo valore è un numero compreso tra `0` e `1` . Viene usato in combinazione con un' `interpolation` espressione o `step` per definire la sfumatura di colore usata per colorare la mappa termica. Questa espressione può essere utilizzata solo nell' `heatmapColor` opzione del livello mappa termica.

> [!TIP]
> Il colore in corrispondenza dell'indice 0, in un'espressione di interpolazione o nel colore predefinito del colore di un passaggio, definisce il colore dell'area in cui non sono presenti dati. Il colore in corrispondenza dell'indice 0 può essere utilizzato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente.

**Esempio**

Questo esempio usa un'espressione di interpolazione Liner per creare una sfumatura di colore uniforme per il rendering della mappa termica. 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

Oltre a usare una sfumatura uniforme per colorare una mappa termica, i colori possono essere specificati all'interno di un set di intervalli usando un' `step` espressione. L'uso `step` di un'espressione per colorare la mappa termica suddivide visivamente la densità in intervalli simili a una mappa di contorno o di stile radar.  

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

Per ulteriori informazioni, vedere la documentazione relativa all' [aggiunta di un livello mappa termica](map-add-heat-map-layer-android.md) .

### <a name="text-field-format-expression"></a>Espressione formato campo testo

L' `format` espressione può essere utilizzata con l' `textField` opzione del livello di simboli per fornire la formattazione mista del testo. Questa espressione accetta una o più `formatEntry` espressioni che specificano una stringa e un set di `formatOptions` da accodare al campo di testo.

| Expression | Descrizione |
|------------|-------------|
| `format(Expression...)` | Restituisce il testo formattato contenente le annotazioni da utilizzare nelle voci di campo di testo in formato misto. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Restituisce una voce di stringa formattata da utilizzare nell' `format` espressione. |

Sono disponibili le opzioni di formato seguenti:

| Expression | Descrizione |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Specifica il fattore di scala per le dimensioni del carattere. Se specificato, questo valore eseguirà l'override della `textSize` proprietà per la singola stringa. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Specifica un colore da applicare a un testo durante il rendering. |

**Esempio**

Nell'esempio seguente viene formattato il campo di testo aggiungendo un tipo di carattere in grassetto e scalando verticalmente le dimensioni del carattere della `title` proprietà della funzionalità. Questo esempio aggiunge anche la `subTitle` proprietà della funzionalità in una nuova riga, con una dimensione del carattere ridimensionata.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

Questo livello eseguirà il rendering della funzionalità punto, come illustrato nell'immagine seguente:

![Immagine della funzionalità punto con il campo di testo formattato](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Espressione zoom

`zoom`Viene utilizzata un'espressione per recuperare il livello di zoom corrente della mappa in fase di rendering e viene definito come `zoom()` . Questa espressione restituisce un numero compreso tra l'intervallo minimo e massimo del livello di zoom della mappa. I controlli mappa interattiva di Azure Maps per il supporto per Web e Android 25, numerati da 0 a 24. L'utilizzo dell' `zoom` espressione consente la modifica dinamica degli stili quando viene modificato il livello di zoom della mappa. L' `zoom` espressione può essere utilizzata solo con `interpolate` `step` espressioni e.

**Esempio**

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Quando la mappa viene ingrandita, i dati vengono aggregati insieme e il livello mappa termica ha un aspetto diverso. Un' `zoom` espressione può essere utilizzata per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copra la stessa area fisica della mappa. Il livello mappa termica renderà l'aspetto più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente. Il ridimensionamento del raggio, in modo che raddoppi a ogni livello di zoom, creerà una mappa termica che risulti coerente in tutti i livelli di zoom. Questa operazione può essere eseguita usando l' `zoom` espressione con un' `base 2 exponential interpolation` espressione, con il raggio di pixel impostato per il livello di zoom minimo e un raggio scalato per il livello di zoom massimo calcolato come `2 * Math.pow(2, minZoom - maxZoom)` illustrato di seguito.

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>Espressioni di associazione variabili

Le espressioni di associazione variabili archiviano i risultati di un calcolo in una variabile. Pertanto, è possibile fare riferimento ai risultati del calcolo in un'altra posizione in un'espressione più volte. Si tratta di un'ottimizzazione utile per le espressioni che coinvolgono molti calcoli.

| Expression | Tipo restituito | Descrizione |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Archivia uno o più valori come variabili per l'utilizzo da parte dell' `var` espressione nell'espressione figlio che restituisce il risultato. |
| `var(Expression expression)` \| `var(string variableName)` | Oggetto | Fa riferimento a una variabile creata utilizzando l' `let` espressione. |

**Esempio**

In questo esempio viene utilizzata un'espressione che calcola i ricavi relativi al rapporto di temperatura, quindi utilizza un' `case` espressione per valutare diverse operazioni booleane su questo valore. L' `let` espressione viene utilizzata per archiviare i ricavi relativi al rapporto di temperatura, in modo che sia necessario calcolarli una sola volta. L' `var` espressione fa riferimento a questa variabile con la frequenza necessaria senza ricalcolarla.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui livelli che supportano le espressioni:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer-android.md)
