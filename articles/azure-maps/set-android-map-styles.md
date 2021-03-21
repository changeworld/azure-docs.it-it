---
title: Impostare uno stile mappa in Android Maps | Mappe Microsoft Azure
description: Vengono illustrati due modi per impostare lo stile di una mappa. Per modificare lo stile, vedere come usare le mappe di Azure Android SDK nel file di layout o nella classe Activity.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100985"
---
# <a name="set-map-style-android-sdk"></a>Impostare lo stile della mappa (Android SDK)

Questo articolo illustra due modi per impostare gli stili della mappa usando le mappe di Azure Android SDK. Mappe di Azure include sei stili di mappe diversi tra cui scegliere. Per altre informazioni sugli stili di mappa supportati, vedere [stili di mappa supportati in mappe di Azure](supported-map-styles.md).

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) .

## <a name="set-map-style-in-the-layout"></a>Impostare lo stile della mappa nel layout

È possibile impostare uno stile mappa nel file di layout per la classe Activity quando si aggiunge il controllo map. Il codice seguente consente di impostare la posizione centrale, il livello di zoom e lo stile della mappa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

La schermata seguente mostra il codice precedente che visualizza una mappa stradale con lo stile scuro in grigio.

![Mappa con stile della mappa stradale scura in scala di grigi](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Impostare lo stile della mappa nel codice

Lo stile della mappa può essere impostato a livello di codice nel codice usando il `setStyle` metodo della mappa. Il codice seguente imposta la posizione centrale e il livello di zoom usando il `setCamera` Metodo Maps e lo stile della mappa su `SATELLITE_ROAD_LABELS` .

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

Lo screenshot seguente mostra il codice precedente che visualizza una mappa con lo stile delle etichette strada satellite.

![Mappa con stile delle etichette strada satellite](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Impostazione della fotocamera della mappa

La fotocamera mappa controlla la parte della mappa visualizzata nella mappa. La fotocamera può trovarsi nel layout a livello di codice. Quando viene impostato nel codice, sono disponibili due metodi principali per impostare la posizione della mappa; usare Center e zoom oppure passare un rettangolo di delimitazione. Nel codice seguente viene illustrato come impostare tutte le opzioni opzionali della fotocamera quando si utilizzano `center` e `zoom` .

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Spesso è consigliabile concentrare la mappa su un set di dati. Un rettangolo di delimitazione può essere calcolato dalle funzionalità usando il `MapMath.fromData` metodo e può essere passato nell' `bounds` opzione della fotocamera della mappa. Quando si imposta una visualizzazione mappa basata su un rettangolo di selezione, spesso è utile specificare un `padding` valore per tenere conto della dimensione in pixel dei punti di cui viene eseguito il rendering come bolle o simboli. Il codice seguente illustra come impostare tutte le opzioni opzionali della fotocamera quando si usa un rettangolo di delimitazione per impostare la posizione della fotocamera.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Si noti che le proporzioni di un rettangolo di delimitazione potrebbero non essere uguali a quelle della mappa, in quanto tale mappa mostrerà spesso l'area del riquadro delimitatore, ma sarà spesso solo verticalmente o orizzontalmente.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)
