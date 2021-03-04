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
ms.openlocfilehash: 1706b60a61bd3b507d9fbcf555e478b388f51168
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047571"
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

## <a name="modify-symbol-colors"></a>Modificare i colori del simbolo

Il Android SDK mappe di Azure viene visualizzato con un set di varianti di colore predefinite dell'icona del marcatore predefinito. Ad esempio, `marker-red` può essere passato nell' `iconImage` opzione di un livello di simbolo per eseguire il rendering di una versione rossa dell'icona del marcatore in tale livello. 

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

La tabella seguente elenca tutti i nomi di immagine icona incorporati disponibili. Tutti questi marcatori eseguono il pull dei colori dalle risorse di colore di cui è possibile eseguire l'override. Oltre a eseguire l'override del colore di riempimento principale del marcatore. Si noti tuttavia che l'override del colore di uno di questi marcatori verrebbe applicato a tutti i livelli che usano l'immagine dell'icona.

| Nome immagine icona | Nome risorsa colore |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

È anche possibile eseguire l'override del colore del bordo di tutti i marcatori usando il `mapcontrol_marker_border` nome della risorsa colore. È possibile eseguire l'override dei colori di questi marcatori aggiungendo un colore con lo stesso nome nel `colors.xml` file dell'app. Il file seguente, ad esempio, `colors.xml` renderebbe il colore verde chiaro del marcatore predefinito.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

Di seguito è riportata una versione modificata del codice XML del vettore del marcatore predefinito che è possibile modificare per creare versioni personalizzate aggiuntive del marcatore predefinito. La versione modificata può essere aggiunta alla `drawable` cartella dell'app e aggiunta allo sprite dell'immagine Maps usando `map.images.add` , quindi usata con un livello di simboli.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

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
