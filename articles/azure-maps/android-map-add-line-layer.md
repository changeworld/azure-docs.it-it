---
title: Aggiungere un livello linea a Maps Android | Mappe Microsoft Azure
description: Informazioni su come aggiungere linee alle mappe. Vedere esempi che usano le mappe di Azure Android SDK per aggiungere i livelli di linea alle mappe e per personalizzare le linee con simboli e sfumature di colore.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681586"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Aggiungere un livello linea alla mappa (Android SDK)

Un livello linea può essere usato per eseguire il rendering delle funzionalità `LineString` e `MultiLineString` come percorsi o route sulla mappa. Un livello linea può essere usato anche per eseguire il rendering della struttura delle funzionalità `Polygon` e `MultiPolygon`. Un'origine dati è connessa a un livello linea per fornire a quest'ultimo i dati di cui eseguire il rendering.

> [!TIP]
> Per impostazione predefinita, i livelli linea eseguiranno il rendering delle coordinate dei poligoni e delle linee in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità di geometria LineString, impostare l' `filter` opzione del livello su `eq(geometryType(), "LineString")` . Se si desidera includere anche le funzionalità MultiLineString, impostare l' `filter` opzione del livello su `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) . I blocchi di codice in questo articolo possono essere inseriti nel `onReady` gestore eventi maps.

## <a name="add-a-line-layer"></a>Aggiungere un livello per le linee

Il codice seguente illustra come creare una linea. Aggiungere la riga a un'origine dati, quindi eseguirne il rendering con un livello linea usando la `LineLayer` classe.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Lo screenshot seguente mostra il codice precedente che esegue il rendering di una riga in un livello linea.

![Mappa con una riga sottoposta a rendering usando il livello linea](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Stile linea unità dati

Il codice seguente crea due funzionalità linea e aggiunge un valore di limite di velocità come proprietà a ogni riga. Un livello linea usa un'espressione di tipo unità dati che colora le linee in base al valore del limite di velocità. Poiché i dati della linea si sovrappongono lungo le strade, il codice seguente aggiunge il livello linea al di sotto del livello etichetta, in modo che le etichette strada possano ancora essere lette chiaramente.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

Lo screenshot seguente mostra il codice precedente che esegue il rendering di due righe in un livello linea con il relativo colore recuperato da un'espressione di stile basata sui dati basata su una proprietà nelle funzionalità della riga.

![Mappa con linee con stile dell'unità dati di cui è stato eseguito il rendering in un livello linea](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>Aggiungere simboli lungo una linea

Questo esempio illustra come aggiungere icone a freccia lungo una linea sulla mappa. Quando si usa un livello di simboli, impostare l' `symbolPlacement` opzione su `SymbolPlacement.LINE` . Questa opzione consente di eseguire il rendering dei simboli lungo la linea e di ruotare le icone (0 gradi = destra).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

Per questo esempio, l'immagine seguente è stata caricata nella cartella di cui è stato disegnato l'app.

| ![Immagine icona freccia viola](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

La schermata seguente mostra il codice sopra riportato per eseguire il rendering di una riga con le icone a freccia visualizzate lungo.

![Mappa con linee con stile di unità dati con frecce visualizzate in un livello linea](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
