---
title: Aggiungere un livello sezione a Maps Android | Mappe Microsoft Azure
description: Informazioni su come aggiungere un livello sezione a una mappa. Vedere un esempio che usa le mappe di Azure Android SDK per aggiungere una sovrapposizione di radar meteorologici a una mappa.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679292"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Aggiungere un livello sezione a una mappa (Android SDK)

Questo articolo illustra come eseguire il rendering di un livello sezione su una mappa usando le mappe di Azure Android SDK. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Altre informazioni sul sistema di riquadri di Mappe di Azure sono reperibili nella documentazione [Livelli di Zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate e archiviate come qualsiasi altra immagine in un server, usando una convenzione di denominazione che il livello sezione riconosce. In alternativa, è possibile eseguire il rendering di queste immagini con un servizio dinamico che genera le immagini quasi in tempo reale. Sono supportate tre diverse convenzioni di denominazione del servizio affiancate dalla classe TileLayer di Azure Maps:

* Notazione zoom di X, Y: in base al livello di zoom, x è la posizione nella colonna e y è la posizione nella riga del riquadro nella griglia dei riquadri.
* Notazione Quadkey: combinazione delle informazioni x, y e zoom in un singolo valore stringa che sia un identificatore univoco per un riquadro.
* È possibile utilizzare le coordinate del riquadro delimitatore per specificare un'immagine nel formato `{west},{south},{east},{north}` , che viene comunemente utilizzata da [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello riquadro da un'immagine, ma anche i dati vettoriali possono essere sottoposti a rendering come livello riquadro. Eseguendo il rendering dei dati vettoriali come livello sezione, il controllo mappa deve caricare solo i riquadri, che possono essere molto più piccoli in dimensioni file rispetto ai dati vettoriali che rappresentano. Molti utenti usano questa tecnica per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL di riquadro passato in un livello riquadro deve essere un URL http/https indirizzato a una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti: 

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}` : Segnaposto per i valori del sottodominio, se viene specificato il valore del sottodominio.

## <a name="prerequisites"></a>Prerequisiti

Per completare il processo in questo articolo, è necessario installare [Azure Maps Android SDK](how-to-use-android-map-control-library.md) per caricare una mappa.

## <a name="add-a-tile-layer-to-the-map"></a>Aggiungere un livello sezione alla mappa

In questo esempio viene illustrato come creare un livello sezione che punta a un set di riquadri. In questo esempio viene utilizzato il sistema di affiancamento "x, y, zoom". L'origine di questo livello sezione è il [progetto OpenSeaMap](https://openseamap.org/index.php), che contiene i grafici nautici originati dalla folla. Spesso quando si visualizzano i livelli dei riquadri è opportuno poter visualizzare chiaramente le etichette delle città sulla mappa. Questo comportamento può essere eseguito inserendo il livello sezione sotto i livelli dell'etichetta della mappa.

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

Lo screenshot seguente mostra il codice precedente che mostra un livello sezione di informazioni nautiche su una mappa con uno stile di scala di grigi scuro.

![Mappa Android visualizzazione del livello sezione](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente per altre informazioni sui modi per impostare gli stili della mappa

> [!div class="nextstepaction"]
> [Modificare lo stile della mappa](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer-android.md)
