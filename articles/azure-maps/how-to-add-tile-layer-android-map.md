---
title: Aggiungere un livello sezione a Maps Android | Mappe Microsoft Azure
description: Informazioni su come aggiungere un livello sezione a una mappa. Vedere un esempio che usa le mappe di Azure Android SDK per aggiungere una sovrapposizione di radar meteorologici a una mappa.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608986"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Aggiungere un livello sezione a una mappa (Android SDK)

Questo articolo illustra come eseguire il rendering di un livello sezione su una mappa usando le mappe di Azure Android SDK. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Altre informazioni sul sistema di riquadri di Mappe di Azure sono reperibili nella documentazione [Livelli di Zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate e archiviate come qualsiasi altra immagine in un server, usando una convenzione di denominazione che il livello sezione riconosce. In alternativa, è possibile eseguire il rendering di queste immagini con un servizio dinamico che genera le immagini quasi in tempo reale. Sono supportate tre diverse convenzioni di denominazione del servizio affiancate dalla classe TileLayer di Azure Maps:

* Notazione zoom di X, Y: in base al livello di zoom, x è la posizione nella colonna e y è la posizione nella riga del riquadro nella griglia dei riquadri.
* Notazione Quadkey: combinazione delle informazioni x, y e zoom in un singolo valore stringa che sia un identificatore univoco per un riquadro.
* È possibile utilizzare le coordinate del rettangolo di delimitazione per specificare un'immagine nel formato `{west},{south},{east},{north}` , che viene comunemente utilizzata da [Web-Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello riquadro da un'immagine, ma anche i dati vettoriali possono essere sottoposti a rendering come livello riquadro. Eseguendo il rendering dei dati vettoriali come livello sezione, il controllo mappa deve caricare solo i riquadri, che possono essere molto più piccoli in dimensioni file rispetto ai dati vettoriali che rappresentano. Molti utenti usano questa tecnica per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL di riquadro passato in un livello riquadro deve essere un URL http/https indirizzato a una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti:

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}` : Segnaposto per i valori del sottodominio, se viene specificato il valore del sottodominio.
* `azmapsdomain.invalid` : Segnaposto per allineare il dominio e l'autenticazione delle richieste del riquadro con gli stessi valori usati dalla mappa. Usare questo valore quando si chiama un servizio affiancato ospitato da mappe di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare il processo in questo articolo, è necessario installare [Azure Maps Android SDK](how-to-use-android-map-control-library.md) per caricare una mappa.

## <a name="add-a-tile-layer-to-the-map"></a>Aggiungere un livello sezione alla mappa

In questo esempio viene illustrato come creare un livello sezione che punta a un set di riquadri. In questo esempio viene utilizzato il sistema di affiancamento "x, y, zoom". L'origine di questo livello sezione è il [progetto OpenSeaMap](https://openseamap.org/index.php), che contiene i grafici nautici originati dalla folla. Spesso quando si visualizzano i livelli dei riquadri è opportuno poter visualizzare chiaramente le etichette delle città sulla mappa. Questo comportamento può essere eseguito inserendo il livello sezione sotto i livelli dell'etichetta della mappa.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Lo screenshot seguente mostra il codice precedente che mostra un livello sezione di informazioni nautiche su una mappa con uno stile di scala di grigi scuro.

![Mappa Android visualizzazione del livello sezione](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Aggiungere un servizio di mapping Web OGC (WMS)

Un servizio di mapping Web (WMTS) è uno standard Open Geospatial Consortium (OGC) per la manutenzione di immagini di dati della mappa. In questo formato sono disponibili molti set di dati aperti che è possibile usare con le mappe di Azure. Questo tipo di servizio può essere utilizzato con un livello sezione se il servizio supporta il `EPSG:3857` sistema di riferimento delle coordinate (CRS). Quando si utilizza un servizio WMS, impostare i parametri width e Height sullo stesso valore supportato dal servizio, assicurarsi di impostare questo stesso valore nell' `tileSize` opzione. Nell'URL formattato, impostare il `BBOX` parametro del servizio con il `{bbox-epsg-3857}` segnaposto.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Lo screenshot seguente illustra il codice precedente che consente di sovrapporre un servizio di mapping Web dei dati geologici del [sondaggio geologico statunitense](https://mrdata.usgs.gov/) sopra una mappa, sotto le etichette.

![Mappa di Android visualizzazione del livello sezione WMS](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Aggiungere un servizio del riquadro di mapping Web OGC (WMTS)

Un servizio Web-mapping Tile (WMTS) è uno standard Open Geospatial Consortium (OGC) per il servizio di sovrapposizioni basate su affiancate per le mappe. In questo formato sono disponibili molti set di dati aperti che è possibile usare con le mappe di Azure. Questo tipo di servizio può essere utilizzato con un livello sezione se il servizio supporta `EPSG:3857` o `GoogleMapsCompatible` CRS (Coordinated Reference System). Quando si usa un servizio WMTS, impostare i parametri width e Height sullo stesso valore supportato dal servizio, assicurarsi di impostare questo stesso valore nell' `tileSize` opzione. Nell'URL formattato sostituire i seguenti segnaposto di conseguenza:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Lo screenshot seguente mostra il codice precedente che consente di sovrapporre un servizio affiancato di mapping Web di immagini dalla [mappa nazionale degli Stati Uniti del sondaggio (USGS)](https://viewer.nationalmap.gov/services/) su una mappa, sotto le strade e le etichette.

![Mappa Android che Visualizza il livello sezione WMTS](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente per altre informazioni sui modi per sovrapporre le immagini in una mappa.

> [!div class="nextstepaction"]
> [Livello immagine](map-add-image-layer-android.md)
