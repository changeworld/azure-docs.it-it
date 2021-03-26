---
title: Gestire gli eventi della mappa nelle mappe Android | Mappe Microsoft Azure
description: Informazioni sugli eventi che vengono generati quando gli utenti interagiscono con maps. Visualizzare un elenco di tutti gli eventi di mapping supportati. Vedere come usare le mappe di Azure Android SDK per gestire gli eventi.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ebe61e5956dc0f35794211a336eb7d884ee18d76
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608902"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagire con la mappa (Android SDK)

Questo articolo illustra come usare il gestore eventi maps.

## <a name="interact-with-the-map"></a>Interazione con la mappa

La mappa gestisce tutti gli eventi tramite la relativa `events` Proprietà. Nella tabella seguente sono elencati tutti gli eventi della mappa supportati.

| Evento                  | Formato del gestore eventi | Descrizione |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Generato dopo il rendering dell'ultimo frame prima che la mappa passi in stato "inattivo" in questi casi:<ul><li>Non sono in corso transizioni della fotocamera.</li><li>Sono stati caricati tutte le tessere attualmente richieste.</li><li>Sono state completate tutte le animazioni di dissolvenza/transizione.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Generato ripetutamente durante una transizione animata da una vista a un'altra, come risultato dell'interazione utente o dei metodi. |
| `OnCameraMoveCanceled` | `()`                 | Generato quando una richiesta di movimento alla fotocamera è stata annullata. |
| `OnCameraMoveStarted`  | `(int reason)`       | Generato immediatamente prima che la mappa inizi una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi. L' `reason` argomento del listener di eventi restituisce un valore intero che fornisce informazioni dettagliate su come è stato avviato lo spostamento della fotocamera. Nell'elenco seguente vengono descritti i possibili motivi:<ul><li>1: movimento</li><li>2: animazione per sviluppatori</li><li>3: animazione API</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Generato quando si preme e si rilascia la mappa nello stesso punto sulla mappa. Questo gestore eventi restituisce un valore booleano che indica se l'evento deve essere utilizzato o passato ulteriormente ad altri listener di eventi. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Generato quando si preme e si rilascia la mappa nello stesso punto di una funzionalità. Questo gestore eventi restituisce un valore booleano che indica se l'evento deve essere utilizzato o passato ulteriormente ad altri listener di eventi. |
| `OnLayerAdded` | `(Layer layer)` | Generato quando un livello viene aggiunto alla mappa. |
| `OnLayerRemoved` | `(Layer layer)` | Generato quando un livello viene rimosso dalla mappa. |
| `OnLoaded` | `()` | Generato immediatamente dopo il download di tutte le risorse necessarie e il primo rendering visivo completo della mappa. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Generato quando la mappa viene premuta, viene mantenuta per un istante e quindi rilasciata nello stesso punto sulla mappa. Questo gestore eventi restituisce un valore booleano che indica se l'evento deve essere utilizzato o passato ulteriormente ad altri listener di eventi. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Generato quando la mappa viene premuta, viene mantenuta per un istante e quindi rilasciata nello stesso punto di una funzionalità. Questo gestore eventi restituisce un valore booleano che indica se l'evento deve essere utilizzato o passato ulteriormente ad altri listener di eventi. |
| `OnReady`              | `(AzureMap map)`     | Generato quando viene caricata inizialmente la mappa o quando viene caricata la modifica dell'orientamento dell'app e le risorse minime obbligatorie della mappa e la mappa è pronta per interagire a livello di codice. |
| `OnSourceAdded` | `(Source source)` | Generato quando viene aggiunto alla mappa un oggetto `DataSource` o `VectorTileSource`. |
| `OnSourceRemoved` | `(Source source)` | Generato quando viene rimosso dalla mappa un oggetto `DataSource` o `VectorTileSource`. |
| `OnStyleChange` | `()` | Generato quando lo stile della mappa viene caricato o modificato. |

Nel codice seguente viene illustrato come aggiungere gli `OnClick` `OnFeatureClick` eventi, e `OnCameraMove` alla mappa.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Per ulteriori informazioni, vedere la documentazione relativa all' [esplorazione della mappa](how-to-use-android-map-control-library.md#navigating-the-map) relativa all'interazione con gli eventi map e trigger.

## <a name="scope-feature-events-to-layer"></a>Ambito degli eventi di funzionalità al livello

Quando si aggiungono gli `OnFeatureClick` `OnLongFeatureClick` eventi o alla mappa, è possibile passare un'istanza di livello o un ID livello come secondo parametro. Quando viene passato un livello, l'evento viene generato solo se l'evento si verifica su tale livello. Gli eventi che hanno come ambito i livelli sono supportati dai livelli Symbol, Bubble, line e Polygon.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Esplorazione della mappa](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
