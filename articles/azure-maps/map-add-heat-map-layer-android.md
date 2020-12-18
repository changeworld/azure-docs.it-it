---
title: Aggiungere un livello mappa termica a Maps Android | Mappe Microsoft Azure
description: Informazioni su come creare una mappa termica. Vedere come usare Azure MapsAndroid SDK per aggiungere un livello mappa termica a una mappa. Informazioni su come personalizzare i livelli della mappa termica.
author: rbrundritt
ms.author: richbrun
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 4de59bd0b2a9dc9b11acf55a59b82724d2c7b862
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681606"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Aggiungere un livello mappa termica (Android SDK)

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati. Vengono usati per rappresentare la densità dei dati usando un intervallo di colori e visualizzare i dati "aree sensibili" su una mappa. Le mappe termiche sono un ottimo modo per eseguire il rendering dei set di impostazioni con un numero elevato di punti. 

Il rendering di decine di migliaia di punti come simboli può coprire la maggior parte dell'area della mappa. Questo caso probabilmente comporta la sovrapposizione di molti simboli. Rendendo difficile acquisire una migliore comprensione dei dati. Tuttavia, la visualizzazione di questo stesso set di dati come mappa termica semplifica la visualizzazione della densità e della densità relativa di ogni punto dati.

È possibile usare le mappe termiche in molti scenari diversi, tra cui:

- **Data temperature**: fornisce le approssimazioni per la temperatura tra due punti dati.
- **Dati per i sensori di rumore**: Mostra non solo l'intensità del rumore in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione a distanza. Il livello di disturbo in un sito potrebbe non essere elevato. Se l'area di copertura del rumore da più sensori si sovrappone, è possibile che questa area sovrapposta possa riscontrare livelli di rumore più elevati. Di conseguenza, l'area sovrapposta è visibile nella mappa termica.
- **Traccia GPS**: include la velocità come mappa con altezza ponderata, in cui l'intensità di ogni punto dati è basata sulla velocità. Questa funzionalità, ad esempio, consente di visualizzare la posizione in cui un veicolo è stato velocizzato.

> [!TIP]
> Per impostazione predefinita, i livelli mappa termica eseguono il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità di geometria dei punti, impostare l' `filter` opzione del livello su `eq(geometryType(), "Point")` . Se si desidera includere anche le funzionalità MultiPoint, impostare l' `filter` opzione del livello su `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) . I blocchi di codice in questo articolo possono essere inseriti nel `onReady` gestore eventi maps.

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come mappa termica, passare l'origine dati in un'istanza della `HeatMapLayer` classe e aggiungerla alla mappa.

L'esempio di codice seguente carica un feed GeoJSON di terremoti della settimana precedente e li esegue il rendering come mappa termica. Viene eseguito il rendering di ogni punto dati con un raggio di 10 pixel a tutti i livelli di zoom. Per garantire un'esperienza utente migliore, la mappa termica si trova al di sotto del livello etichetta, in modo che le etichette risultino chiaramente visibili. I dati in questo esempio sono del [programma USGS Earthquake Hazards](https://earthquake.usgs.gov/). Questo esempio carica i dati GeoJSON dal Web usando il blocco di codice dell'utilità di importazione dati disponibile nel documento [creare un'origine dati](create-data-source-android-sdk.md) .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Lo screenshot seguente mostra una mappa che carica una mappa termica usando il codice precedente.

![Mappa con il livello mappa termica dei terremoti recenti](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Personalizzare il livello mappa termica

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello mappa termica offre diverse opzioni per la personalizzazione, tra cui:

- `heatmapRadius`: Definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. È possibile impostare il raggio come numero fisso o come espressione. Utilizzando un'espressione, è possibile ridimensionare il raggio in base al livello di zoom e rappresentare un'area spaziale coerente sulla mappa, ad esempio un raggio di 5 chilometri.
- `heatmapColor`: Specifica la modalità di colorazione della mappa termica. Una sfumatura di colore è una funzionalità comune delle mappe termiche. È possibile ottenere l'effetto con un' `interpolate` espressione. È anche possibile usare un' `step` espressione per colorare la mappa termica, suddividendo in modo visivo la densità in intervalli simili a una mappa di contorno o di stile radar. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo.

  È possibile specificare i valori dei colori per le mappe termiche come espressione sul `heatmapDensity` valore. Il colore dell'area in cui non sono stati definiti dati in corrispondenza dell'indice 0 dell'espressione "interpolazione" o del colore predefinito di un'espressione "con rientri". È possibile utilizzare questo valore per definire un colore di sfondo. Spesso, questo valore è impostato su Transparent o su un nero semi-trasparente. 

  Di seguito sono riportati alcuni esempi di espressioni colori:

  | Espressione colore interpolazione | Espressione colore con rientri |
  |--------------------------------|--------------------------|
  | interpolare<br/>&nbsp;&nbsp;&nbsp;&nbsp;lineare (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0, color. Transparent),<br/>&nbsp;&nbsp;&nbsp;&nbsp;arresta (0,01, colore (Color. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0.5, color (parseColor ("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, color (parseColor ("#00c3ff")))<br/>)` | passo<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Color (Color. Transparent),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,01, color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,25, color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0.5, color (Color. GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,5, color. YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, color (Color. RED))<br/>) |

- `heatmapOpacity`: Specifica come opaco o trasparente il livello mappa termica.
- `heatmapIntensity`: Applica un moltiplicatore al peso di ogni punto dati per aumentare l'intensità complessiva del mappa termica. Causa una differenza nel peso dei punti dati, semplificando la visualizzazione.
- `heatmapWeight`: Per impostazione predefinita, tutti i punti dati hanno un peso di 1 e sono ponderati equamente. L'opzione Weight funge da moltiplicatore ed è possibile impostarla come numero o espressione. Se un numero è impostato come peso, è l'equivalenza di posizionare due volte ogni punto dati sulla mappa. Ad esempio, se il peso è `2` , la densità raddoppia. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità.

  Tuttavia, se si utilizza un'espressione, il peso di ogni punto dati può essere basato sulle proprietà di ogni punto dati. Si supponga, ad esempio, che ogni punto dati rappresenti un terremoto. Il valore magnitude è un'importante metrica per ogni punto dati sismico. I terremoti si verificano sempre, ma hanno una grandezza bassa e non vengono rilevati. Usare il valore magnitude in un'espressione per assegnare il peso a ogni punto dati. Usando il valore Magnitude per assegnare il peso, si ottiene una rappresentazione migliore dell'importanza dei terremoti all'interno della mappa termica.
- `minZoom` e `maxZoom` : intervallo del livello di zoom in cui deve essere visualizzato il livello.
- `filter`: Espressione di filtro utilizzata per limitare l'oggetto recuperato dall'origine e di cui è stato eseguito il rendering nel livello.
- `sourceLayer`: Se l'origine dati connessa al livello è un'origine del riquadro vettoriale, è necessario specificare un livello di origine all'interno dei riquadri vettoriali.
- `visible`: Consente di nascondere o visualizzare il livello.

Di seguito è riportato un esempio di mappa termica in cui viene usata un'espressione di interpolazione Liner per creare una sfumatura di colore uniforme. La `mag` proprietà definita nei dati viene utilizzata con un'interpolazione esponenziale per impostare il peso o la pertinenza di ogni punto dati.

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

La schermata seguente mostra il livello mappa termica personalizzato sopra riportato usando gli stessi dati dell'esempio precedente della mappa termica.

![Mappa con un livello mappa termica personalizzato dei terremoti recenti](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Mappa termica Zoomable coerente

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Quando si esegue lo zoom della mappa, i dati vengono aggregati insieme e il livello mappa termica è diverso. Il video seguente illustra il comportamento predefinito della mappa termica in cui gestisce un raggio di pixel quando si esegue lo zoom della mappa.

![Animazione che mostra una mappa che esegue lo zoom con un livello mappa termica che Mostra dimensioni in pixel coerenti](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Utilizzare un' `zoom` espressione per ridimensionare il raggio per ogni livello di zoom, in modo che ogni punto dati copra la stessa area fisica della mappa. Questa espressione rende il livello mappa termica più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente.

Il ridimensionamento del raggio in modo che raddoppi a ogni livello di zoom crea una mappa termica che sembra coerente in tutti i livelli di zoom. Per applicare questa scala, utilizzare `zoom` con un'espressione base 2 `exponential interpolation` , con il raggio di pixel impostato per il livello di zoom minimo e un raggio scalato per il livello di zoom massimo calcolato come `2 * Math.pow(2, minZoom - maxZoom)` illustrato nell'esempio seguente. Eseguire lo zoom della mappa per vedere come la mappa termica viene ridimensionata con il livello di zoom.

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

Il video seguente mostra una mappa che esegue il codice precedente, che consente di ridimensionare il raggio mentre viene eseguita lo zoom della mappa per creare un rendering coerente della mappa termica nei livelli di zoom.

![Animazione che mostra una mappa che esegue lo zoom con un livello mappa termica che mostra una dimensione geospaziale coerente](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-android-sdk.md)
