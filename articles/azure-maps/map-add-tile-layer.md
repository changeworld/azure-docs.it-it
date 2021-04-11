---
title: Aggiungere un livello sezione a una mappa | Mappe Microsoft Azure
description: Informazioni su come sovrapporre le immagini nelle mappe. Vedere un esempio che usa Azure Maps Web SDK per aggiungere un livello sezione contenente una sovrapposizione di radar meteorologici a una mappa.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608103"
---
# <a name="add-a-tile-layer-to-a-map"></a>Aggiungere un livello riquadro a una mappa

Questo articolo illustra come sovrapporre un livello sezione sulla mappa. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Per altre informazioni sul sistema di affiancamento delle mappe di Azure, vedere [livelli di zoom e griglia dei riquadri](zoom-levels-and-tile-grid.md).

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate o sottoposte a rendering dinamico. Le immagini con rendering preliminare vengono archiviate come qualsiasi altra immagine in un server utilizzando una convenzione di denominazione riconosciuta dal livello sezione. Le immagini sottoposte a rendering dinamico usano un servizio per caricare le immagini quasi in tempo reale. Sono supportate tre diverse convenzioni di denominazione del servizio affiancate dalla classe [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) di Azure Maps:

* X, Y, notazione zoom-X è la colonna, Y è la posizione della riga del riquadro nella griglia affiancata e la notazione di zoom un valore in base al livello di zoom.
* Notazione quadkey: combina le informazioni x, y e zoom in un unico valore stringa. Questo valore stringa diventa un identificatore univoco per un singolo riquadro.
* Rettangolo di delimitazione: specificare un'immagine nel formato delle coordinate del rettangolo di delimitazione: `{west},{south},{east},{north}` . Questo formato viene comunemente usato da [Web-Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello sezione da un'immagine. anche i dati vettoriali possono essere visualizzati come livello sezione. Eseguendo il rendering dei dati vettoriali come livello sezione, il controllo mappa deve caricare solo i riquadri più piccoli nelle dimensioni dei file rispetto ai dati vettoriali che rappresentano. Questa tecnica viene comunemente usata per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL del riquadro passato a un livello sezione deve essere un URL http o HTTPS per una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti:

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}` : Segnaposto per i valori del sottodominio, se specificato, `subdomain` verrà aggiunto.
* `{azMapsDomain}` : Segnaposto per allineare il dominio e l'autenticazione delle richieste del riquadro con gli stessi valori usati dalla mappa.

## <a name="add-a-tile-layer"></a>Aggiungere un livello per i riquadri

 In questo esempio viene illustrato come creare un livello sezione che punta a un set di riquadri. In questo esempio viene utilizzato il sistema di affiancamento zoom x, y. l'origine di questo livello sezione è il [progetto OpenSeaMap](https://openseamap.org/index.php), che contiene i grafici nautici originati dalla folla. Quando si visualizzano i dati radar, idealmente gli utenti visualizzano chiaramente le etichette delle città mentre esplorano la mappa. Questo comportamento può essere implementato inserendo il livello sezione al di sotto del `labels` livello.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello riquadro usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il livello riquadro <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>dell'elemento Pen usando X, Y e Z</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Aggiungere un servizio di mapping Web OGC (WMS)

Un servizio di mapping Web (WMTS) è uno standard Open Geospatial Consortium (OGC) per la manutenzione di immagini di dati della mappa. In questo formato sono disponibili molti set di dati aperti che è possibile usare con le mappe di Azure. Questo tipo di servizio può essere utilizzato con un livello sezione se il servizio supporta il `EPSG:3857` sistema di riferimento delle coordinate (CRS). Quando si utilizza un servizio WMS, impostare i parametri width e Height sullo stesso valore supportato dal servizio, assicurarsi di impostare questo stesso valore nell' `tileSize` opzione. Nell'URL formattato, impostare il `BBOX` parametro del servizio con il `{bbox-epsg-3857}` segnaposto.

Lo screenshot seguente illustra il codice precedente che consente di sovrapporre un servizio di mapping Web dei dati geologici del [sondaggio geologico statunitense](https://mrdata.usgs.gov/) sopra una mappa, sotto le etichette.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="Livello sezione WMS" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/BapjZqr'>livello sezione</a> di Pen WMS di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Aggiungere un servizio del riquadro di mapping Web OGC (WMTS)

Un servizio Web-mapping Tile (WMTS) è uno standard Open Geospatial Consortium (OGC) per il servizio di sovrapposizioni basate su affiancate per le mappe. In questo formato sono disponibili molti set di dati aperti che è possibile usare con le mappe di Azure. Questo tipo di servizio può essere utilizzato con un livello sezione se il servizio supporta `EPSG:3857` o `GoogleMapsCompatible` CRS (Coordinated Reference System). Quando si usa un servizio WMTS, impostare i parametri width e Height sullo stesso valore supportato dal servizio, assicurarsi di impostare questo stesso valore nell' `tileSize` opzione. Nell'URL formattato sostituire i seguenti segnaposto di conseguenza:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Lo screenshot seguente mostra il codice precedente che consente di sovrapporre un servizio affiancato di mapping Web di immagini dalla [mappa nazionale degli Stati Uniti del sondaggio (USGS)](https://viewer.nationalmap.gov/services/) su una mappa, sotto le strade e le etichette.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello sezione WMTS" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/BapjZVY'>livello affiancato Pen WMTS</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizzare un livello riquadro

La classe del livello sezione presenta molte opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello riquadro' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opzioni del livello riquadro</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per le immagini](./map-add-image-layer.md)
