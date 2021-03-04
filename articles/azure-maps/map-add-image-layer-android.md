---
title: Aggiungere un livello immagine a una mappa Android | Mappe Microsoft Azure
description: Informazioni su come aggiungere immagini a una mappa. Vedere come usare le mappe di Azure Android SDK per personalizzare i livelli di immagine e le immagini sovrapposte su set di coordinate fissi.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054691"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Aggiungere un livello immagine a una mappa (Android SDK)

Questo articolo illustra come sovrapporre un'immagine a un set fisso di coordinate. Di seguito sono riportati alcuni esempi di tipi di immagini diversi che possono sovrapporsi alle mappe:

* Immagini acquisite dai droni
* Compilazione di planimetrie
* Immagini della mappa cronologica o altre specializzate
* Progetti per i siti dei processi

> [!TIP]
> Un livello immagine è un modo semplice per sovrapporre un'immagine a una mappa. Si noti che le immagini di grandi dimensioni possono utilizzare una grande quantità di memoria e potenzialmente causare problemi di prestazioni. In questo caso, provare a suddividere l'immagine in riquadri e a caricarli nella mappa come livello sezione.

## <a name="add-an-image-layer"></a>Aggiungere un livello per le immagini

Il codice seguente sovrappone un'immagine di una [mappa di Newark, New Jersey, da 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa. Questa immagine viene aggiunta alla `drawable` cartella del progetto. Un livello immagine viene creato impostando l'immagine e le coordinate per i quattro angoli nel formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Spesso è consigliabile aggiungere livelli di immagine al di sotto del `label` livello.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

In alternativa, è possibile specificare un URL di un'immagine ospitata in linea. Tuttavia, se lo scenario lo consente, aggiungere l'immagine alla cartella dei progetti `drawable` , che verrà caricata più velocemente poiché l'immagine sarà disponibile localmente e non dovrà essere scaricata.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Lo screenshot seguente mostra una mappa di Newark, New Jersey, da 1922 sovrapposta usando un livello immagine.

![Mappa di Newark, New Jersey, da 1922 sovrapposta usando un livello immagine](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importa un file KML come sovrapposizione di base

In questo esempio viene illustrato come aggiungere informazioni sulla sovrapposizione di base KML come livello immagine sulla mappa. Le sovrapposizioni di base KML forniscono coordinate Nord, Sud, est e ovest e una rotazione in senso antiorario. Tuttavia, il livello immagine prevede le coordinate per ogni angolo dell'immagine. La sovrimpressione del terreno KML in questo esempio è per la Cattedrale di Chartres ed è originata da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Il codice usa il metodo statico della `getCoordinatesFromEdges` `ImageLayer` classe. Questo metodo calcola i quattro angoli dell'immagine usando le informazioni relative a nord, Sud, est, ovest e rotazione della sovrimpressione del terreno KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Lo screenshot seguente mostra una mappa con una sovrapposizione del terreno KML sovrapposta usando un livello immagine.

![Eseguire il mapping con una sovrapposizione del terreno KML sovrapposta usando un livello immagine](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Usare i `getPixels` `getPositions` metodi e della classe del livello immagine per eseguire la conversione tra le coordinate geografiche del livello immagine posizionato e le coordinate del pixel dell'immagine locale.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente per altre informazioni sui modi per sovrapporre le immagini in una mappa.

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](how-to-add-tile-layer-android-map.md)