---
title: Aggiungere un livello di simbolo a una mappa | Mappe Microsoft Azure
description: Informazioni su come aggiungere simboli personalizzati, ad esempio testo o icone, a maps. Per questo scopo, vedere come usare le origini dati e i livelli di simboli in Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891056"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Aggiungere un livello simbolo a una mappa

Connettere un simbolo a un'origine dati e usarlo per eseguire il rendering di un'icona o di un testo in un determinato punto. 

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Usare un livello di simboli per eseguire il rendering di grandi raccolte di punti sulla mappa. Rispetto al marcatore HTML, il livello dei simboli esegue il rendering di un numero elevato di dati punto sulla mappa, con prestazioni migliori. Tuttavia, il livello dei simboli non supporta gli elementi CSS e HTML tradizionali per lo stile.  

> [!TIP]
> Per impostazione predefinita, i livelli simbolo eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità di geometria dei punti, impostare la `filter` proprietà del livello su `['==', ['geometry-type'], 'Point']` o `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , se si desidera, includere anche le funzionalità multipoint.

Maps image sprite Manager carica le immagini personalizzate usate dal livello symbol. Supporta i formati di immagine seguenti:

- JPEG
- PNG
- SVG
- BMP
- GIF (nessuna animazione)

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Prima di poter aggiungere un livello di simbolo alla mappa, è necessario eseguire un paio di passaggi. Prima di tutto, creare un'origine dati e aggiungerla alla mappa. Creare un livello di simbolo. Passare quindi l'origine dati al livello del simbolo per recuperare i dati dall'origine dati. Aggiungere infine i dati nell'origine dati, in modo da eseguire il rendering di un elemento. 

Il codice seguente illustra gli elementi da aggiungere alla mappa dopo che è stato caricato. Questo esempio esegue il rendering di un singolo punto sulla mappa usando un livello di simbolo. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Sono disponibili quattro tipi diversi di dati punto che è possibile aggiungere alla mappa:

- Geometria del punto GeoJSON: questo oggetto contiene solo una coordinata di un punto e nient'altro. `atlas.data.Point`È possibile utilizzare la classe helper per creare facilmente questi oggetti.
- GeoJSON MultiPoint Geometry: questo oggetto contiene le coordinate di più punti e nient'altro. `atlas.data.MultiPoint`È possibile utilizzare la classe helper per creare facilmente questi oggetti.
- Funzionalità GeoJSON: questo oggetto è costituito da qualsiasi geometria GeoJSON e da un set di proprietà che contengono i metadati associati alla geometria. `atlas.data.Feature`È possibile utilizzare la classe helper per creare facilmente questi oggetti.
- `atlas.Shape` la classe è simile alla funzionalità GeoJSON. Entrambe sono costituite da una geometria GeoJSON e da un set di proprietà che contengono i metadati associati alla geometria. Se un oggetto GeoJSON viene aggiunto a un'origine dati, è possibile eseguirne facilmente il rendering in un livello. Tuttavia, se la proprietà coordinate dell'oggetto GeoJSON viene aggiornata, l'origine dati e la mappa non cambiano. Questo perché non è presente alcun meccanismo nell'oggetto JSON per attivare un aggiornamento. La classe Shape fornisce funzioni per l'aggiornamento dei dati in esso contenuti. Quando viene apportata una modifica, l'origine dati e la mappa vengono automaticamente notificati e aggiornati. 

L'esempio di codice seguente crea una geometria del punto GeoJSON e la passa alla classe per facilitarne l' `atlas.Shape` aggiornamento. Il centro della mappa viene inizialmente usato per eseguire il rendering di un simbolo. Un evento Click viene aggiunto alla mappa in modo tale che, quando viene attivato, le coordinate del mouse vengono utilizzate con la `setCoordinates` funzione Shapes. Le coordinate del mouse vengono registrate al momento dell'evento click. Quindi `setCoordinates` Aggiorna la posizione del simbolo sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Modifica della posizione della puntina' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Per impostazione predefinita, i livelli dei simboli ottimizzano il rendering dei simboli nascondendo i simboli che si sovrappongono. Quando si esegue lo zoom avanti, i simboli nascosti diventano visibili. Per disabilitare questa funzionalità ed eseguire il rendering di tutti i simboli in qualsiasi momento, impostare la `allowOverlap` proprietà delle `iconOptions` Opzioni su `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Aggiungere un'icona personalizzata a un livello simbolo

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Di conseguenza tutte le risorse, ad esempio le immagini icona, devono essere caricate nel contesto di WebGL. In questo esempio viene illustrato come aggiungere un'icona personalizzata alle risorse della mappa. Questa icona viene quindi utilizzata per eseguire il rendering dei dati del punto con un simbolo personalizzato sulla mappa. La proprietà `textField` del livello simbolo richiede che venga specificata un'espressione. In questo caso, si vuole eseguire il rendering della proprietà temperature. Poiché temperature è un numero, è necessario convertirlo in una stringa. Inoltre, si desidera aggiungere "° f". Per eseguire questa concatenazione è possibile utilizzare un'espressione. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Icona immagine simbolo personalizzata' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Icona immagine simbolo personalizzata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare con il livello simbolo. Per altre informazioni, vedere il documento [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-symbol-layer"></a>Personalizzare un livello simbolo 

Per il livello simbolo sono disponibili numerose opzioni di stile. Di seguito è disponibile uno strumento per testare queste opzioni.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello simbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Opzioni del livello simbolo) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando si desidera eseguire il rendering del testo solo con un livello di simbolo, è possibile nascondere l'icona impostando la `image` proprietà delle opzioni dell'icona su `'none'` .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)