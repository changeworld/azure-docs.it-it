---
title: Gestire gli eventi della mappa nelle mappe Android | Mappe Microsoft Azure
description: Informazioni sugli eventi che vengono generati quando gli utenti interagiscono con maps. Visualizzare un elenco di tutti gli eventi di mapping supportati. Vedere come usare le mappe di Azure Android SDK per gestire gli eventi.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681537"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagire con la mappa (Android SDK)

Questo articolo illustra come usare il gestore eventi maps.

## <a name="interact-with-the-map"></a>Interazione con la mappa

La mappa gestisce tutti gli eventi tramite la relativa `events` Proprietà. Nella tabella seguente sono elencati tutti gli eventi della mappa supportati.

| Event                  | Formato del gestore eventi | Descrizione |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Generato dopo il rendering dell'ultimo frame prima che la mappa passi in stato "inattivo" in questi casi:<ul><li>Non sono in corso transizioni della fotocamera.</li><li>Sono stati caricati tutte le tessere attualmente richieste.</li><li>Sono state completate tutte le animazioni di dissolvenza/transizione.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Generato ripetutamente durante una transizione animata da una vista a un'altra, come risultato dell'interazione utente o dei metodi. |
| `OnCameraMoveCanceled` | `()`                 | Generato quando una richiesta di movimento alla fotocamera è stata annullata. |
| `OnCameraMoveStarted`  | `(int reason)`       | Generato immediatamente prima che la mappa inizi una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi. L' `reason` argomento del listener di eventi restituisce un valore intero che fornisce informazioni dettagliate su come è stato avviato lo spostamento della fotocamera. Di seguito è riportato l'elenco dei possibili motivi:<ul><li>1: movimento</li><li>2: animazione per sviluppatori</li><li>3: animazione API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Generato quando si preme e si rilascia la mappa nello stesso punto sulla mappa. |
| `OnFeatureClick`       | `(List<Feature>)`    | Generato quando si preme e si rilascia la mappa nello stesso punto di una funzionalità.  |
| `OnLongClick`          | `(double lat, double lon)` | Generato quando la mappa viene premuta, viene mantenuta per un istante e quindi rilasciata nello stesso punto sulla mappa. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Generato quando la mappa viene premuta, viene mantenuta per un istante e quindi rilasciata nello stesso punto di una funzionalità. |
| `OnReady`              | `(AzureMap map)`     | Generato quando viene caricata inizialmente la mappa o quando viene caricata la modifica dell'orientamento dell'app e le risorse minime obbligatorie della mappa e la mappa è pronta per interagire a livello di codice. |

Nel codice seguente viene illustrato come aggiungere gli `OnClick` `OnFeatureClick` eventi, e `OnCameraMove` alla mappa.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Per ulteriori informazioni, vedere la documentazione relativa all' [esplorazione della mappa](how-to-use-android-map-control-library.md#navigating-the-map) relativa all'interazione con gli eventi map e trigger.

## <a name="scope-feature-events-to-layer"></a>Ambito degli eventi di funzionalità al livello

Quando si aggiungono gli `OnFeatureClick` `OnLongFeatureClick` eventi o alla mappa, è possibile passare un ID livello come secondo parametro. Quando viene passato un ID livello, l'evento viene generato solo se l'evento si verifica su tale livello. Gli eventi con ambito a livelli sono supportati dai livelli Symbol, Bubble, line e Polygon.

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
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

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
