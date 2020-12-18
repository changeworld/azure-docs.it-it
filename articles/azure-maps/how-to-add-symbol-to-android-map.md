---
title: Aggiungere un livello di simbolo ad Android Maps | Mappe Microsoft Azure
description: Informazioni su come aggiungere un marcatore a una mappa. Vedere un esempio che usa le mappe di Azure Android SDK per aggiungere un livello di simboli che contiene dati basati su punti da un'origine dati.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679343"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Aggiungere un livello di simbolo (Android SDK)

Questo articolo illustra come eseguire il rendering dei dati punto da un'origine dati come livello di simbolo su una mappa usando Azure Maps Android SDK. I livelli di simbolo eseguono il rendering dei punti come immagine e testo sulla mappa.

> [!TIP]
> Per impostazione predefinita, i livelli simbolo eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità di geometria dei punti, impostare l' `filter` opzione del livello su `eq(geometryType(), "Point")` . Se si desidera includere anche le funzionalità MultiPoint, impostare l' `filter` opzione del livello su `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) . I blocchi di codice in questo articolo possono essere inseriti nel `onReady` gestore eventi maps.

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Prima di poter aggiungere un livello di simbolo alla mappa, è necessario eseguire un paio di passaggi. Prima di tutto, creare un'origine dati e aggiungerla alla mappa. Creare un livello di simbolo. Passare quindi l'origine dati al livello del simbolo per recuperare i dati dall'origine dati. Aggiungere infine i dati nell'origine dati, in modo da eseguire il rendering di un elemento.

Il codice seguente illustra gli elementi da aggiungere alla mappa dopo che è stato caricato. Questo esempio esegue il rendering di un singolo punto sulla mappa usando un livello di simbolo.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Sono disponibili tre tipi diversi di dati punto che è possibile aggiungere alla mappa:

- Geometria del punto GeoJSON: questo oggetto contiene solo una coordinata di un punto e nient'altro. Il `Point.fromLngLat` metodo statico può essere usato per creare facilmente questi oggetti.
- GeoJSON MultiPoint Geometry: questo oggetto contiene le coordinate di più punti e nient'altro. Passare una matrice di punti nella `MultiPoint` classe per creare questi oggetti.
- Funzionalità GeoJSON: questo oggetto è costituito da qualsiasi geometria GeoJSON e da un set di proprietà che contengono i metadati associati alla geometria.

Per ulteriori informazioni, vedere il documento [creare un'origine dati](create-data-source-android-sdk.md) per la creazione e l'aggiunta di dati alla mappa.

L'esempio di codice seguente crea una geometria del punto GeoJSON e la passa alla funzionalità GeoJSON e ha un `title` valore aggiunto alle relative proprietà. La `title` proprietà viene visualizzata come testo sopra l'icona del simbolo sulla mappa.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

Lo screenshot seguente mostra il codice precedente che consente di riportare una funzionalità punto usando un'icona e un'etichetta di testo con un livello di simboli.

![Mappa con il rendering del punto usando un livello di simboli che visualizza un'icona e un'etichetta di testo per una funzionalità punto](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Per impostazione predefinita, i livelli dei simboli ottimizzano il rendering dei simboli nascondendo i simboli che si sovrappongono. Quando si esegue lo zoom avanti, i simboli nascosti diventano visibili. Per disabilitare questa funzionalità ed eseguire il rendering di tutti i simboli in qualsiasi momento, impostare le `iconAllowOverlap` `textAllowOverlap` Opzioni e su `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Aggiungere un'icona personalizzata a un livello simbolo

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Di conseguenza tutte le risorse, ad esempio le immagini icona, devono essere caricate nel contesto di WebGL. In questo esempio viene illustrato come aggiungere un'icona personalizzata alle risorse della mappa. Questa icona viene quindi utilizzata per eseguire il rendering dei dati del punto con un simbolo personalizzato sulla mappa. La proprietà `textField` del livello simbolo richiede che venga specificata un'espressione. In questo caso, si vuole eseguire il rendering della proprietà temperature. Poiché temperature è un numero, è necessario convertirlo in una stringa. Inoltre, si desidera aggiungere "° f". Per eseguire questa concatenazione è possibile utilizzare un'espressione. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

Per questo esempio, l'immagine seguente è stata caricata nella cartella di cui è stato disegnato l'app.

| ![Immagine dell'icona meteo di docce pioggia](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Lo screenshot seguente mostra il codice precedente che consente di riportare una funzionalità punto usando un'icona personalizzata e un'etichetta di testo formattato con un livello di simboli.

![Mappa con il rendering del punto usando un livello di simboli che visualizza un'icona personalizzata e un'etichetta di testo formattato per una funzionalità punto](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Quando si desidera eseguire il rendering del testo solo con un livello di simbolo, è possibile nascondere l'icona impostando la `iconImage` proprietà delle opzioni dell'icona su `"none"` .

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Visualizzare informazioni sulle funzionalità](display-feature-information-android.md)
